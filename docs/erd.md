# ERD

[메인 README](../README.md) · [주요 프로시저](procedures.md) · [수집 데이터와 전처리](data-collection-and-preprocessing.md)

기준: Flyway migration V1~V33 (2026-09-14, `main`). MySQL이 원본이며 Elasticsearch·Qdrant는 파생 색인, Redis는 임시 보관입니다.
그림은 핵심 열만 표시하고, 관계는 migration의 FK와 참조 열 기준입니다. 공고를 참조하는 일부 테이블은 FK 없이
복합 ID(`source_code`, `source_program_id`)로 연결되며 점선으로 표시했습니다.

## 1. 공고 · 계정 · 기업

```mermaid
erDiagram
    support_program {
        bigint id PK
        varchar source_code
        varchar source_program_id
        varchar title
        text summary
        json categories
        json regions
        date application_start_date
        date application_end_date
        boolean is_source_present
    }
    support_program_source_document {
        varchar source_code PK
        varchar source_program_id PK
        mediumtext content
        char content_hash
        datetime fetched_at
    }
    support_program_sync_status {
        varchar source_code PK
        bigint published_generation
        char published_catalog_fingerprint
        int published_program_count
    }
    account {
        bigint id PK
        varchar email UK
        varchar password_hash
        varchar role
        datetime email_verified_at
        datetime suspended_at
        datetime deleted_at
    }
    account_session {
        bigint id PK
        char token_hash UK
        bigint account_id FK
        datetime expires_at
    }
    account_oauth_identity {
        bigint id PK
        bigint account_id FK
        varchar provider
        varchar subject
    }
    company {
        bigint id PK
        bigint account_id FK
        char business_number
        varchar company_name
        varchar region
        varchar industry
        smallint founded_year
    }
    company_partner_profile {
        bigint company_id PK
        json roles
        json interest_areas
        json capabilities
    }
    saved_support_program {
        bigint id PK
        bigint account_id FK
        bigint support_program_id FK
        datetime saved_at
    }
    chat_conversation {
        bigint id PK
        bigint account_id FK
        varchar conversation_id
        json snapshot
    }

    support_program ||--o| support_program_source_document : "원문 (질문 시 수집)"
    account ||--o{ account_session : ""
    account ||--o{ account_oauth_identity : ""
    account ||--o| company : ""
    company ||--o| company_partner_profile : ""
    account ||--o{ saved_support_program : ""
    support_program ||--o{ saved_support_program : ""
    account ||--o{ chat_conversation : ""
```

`account_password_reset`, `signup_email_verification`, `account_admin_action`, `account_oauth_unlink_job`은 계정 보조 테이블입니다(토큰·인증번호 해시, 관리자 조치 기록, 탈퇴 연결 해제 작업).

## 2. 파트너 · 리포트 · 검토 · 신청 문서

```mermaid
erDiagram
    partner_recruitment {
        bigint id PK
        bigint account_id FK
        bigint company_id FK
        bigint support_program_id FK
        varchar title
        varchar seeking_role
        date recruitment_deadline
        datetime closed_at
    }
    partner_proposal {
        bigint id PK
        bigint recruitment_id FK
        bigint proposer_account_id FK
        varchar decision
        datetime withdrawn_at
    }
    daily_report_subscription {
        bigint account_id PK
        varchar support_purpose
        boolean enabled
        varchar confirmed_email
    }
    daily_report {
        bigint id PK
        bigint account_id FK
        date report_date
        varchar status
        varchar delivery_status
        json content_json
    }
    daily_report_generation_job {
        bigint id PK
        bigint report_id FK
        varchar status
        datetime next_publish_at
    }
    combination_review {
        bigint id PK
        bigint owner_account_id FK
        varchar title
        bigint input_revision
    }
    combination_review_program {
        bigint review_id FK
        varchar source_code
        varchar source_program_id
        varchar execution_status
    }
    combination_review_run {
        bigint id PK
        bigint review_id FK
        char request_key
        varchar status
        json analysis_json
    }
    application_preparation {
        bigint id PK
        bigint owner_account_id FK
        varchar source_code
        varchar source_program_id
        varchar form_version_id
        varchar progress_stage
    }
    application_preparation_fact {
        bigint id PK
        bigint preparation_id FK
        varchar field_key
        varchar fact_status
        text value_text
    }
    application_form_snapshot {
        varchar form_version_id PK
        varchar source_code
        varchar source_program_id
        json manifest_json
        varchar parser_version
    }
    application_form_discovery_job {
        bigint id PK
        bigint owner_account_id FK
        char request_key
        varchar status
        json result_json
    }

    partner_recruitment ||--o{ partner_proposal : ""
    daily_report_subscription ||--o{ daily_report : "계정별"
    daily_report ||--o| daily_report_generation_job : "Outbox"
    combination_review ||--|{ combination_review_program : "2건 이상"
    combination_review ||--o{ combination_review_run : "실행 = Outbox"
    application_preparation ||--o{ application_preparation_fact : ""
    application_preparation }o..o| application_form_snapshot : "form_version_id"
    application_preparation }o..o| application_form_discovery_job : "복합 ID"
```

`combination_review_run_source`(첨부 원본 바이트)와 `application_preparation_interpretation_run`(AI 해석 실행 스냅샷)은 각 실행의 하위 테이블입니다.
`daily_report_generation_budget`은 날짜별 생성 시도 예산입니다.

## 3. MySQL 밖의 저장 구조

| 저장소 | 단위 | 키 · 내용 | 수명 |
|---|---|---|---|
| Elasticsearch `support-program-lexical-v2` | 공고 1건 = 문서 1개 | `id`(복합 ID) · `contentHash` · `sortTimestamp` · `text`(Nori 분석) | 동기화마다 새 버전 색인 후 전환, MySQL 지문과 대조 |
| Qdrant 공고 컬렉션 | 공고 1건 = 벡터 1개(1,536차원) | UUID = f(복합 ID, 내용 해시), payload에 ID·해시·제공처 | 해시가 바뀐 공고만 갱신 |
| Qdrant evidence 컬렉션 | 원문 청크 1개 = 벡터 1개 | 청크 ID = SHA-256(문서 ID + 문서 해시 + 순서), payload에 문서 ID·순서 | 원문 해시가 바뀔 때 재색인 |
| Redis | 비회원 검색 1회 | 검색 결과·조건·소유 계정 | 30분 TTL, 로그인 후 복원 시 삭제 |
| RabbitMQ quorum queue | 작업 1건 | 리포트 생성 · 메일 발송 · 중복 검토 · 첨부 분석 · 카카오 연결 해제 | 상태·재시도는 MySQL 작업 행(Outbox)이 기준 |

## 4. 설계 원칙

- **원본은 하나**: 공고는 `(source_code, source_program_id)` 고유키로 식별하고 색인 식별자도 같은 값을 씁니다.
- **삭제하지 않는다**: 원본에서 사라진 공고는 `is_source_present=false`, 탈퇴 계정은 `deleted_at`, 리포트·검토는 상태 열로 표현합니다.
- **실행 행이 Outbox**: 백그라운드 작업은 HTTP 접수 시 MySQL 행을 먼저 만들고 큐에 전달합니다. 상태 전이로 중복 실행을 막습니다.
- **AI 실행은 스냅샷으로 보존**: 입력·출력·모델·프롬프트 버전을 JSON으로 저장해 나중에 재현·검수할 수 있습니다.
