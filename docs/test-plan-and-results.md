# 테스트 계획과 결과 보고서

[메인 README](../README.md) · [문서 목록](README.md) · [트러블슈팅](troubleshooting.md) · [구현 현황](implementation-status.md)

기준일: 2026-09-14 (`main` `d7df4598`). 무엇을 어떤 계층에서 검증하는지, 최신 결과가 얼마인지, 어떻게 재현하는지 정리합니다.
테스트 통과는 계약·회귀의 재현성을 뜻하며 실제 추천 정확도나 운영 품질을 보증하지 않습니다. 수치는 기록된 문서의 값을 옮겼고
제출 전에 같은 명령으로 다시 계산해야 합니다.

## 1. 계획: 무엇을 어디서 검증하나

| 계층 | 도구 | 대상 | 외부 호출 |
|---|---|---|---|
| 단위·계약 | Vitest + Testing Library / JUnit 5 / pytest | 화면·ViewModel, Service·Facade·Mapper, Agent 출력 계약과 검증 규칙 | 없음 |
| DB·검색 통합 | Testcontainers MySQL 8.4 · 실제 Elasticsearch 9.5.3 + Nori | Flyway 적용, MyBatis SQL, 키워드 색인·후보 검색 | 없음 |
| 컨테이너 통합 | 격리 Docker Compose + 로컬 스텁(기업마당·K-Startup·OpenAI) | 4개 제공처 동기화 → 검색 → 장애·복구 | 유료 API 0회 |
| 품질 평가 | 고정 스냅샷·질문·판정 (`evaluation/`) | 키워드 후보 검색, 실제 AI 검색, 근거 답변, 도우미 의도 분류 | 실측 시에만 OpenAI |
| CI | GitHub Actions `ci.yml` (Frontend · Core API · AI Service 3 job) | 위 자동 테스트 + 평가 도구·공유 평가 자료 검증 | 없음 |

CI는 모델을 호출하지 않으며 공유된 평가 결과의 재계산까지 검증합니다.
[CI 정의](../.github/workflows/ci.yml) · [Elasticsearch 독립 실험 워크플로](../.github/workflows/elasticsearch-experiment.yml)

## 2. 결과: 자동 테스트

