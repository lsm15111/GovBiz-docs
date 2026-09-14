# 문서 안내

[메인 README](../README.md)로 돌아가기

메인 README는 팀 소개 · 프로젝트 개요 · 기술 스택 · 인덱싱 단계 · 화면 설계 · 한 줄 회고만 담습니다. 나머지는 아래 문서에서 관리합니다.

## 따로 정리한 문서

| 문서 | 내용 |
|---|---|
| [ERD](erd.md) | MySQL 29개 테이블의 관계(mermaid erDiagram 2장), Elasticsearch·Qdrant·Redis·RabbitMQ 저장 구조, 설계 원칙 |
| [주요 프로시저](procedures.md) | 동기화 · AI 검색 · 필터 검색 · 원문 질문 · 신청 문서 · 중복 검토 · 리포트 · 결과 복원 8개의 단계별 처리와 실패 시 동작 |
| [GitHub Flow 사용 여부](github-flow.md) | 브랜치·커밋·PR·squash merge 규칙, CI 조건, 기여 현황, 겪은 문제 |
| [기능 흐름도 · 아키텍처 구조도](architecture-overview.md) | 시스템 구성도(이미지 + mermaid), 검색·원문 질문·백그라운드 작업 흐름도, 구성 요소별 책임, 계층 구조 |
| [서비스 단계](service-stage.md) | 시작 시 준비, 요청당 파이프라인과 시간 예산, 단계별 실측, 요청 제한, LangChain 개념 대응 |
| [검색 성능 개선](search-performance.md) | top-k 근거, Hybrid Search(Nori v1→v2 지표), 런타임 최적화, 캐싱 효과와 한계 |
| [향후 개선 계획](roadmap.md) | 다음 작업 7개의 근거·완료 기준, 모델 고도화로 풀 수 있는 것, 적용처 |

## 과제 산출물

| 산출물 | 문서 |
|---|---|
| 수집 데이터 및 전처리 | [수집 데이터와 전처리](data-collection-and-preprocessing.md) |
| 시스템 아키텍처 | [기능 흐름도 · 아키텍처 구조도](architecture-overview.md) · [아키텍처 README](architecture/README.md) · [호출·데이터 흐름](architecture.md) |
| RAG · 벡터 DB 연동 코드 | [AI Service 개발](../backend/ai-service/README.md) · [Core API 개발](../backend/core-api/README.md) |
| 테스트 계획 및 결과 보고서 | [테스트 계획과 결과](test-plan-and-results.md) |
| 트러블슈팅 | [트러블슈팅 기록](troubleshooting.md) |
| README 작성 틀 | [README 기본 틀](readme-template.md) |

## 저장소에 이미 있는 상세 문서

기능별 API 계약(`*-contract.md`), Elasticsearch·Redis·RabbitMQ 적용 상세, 평가 실행 기록, 설계 제안 문서는 기존 `docs/` 목록을 그대로 사용합니다.
이 폴더의 새 문서는 기존 문서를 대체하지 않고 요약·진입점 역할을 합니다.
