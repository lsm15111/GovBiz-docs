# 기능 흐름도 · 아키텍처 구조도

[메인 README](../README.md) · [서비스 단계](service-stage.md) · [주요 프로시저](procedures.md) · [ERD](erd.md)

## 1. 시스템 구성도

<p align="center">
  <img src="assets/architecture/govbiz-architecture.png" alt="GovBiz 시스템 아키텍처: React Web → Spring Boot Core API → FastAPI AI Service, MySQL·Elasticsearch·Qdrant·Redis·RabbitMQ, 공고 제공처 API와 OpenAI" width="900">
</p>

```mermaid
flowchart LR
    Web["React Web"] --> Core["Spring Boot Core API"]
    Core --> DB[("MySQL<br/>공고 카탈로그 · 계정 · 작업 상태")]
    Core --> ES[("Elasticsearch<br/>Nori BM25 색인")]
    Core --> Redis[("Redis<br/>비회원 결과 복원")]
    Core <--> MQ["RabbitMQ<br/>리포트 · 메일 · 검토 · 첨부 분석"]
    Core --> AI["FastAPI AI Service"]
    AI --> Q[("Qdrant<br/>공고 · 원문 청크 벡터")]
    AI --> OpenAI["OpenAI<br/>임베딩 · 점수화 · 근거 답변"]
    Sync["Core 백그라운드 동기화"] --> Src["기업마당 · K-Startup<br/>과기정통부 · 충남 API"]
    Sync --> ES
    Sync --> AI
    Sync --> DB
    Core --> Html["기업마당 공식 상세 HTML<br/>(명시적 질문 시)"]
```

| 구성 요소 | 책임 | 하지 않는 것 |
|---|---|---|
| React Web | 화면, 대화 상태, 로그인 여부에 따른 라우팅 | 외부 API·AI Service 직접 호출 |
| Core API | 공개 HTTP 계약, 공고 수집·정규화·색인 동기화, 후보 결합(RRF), 인용 재검증, 계정·기업·큐 소비자 | 모델 호출 |
| AI Service | 임베딩, Qdrant 검색, 조건 해석·후보 점수화·근거 답변·문항 발견 Agent | 브라우저 공개, DB 직접 접근 |
| MySQL | 원본 데이터, 작업 상태(Outbox), AI 실행 스냅샷 | — |
| Elasticsearch · Qdrant | 재생성 가능한 파생 색인 | 원본 역할 |
| Redis | 비회원 검색 결과 30분 보관 | AI 캐시, 세션 저장 |
| RabbitMQ | 백그라운드 작업 전달 | 상태의 진실 원천(MySQL이 담당) |

## 2. 기능 흐름도 — AI 대화 검색 1회

```mermaid
sequenceDiagram
    autonumber
    actor U as 사용자
    participant W as Web
    participant C as Core API
    participant ES as Elasticsearch
    participant AI as AI Service
    participant Q as Qdrant
    participant O as OpenAI
    U->>W: 서울 AI 창업지원 사업 찾아줘
    W->>C: POST 검색 (검색문 + 확인된 조건)
    C->>AI: 조건 해석
    AI->>O: 구조화 출력
    C->>ES: 키워드 후보 20 (Nori BM25)
    C->>AI: 의미 후보 20
    AI->>O: 질의 임베딩 (캐시)
    AI->>Q: 벡터 검색
    C->>C: RRF 결합 (k=60)
    C->>AI: 후보 점수화
    AI->>O: 관련도 · 자격 판정 · 인용
    AI-->>C: 상위 5건 + 이유 + 원문 인용
    C-->>W: 추천 결과 (관련도와 자격은 별도 표시)
```

## 3. 기능 흐름도 — 원문 근거 질문

```mermaid
sequenceDiagram
    autonumber
    actor U as 사용자
    participant W as Web
    participant C as Core API
    participant B as 기업마당 HTML
    participant AI as AI Service
    participant Q as Qdrant
    participant O as OpenAI
    U->>W: 공고 상세에서 "지원 대상이 누구야?"
    W->>C: POST 원문 질문
    C->>B: 공식 상세 HTML 수집 (제목 일치 검증)
    C->>C: 본문 추출 → 최대 1,500자 × 50청크
    C->>AI: 청크 색인 (해시가 바뀐 경우만)
    AI->>Q: evidence 컬렉션 upsert
    C->>AI: 질문 + 해당 공고 청크 상위 5
    AI->>O: 답변 + 청크 번호 선택
    AI-->>C: 답변 + 번호 → ID 복원
    C->>C: 인용 ⊆ 검색 집합 재검증
    C-->>W: 답변 + 인용 청크 + 원문 URL (부족하면 INSUFFICIENT_EVIDENCE)
```

## 4. 기능 흐름도 — 백그라운드 작업 (리포트 · 중복 검토 · 첨부 분석)

```mermaid
flowchart LR
    R["HTTP 접수<br/>202 + 작업 ID"] --> O["MySQL 작업 행 생성<br/>(Outbox, 상태 PENDING)"]
    O --> P["RabbitMQ 발행"]
    P --> Cs["Core 내부 소비자"]
    Cs --> S{"상태 전이<br/>PENDING → RUNNING"}
    S -- 이미 실행 중 --> Skip["중복 실행 차단"]
    S -- 성공 --> Run["첨부 수집 · 파싱 · AI 실행"]
    Run --> Done["SUCCEEDED / FAILED"]
    Run -. 응답 불명 .-> Unk["UNKNOWN<br/>자동 재호출 안 함"]
    Poll["화면 상태 조회"] --> O
```

## 5. 계층 구조

| 서비스 | 계층 | 규칙 |
|---|---|---|
| Frontend | `presentation`(View · ViewModel) → `domain`(UseCase · Entity) → `data`(Repository · HTTP) | 의존 방향은 안쪽으로만, Awilix DI, 상태는 Redux Toolkit |
| Core API | `controller` → `facade` → `service` → `repository`(MyBatis XML) | 외부 통신은 `client`, 기능별 패키지, Flyway migration은 수정 금지·추가만 |
| AI Service | `router` → `service` → `agent`(구체 클래스) → OpenAI | typed Agent + `Runner.run(max_turns=1)`, 출력 계약 위반은 거부 |

상세: [아키텍처 README](architecture/README.md) · [호출·데이터 흐름](architecture.md) · [이미지 원본](assets/architecture/README.md)
