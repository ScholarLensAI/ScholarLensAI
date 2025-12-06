# ScholarLens AI

Upstage AI 기반 논문 리딩 어시스턴트

---

## 프로젝트 개요

ScholarLens AI는 사용자가 논문을 보다 효율적으로 읽고 이해하도록 돕는 AI 기반 분석 시스템입니다.
Upstage **Document Parse API**를 통해 논문 구조를 자동 파싱하고, **Solar Pro2 LLM**을 활용하여 다음 기능을 제공합니다:

- 섹션별 핵심 요약
- 문맥 기반 Q&A
- 의미 기반 자동 하이라이팅
- 다국어 번역



#### 목표 사용자

- 연구자 및 대학원생
- R&D 종사자
- 논문 리뷰어 및 저널 편집자
- 학부생 및 논문 입문자

#### 주요 기능

- **지능형 PDF 파싱**:
  - 논문 업로드 시 Upstage Document Parse로 논문 구조 자동 분석
  - 섹션 자동 인식 (Introduction, Methods, Results, Discussion, Conclusion)
- **AI 요약 및 분석**:
  - 섹션별 핵심 요약
  - Solar LLM 기반의 논문 내용에 대한 실시간 질의응답 Q&A chatbot
  - 다국어 번역 (한국어, 영어, 일본어, 중국어 등)
  - 핵심 내용 자동 하이라이트

---
## Service Architecture
본 프로젝트는 Frontend(Next.js)와 Backend(FastAPI)를 독립 서브모듈로 관리하며, RESTful API 기반으로 통신합니다.


```
┌─────────────────────────────────────────────────────────┐
│                    ScholarLens AI                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────┐         ┌────────────────────┐     │
│  │   Frontend      │ ◄─────► │     Backend        │     │
│  │   (Next.js)     │ RESTful │    (FastAPI)       │     │
│  │                 │   API   │                    │     │
│  │  - PDF Viewer   │         │  - Document Parse  │     │
│  │  - UI/UX        │         │  - AI Services     │     │
│  │  - State Mgmt   │         │  - API Endpoints   │     │
│  └─────────────────┘         └────────────────────┘     │
│                                       │                 │
│                                       ▼                 │
│                              ┌─────────────────┐        │
│                              │  Upstage API    │        │
│                              │                 │        │
│                              │  - Doc Parse    │        │
│                              │  - Solar LLM    │        │
│                              └─────────────────┘        │
└─────────────────────────────────────────────────────────┘
```

## 기술 스택

| 구분 | 기술 |
|------|------|
| **Backend** | FastAPI, Python 3.10+ |
| **Frontend** | Next.js, React 18, TypeScript |
| **AI** | Upstage Document Parse, Solar LLM |
| **Styling** | Tailwind CSS, shadcn/ui |
| **Infrastructure** | Docker|

---

## 프로젝트 구조

본 프로젝트는 Frontend 및 Backend를 Git Submodule로 관리합니다.

```
ScholarLensAI/
├── scholarlensAI-FE/        # Frontend 서브모듈
├── scholarlensAI-BE/        # Backend 서브모듈
├── README.md                # 메인 문서
├── QUICKSTART.md            # 빠른 시작 가이드
└── .gitmodules              # 서브모듈 설정
```



## 개발 로드맵
| Phase | 목표 |
|--------|------|
| 1: MVP | 기본 BE/FE 구성, PDF 파싱 및 UI 구축 |
| 2: 핵심 기능 | 섹션 요약·정보 추출·번역·Q&A |
| 3: 배포 | CI/CD, 클라우드 운영, 최적화 및 모니터링 |


---

## 주요 API 엔드포인트

| 메서드 | 경로 | 설명 |
|-------|------|------|
| GET | `/health` | 서버 상태 및 API 키 확인 |
| POST | `/api/summary/upload` | PDF 업로드 및 파싱 요청 (문서 ID 반환) |
| GET | `/api/summary/sections/{document_id}` | 파싱된 섹션 목록 조회 |
| GET | `/api/summary/generate/{document_id}` | 문서 섹션별 요약 생성 |
| POST | `/api/summary/section` | 특정 섹션 요약 |
| POST | `/api/translation/translate` | 텍스트/섹션 번역 |
| GET | `/api/translation/languages` | 지원 언어 조회 |
| POST | `/api/chat/message` | 문서 기반 질의응답 |
| GET | `/api/highlights/{document_id}` | 문서 하이라이트 영역 조회 |

📌 **Swagger UI** → `http://localhost:8000/docs`
📌 **Backend 상세 문서** → [Backend.md](https://github.com/ScholarLensAI/scholarlensAI-BE/blob/main/README.md)


## 빠른 시작
전체 실행 흐름은 [QUICKSTART.md](QUICKSTART.md)를 참고하세요.
요약은 아래와 같습니다.

1. 저장소 클론 (서브모듈 포함)
```
git clone --recursive https://github.com/ScholarLensAI/ScholarLensAI.git
cd ScholarLensAI
```

2. **환경 변수 설정**
```bash
# .env 파일 생성
cp .env.example .env

# .env 파일에 API 키 입력
UPSTAGE_API_KEY=your_api_key_here
```

3. **서비스 실행**
```bash
docker compose up --build
# 또는
UPSTAGE_API_KEY="up_your_key" docker compose up --build
```

4. **접속**
   - Frontend → http://localhost:3000
   - Backend API → http://localhost:8000
   - API 문서 → http://localhost:8000/docs


---

## 트러블슈팅

### 서브모듈 업데이트
```bash
# 모든 서브모듈 최신 버전으로 업데이트
git submodule update --remote --merge
```

### Docker 빌드 실패
```bash
# Docker 버전 확인 (20.10 이상 권장)
docker --version

# Docker Compose 버전 확인 (2.0 이상 권장)
docker compose version

# 기존 컨테이너 및 이미지 정리
docker compose down -v
docker system prune -a
```

### 포트 충돌
기본 포트 3000, 8000이 이미 사용 중인 경우 `docker-compose.yml`에서 포트 변경:
```yaml
ports:
  - "3001:3000"  # Frontend
  - "8001:8000"  # Backend
```

### API 키 오류
```bash
# .env 파일 위치 확인
ls -la .env

# API 키 형식 검증 (공백 없이)
cat .env | grep UPSTAGE_API_KEY

# 서비스 재시작
docker compose restart
```

---

### 서비스별 문서 안내
| 문서 | 목적 |
|------|------|
| **README.md** (현재) | 프로젝트 전체 개요 및 아키텍처 |
| **[QUICKSTART.md](QUICKSTART.md)** | 설치 및 실행 가이드 |
| **[Backend.md](https://github.com/ScholarLensAI/scholarlensAI-BE/blob/main/README.md)** | Backend API 및 서비스 상세 |
| **[Frontend.md](https://github.com/ScholarLensAI/scholarlensAI-FE/blob/main/README.md)** | Frontend 웹 화면 실행 및 구조 설명 |
