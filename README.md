# GovBiz-docs

## 팀 소개
| 이름 | 역할 | GitHub |
|--- | --- | --- |
| 김건우 | 역할 | ![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white) |
| 김동섭 | 역할 | ![GitHub](https://img.shields.io/badge/GitHub-white?style=social&logo=github) |
| 송승재 | 역할 | ![GitHub](https://img.shields.io/badge/GitHub-f2f2f2?style=flat-square&logo=github&logoColor=181717) |
| 이성민 | 역할 | ![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)|
| 홍지윤 | 역할 | ![GitHub](https://img.shields.io/badge/GitHub-e8e8e8?style=for-the-badge&logo=github&logoColor=24292e) |


## 프로젝트 개요(차별화 전략)
정부지원사업 검색부터 신청 준비와 기업 간 협업까지 돕는 AI 웹 서비스입니다.
기업마당·K-Startup 등 공식 공고를 기반으로 지원사업 추천, 원문 근거 확인, 신청 정보 정리와 협업 모집·제안을 지원합니다.

### 차별화 전략
- **AI 대화 검색**: 키워드가 아니라 회사 상황을 대화로 설명하면 조건을 반영해 추천
- **원문 근거 인용**: 추천 이유·자격 여부를 공식 공고 원문의 실제 문장으로 인용해 신뢰도 확보
- **중복 지원·수혜 검토**: 이미 받은 지원과 겹치는 공고인지 자동 분석
- **파트너 모집**: 공고 단위로 협업 파트너를 모집·제안할 수 있는 기능까지 포함


## 🛠️기술스택
> Frontend
![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)
![TypeScript](https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge&logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/vite-%23646CFF.svg?style=for-the-badge&logo=vite&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/tailwindcss-%2338B2AC.svg?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Redux](https://img.shields.io/badge/redux-%23593d88.svg?style=for-the-badge&logo=redux&logoColor=white)
![Zod](https://img.shields.io/badge/zod-%233068b7.svg?style=for-the-badge&logo=zod&logoColor=white)
![NodeJS](https://img.shields.io/badge/node.js-%236DA55F.svg?style=for-the-badge&logo=node.js&logoColor=white)

> Backend (Core API)
![Kotlin](https://img.shields.io/badge/kotlin-%237F52FF.svg?style=for-the-badge&logo=kotlin&logoColor=white)
![Spring](https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white)
- MyBatis 4.0.0, Flyway (스키마 버전 관리)
- jsoup 1.23.2 (원문 파싱)
- spring-security-crypto (BCrypt 비밀번호 해시)

> AI Service
![Python](https://img.shields.io/badge/python-%233670A0.svg?style=for-the-badge&logo=python&logoColor=ffdd54)
![FastAPI](https://img.shields.io/badge/fastapi-%23009688.svg?style=for-the-badge&logo=fastapi&logoColor=white)
![OpenAI](https://img.shields.io/badge/openai-%23412991.svg?style=for-the-badge&logo=openai&logoColor=white)
- OpenAI SDK 3.x, Agents SDK 0.22.x, tiktoken

> 데이터·인프라
![MySQL](https://img.shields.io/badge/mysql-%234479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![ElasticSearch](https://img.shields.io/badge/elasticsearch-%23005571.svg?style=for-the-badge&logo=elasticsearch&logoColor=white)
![Redis](https://img.shields.io/badge/redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/Rabbitmq-%23FF6600.svg?style=for-the-badge&logo=rabbitmq&logoColor=white)
- Qdrant 1.17.1 (의미 검색 벡터)

> 외부 API
- 공공데이터포털 기업마당 API
- Bizno 사업자등록번호 조회 API
- OpenAI (gpt-5.6-luna 기본, 랭킹은 gpt-5.6-sol, 임베딩은 text-embedding-3-small)




-------

## 🛠 기술스택

### Frontend
<div align="center">

![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)
![TypeScript](https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge&logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/vite-%23646CFF.svg?style=for-the-badge&logo=vite&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/tailwindcss-%2338B2AC.svg?style=for-the-badge&logo=tailwind-css&logoColor=white)
![Redux](https://img.shields.io/badge/redux-%23593d88.svg?style=for-the-badge&logo=redux&logoColor=white)
![Zod](https://img.shields.io/badge/zod-%233068b7.svg?style=for-the-badge&logo=zod&logoColor=white)
![NodeJS](https://img.shields.io/badge/node.js-%236DA55F.svg?style=for-the-badge&logo=node.js&logoColor=white)

</div>

### Backend (Core API)
<div align="center">

![Kotlin](https://img.shields.io/badge/kotlin-%237F52FF.svg?style=for-the-badge&logo=kotlin&logoColor=white)
![Spring](https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white)

</div>

<div align="center">

MyBatis 4.0.0 · Flyway · jsoup 1.23.2 · spring-security-crypto (BCrypt)

</div>

### AI Service
<div align="center">

![Python](https://img.shields.io/badge/python-%233670A0.svg?style=for-the-badge&logo=python&logoColor=ffdd54)
![FastAPI](https://img.shields.io/badge/fastapi-%23009688.svg?style=for-the-badge&logo=fastapi&logoColor=white)
![OpenAI](https://img.shields.io/badge/openai-%23412991.svg?style=for-the-badge&logo=openai&logoColor=white)

</div>

<div align="center">

Agents SDK 0.22.x · tiktoken

</div>

### 데이터 · 인프라
<div align="center">

![MySQL](https://img.shields.io/badge/mysql-%234479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![ElasticSearch](https://img.shields.io/badge/elasticsearch-%23005571.svg?style=for-the-badge&logo=elasticsearch&logoColor=white)
![Redis](https://img.shields.io/badge/redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/Rabbitmq-%23FF6600.svg?style=for-the-badge&logo=rabbitmq&logoColor=white)

</div>

<div align="center">

Qdrant 1.17.1 (의미 검색 벡터)

</div>

### 외부 API
<div align="center">

공공데이터포털 기업마당 API · Bizno 사업자등록번호 조회 API · OpenAI (gpt-5.6-luna · 랭킹 gpt-5.6-sol · 임베딩 text-embedding-3-small)

</div>



## 🛠 기술스택

| 구분 | 기술 |
|---|---|
| **Frontend** | <img src="https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB" height="20"/> <img src="https://img.shields.io/badge/typescript-%23007ACC.svg?style=for-the-badge&logo=typescript&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/vite-%23646CFF.svg?style=for-the-badge&logo=vite&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/tailwindcss-%2338B2AC.svg?style=for-the-badge&logo=tailwind-css&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/redux-%23593d88.svg?style=for-the-badge&logo=redux&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/zod-%233068b7.svg?style=for-the-badge&logo=zod&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/node.js-%236DA55F.svg?style=for-the-badge&logo=node.js&logoColor=white" height="20"/> |
| **Backend**<br/>(Core API) | <img src="https://img.shields.io/badge/kotlin-%237F52FF.svg?style=for-the-badge&logo=kotlin&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white" height="20"/><br/>MyBatis 4.0.0 · Flyway · jsoup 1.23.2 · spring-security-crypto(BCrypt) |
| **AI Service** | <img src="https://img.shields.io/badge/python-%233670A0.svg?style=for-the-badge&logo=python&logoColor=ffdd54" height="20"/> <img src="https://img.shields.io/badge/fastapi-%23009688.svg?style=for-the-badge&logo=fastapi&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/openai-%23412991.svg?style=for-the-badge&logo=openai&logoColor=white" height="20"/><br/>Agents SDK 0.22.x · tiktoken |
| **데이터·인프라** | <img src="https://img.shields.io/badge/mysql-%234479A1.svg?style=for-the-badge&logo=mysql&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/elasticsearch-%23005571.svg?style=for-the-badge&logo=elasticsearch&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white" height="20"/> <img src="https://img.shields.io/badge/rabbitmq-%23FF6600.svg?style=for-the-badge&logo=rabbitmq&logoColor=white" height="20"/><br/>Qdrant 1.17.1 (의미 검색 벡터) |
| **외부 API** | 공공데이터포털 기업마당 API · Bizno 사업자등록번호 조회 API · OpenAI (gpt-5.6-luna · 랭킹 gpt-5.6-sol · 임베딩 text-embedding-3-small) |




## 🔎인덱싱 단계
동기화 세대 발급
   → 기업마당 전체 페이지 수집·검증
   → Elasticsearch 키워드 색인 (Nori 형태소 분석)
   → Qdrant 벡터 준비 (OpenAI 임베딩)
   → 최신 시작 세대인지 확인
   → MySQL 카탈로그를 하나의 트랜잭션으로 공개


## 🖥️화면설계 (간단하게) **UI 시안/UX Flow**
### 01 지원사업 검색 · `/app/chat`

```mermaid
flowchart LR
    A["한 줄 입력<br/>기업 상황을 문장으로"] --> B["조건 확인 카드<br/>해석 40초"]
    B -->|확인| C["추천 0~5건<br/>검색 90초"]
    B -->|조건 모호| B2["질문으로 되돌아감"]
    B2 --> B
    C -->|결과 있음| D["공고 상세<br/>상세 10초"]
    C -->|결과 0건| C2["전체 공고로 범위 확대 제안"]
    D --> E1["관심 공고함에 담기"]
    D --> E2["원문 질문<br/>70초"]
    D --> E3["신청 문서 작성"]
    C -.->|429 · 504| X["입력 보존 · 수동 재시도"]

    classDef step fill:#e7f6ed,stroke:#087f46,color:#202124
    classDef done fill:#202124,stroke:#202124,color:#ffffff
    classDef branch fill:#fffaf0,stroke:#e0b357,color:#202124
    classDef error fill:#fff5f6,stroke:#c9636f,color:#202124
    class A,B,C step
    class D,E1,E2,E3 done
    class B2,C2 branch
    class X error
```

| 분기 | 조건 | 동작 |
|---|---|---|
| A | 조건이 모호함 | 질문으로 되돌아감 — 확인 전에는 검색하지 않음 |
| B | 결과 0건 | 전체 공고로 범위 확대를 **제안** (자동 실행 아님) |
| C | 429 · 504 | 입력을 보존하고 수동 재시도 버튼만 제공 |

   
## 한 줄 회고