| 서비스 | 최신 결과 | 기록일 | 출처 |
|---|---|---|---|
| Core API | **1,261건 통과**, 실패·오류·건너뜀 0. 실제 ES·Nori 통합 9건, MySQL Flyway 검증 포함 | 2026-09-13 | [Elasticsearch 적용 상세](elasticsearch-lexical-search.md#2026-09-13-v2-검증-결과) |
| Frontend | **91개 파일 1,102건 통과**, `pnpm lint`·`pnpm build` 통과 | 2026-09-13 | [리포트 메일 발송](rabbitmq-daily-report-delivery.md) |
| AI Service | **941건 통과** | 2026-09-12 | [중복 검토 분석](rabbitmq-combination-review.md) |
| 인프라 스크립트 | 25건 통과 | 2026-09-13 | 위 Elasticsearch 문서 |
| 평가 도구 | 검색 평가 139건(실제 ES 통합 포함) · 검토 도구 108건 · 근거 평가 118건 | 2026-09-13 / 09-07 | 위 문서 · [RAG 검수](../evaluation/support-program-evidence/README.md) |

세 서비스 합계 약 3,300건입니다. 테스트 파일 수는 Frontend 92 · Core 157 · AI Service 34입니다.

## 3. 결과: 사용자 시나리오

사용자 관점에서 기능이 동작하는지 확인한 시나리오입니다. "확인 위치"는 해당 계약·테스트·실측 기록이 있는 문서입니다.

| ID | 기능 | 입력 | 기대 결과 | 확인 위치 |
|---|---|---|---|---|
| TC-01 | AI 대화 검색 | `서울 AI 창업지원 사업 찾아줘` | 추천 공고 최대 5건, 관련도 점수·추천 이유, 자격 확인은 별도 표시 | [검색 계약](support-program-search-contract.md) · [실제 캡처](../evaluation/support-program-search/runs/support-program-catalog-20260906-v1/README.md) |
| TC-02 | 후속 대화 조건 변경 | 결과 후 `부산으로 바꿔줘` | 변경 제안 확인 질문 → 사용자 확인 후 재검색 | [대화 조건 갱신](conversation-condition-update.md) |
| TC-03 | 필터 검색 | 지역 `서울` + 분야 + 접수 중 | 조건 AND 일치 목록, 최신·마감순 정렬, 페이지 | [직접 조건 검색](support-program-catalog.md) |
| TC-04 | 공고 상세 | 공고 선택 | 신청 기간·접수 상태(`OPEN`/`UPCOMING`/`CLOSED`/`UNKNOWN`)·출처 URL | [데이터와 접수 상태](architecture.md#데이터와-접수-상태) |
| TC-05 | 원문 근거 질문 | 기업마당 공고에서 `지원 대상이 누구야?` | 답변 + 원문 인용 청크. 근거 부족 시 `INSUFFICIENT_EVIDENCE` 안내 | [RAG 검수·답변 평가](../evaluation/support-program-evidence/README.md) |
| TC-06 | 비회원 검색 후 로그인 | 검색 → 로그인 | 30분 안에 검색 결과·조건 복원 | [Redis 적용 상세](redis-search-result-restoration.md) |
| TC-07 | 회원가입·로그인 | 이메일 인증번호 → 가입 → 로그인 | 인증한 이메일만 가입, 세션 절대·유휴 만료, 정지 계정 403 | [계정·인증 계약](account-auth-contract.md) |
| TC-08 | 소셜 로그인·탈퇴 | 카카오/Google 로그인 → 탈퇴 | 탈퇴 시 연결 해제 작업 큐 적재, 재가입 충돌 방지 | [카카오 연결 해제](rabbitmq-account-oauth-unlink.md) |
| TC-09 | 기업 등록 | 사업자등록번호 입력 | Bizno 조회로 상호·상태 확인 후 프로필 저장 | [계정·인증 계약](account-auth-contract.md) |
| TC-10 | 관심 공고·달력 | 공고 저장 | 관심 공고함 목록·달력에 시작·마감 표시, 신청 준비·중복 검토 연동 | [관심 공고 달력](saved-programs-calendar.md) |
| TC-11 | 신청 문서 준비 | 공고 선택 → 첨부 분석 → 문항 답변 | 백그라운드 분석 상태 조회, 문항별 원문 인용, HWP/HWPX/PDF 기입 다운로드 | [신청 문서 설계](application-preparation-design.md) · [공식 문서 분석 큐](rabbitmq-application-form-discovery.md) |
| TC-12 | 중복 지원·수혜 검토 | 공고 2건 선택 → 실행 | 202 접수 → 실행 상태 자동 조회 → 결과·근거 이력 | [중복 검토 설계](duplicate-support-review-design.md) · [비동기 분석](rabbitmq-combination-review.md) |
| TC-13 | 기업 맞춤 리포트 | 리포트 설정·미리보기 | 저장 기업 조건 기반 최대 3건 추천, 메일 수신 동의·해지 | [기업 맞춤 일일 리포트](daily-reports.md) |
| TC-14 | 파트너 모집·제안 | 모집글 작성 → 제안 → 수락 | 모집·수정·마감, 제안 수락·거절·철회 | [Frontend 화면 안내](../frontend/README.md) |
| TC-15 | 관리자 회원 관리 | 계정 검색 → 정지 | 정지·해제, 강제 로그아웃, 마지막 관리자 탈퇴 방지 | [Frontend 화면 안내](../frontend/README.md) |
| TC-16 | 도우미 자유 질문 | `관심 공고 어떻게 저장해?` | 여섯 의도 중 하나로 분류, 도움말 인용은 전달한 항목 안에서만 | [도우미 평가](../evaluation/assistant/README.md) |
| TC-17 | 요청량 제한 | 한도 초과 요청 | 429/503 계약, 동시 실행 제한 | [요청량·동시 실행 제한](support-program-request-limits.md) |

## 4. 결과: 장애·복구 (격리 Compose, 2026-09-13)

| 시나리오 | 결과 |
|---|---|
| 4개 제공처 테스트 공고 42건 동기화 → 새 v2 색인 → 검색 | 통과 |
| Elasticsearch 중단 | 검색은 명시적 503, MySQL 목록 조회는 보존 |
| Elasticsearch 영속 볼륨 재생성 | 재색인 후 검색 복구 |
| Redis · RabbitMQ · Qdrant · AI Service 각각 중단·복구 | 기존 장애 복구 검증 통과 |
| OpenAI | 로컬 스텁만 사용, 유료 호출 0회 |

검증용 컨테이너·볼륨은 정리했으며 기존 개발 Docker에 배포한 것은 아닙니다. [검증 기록](elasticsearch-lexical-search.md#2026-09-13-v2-검증-결과)

## 5. 결과: 검색·RAG 품질과 지연

| 평가 | 조건 | 결과 | 해석 |
|---|---|---|---|
| 키워드 후보 검색 Nori v1 → v2 | 2026-09-06 스냅샷 1,422건, 질문 300개, 모델 호출 0회 | Hit@1 258 → **268**, Hit@5 292 → 295, Hit@20 295 → **299**, MRR@20 0.9061 → **0.9338** | 후보 20개 안에 목표 공고가 거의 항상 들어옴. 순위 하락 9개도 보고서에 기록 |
| 실제 AI 검색 (3단계 기준선) | 질문 16개 중 평가 가능 6개, 양성 2개 | 후보 Recall@20 0.50 / 최종 MRR@5 0.50 | 표본이 너무 작아 **합격 판정이 아님**. 4단계 2차에서 dev 무관 추천 6 → 4건, 관련 추천 15 → 16건 |
| 원문 근거 답변 | 공식 HTML 6건 전체 경로 + 가상 6건 | 의미 일치 5·부분 1 → 프롬프트 보완 후 12건 일치 | 공고당 청크 1개 사례가 많아 다중 청크 검색 품질은 미평가 |
| 도우미 의도 분류 | 가상 질문 50개 | luna/none 50/50 (평균 1,967ms) · nano/low 48/50 (3,175ms) | 분류·인용 회귀 확인용. 답변 문장 품질은 측정 안 함 |
| 검색 지연 | 같은 검색 2회 | 첫 검색 42.467초 (랭킹 37.236초) → 캐시 적중 **0.484초** | 병목은 LLM 랭킹. Fast 처리는 중앙값 41.1 → 27.2초, 단가 2배 |

상세: [Nori v2 비교](../evaluation/support-program-search/runs/lexical-v2-20260913-v1/README.md) ·
[실데이터 평가](../evaluation/support-program-search/runs/support-program-catalog-20260906-v1/README.md) ·
[RAG 검수](../evaluation/support-program-evidence/README.md) · [검색 지연](search-latency-20260908.md)

## 6. 재현 방법

저장소 루트에서 실행합니다. 아래 명령은 모델 API를 호출하지 않습니다.

```bash
# Frontend (Node 24 · pnpm 11.22)
cd frontend && VITE_CORE_API_BASE_URL=http://localhost:8080 pnpm test --maxWorkers=2 && pnpm lint && pnpm build
```

```bash
# Core API (JDK 21 · Docker 필요: Testcontainers MySQL, 실제 ES 통합)
cd backend/core-api && ./gradlew clean build --no-daemon
```

```bash
# AI Service (Python 3.11 · uv)
cd backend/ai-service && uv sync --locked --extra dev && uv run --locked --extra dev python -m pytest
```

```bash
# 공유 평가 자료 재검증 (Python 3.11 이상, 추가 패키지 없음)
python -B evaluation/support-program-search/review/verify-shared-run.py --run-dir evaluation/support-program-search/runs/support-program-catalog-20260906-v1 --with-capture
```

실제 모델로 다시 측정하려면 각 평가 README의 `--live` 옵션과 비용 안내를 따릅니다.

## 7. 검증하지 않은 범위

- 운영 환경의 동시 부하·상시 지연·실제 배포(Vercel + AWS) 검증. 현재 Compose는 개발용입니다.
- 사람 검증 정답에 기반한 추천 정확도. 모든 판정 자료는 AI 작성입니다.
- 이메일 실제 전달, 소셜 로그인 실계정, K-Startup·과기정통부·충남 공고의 독립 품질 비교.
- 세 브라우저 호환성, 접근성 자동 검사.
