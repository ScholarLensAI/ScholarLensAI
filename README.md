# ScholarLens AI

Upstage AI 기반 논문 리딩 어시스턴트

---

## 프로젝트 개요

ScholarLens AI는 사용자가 논문을 효율적으로 읽고 이해할 수 있도록 돕는 AI 어시스턴트입니다.
Upstage의 Document Parse API를 통하여 논문을 자동으로 파싱하고, Upstage의 Solar Pro2 API를 이용하여 섹션별 요약, 정보 추출, Q&A 기능을 제공합니다.


## 목표 사용자

- 연구자 및 대학원생
- 산업 분야 R&D 종사자
- 논문 리뷰어 및 저널 편집자
- 학부생 및 논문 입문자


## 주요 기능

- **지능형 PDF 파싱**:
  - 논문 업로드 시 Upstage Document Parse로 논문 구조 자동 분석
  - 섹션 자동 인식 (Introduction, Methods, Results, Discussion, Conclusion)
- **AI 요약 및 분석**:
  - Introduction, Methods, Results 등 각 섹션 핵심 내용 자동 요약
  - 주요 발견사항 및 기여점 추출
  - Solar LLM 기반의 논문 내용에 대한 실시간 질의응답 Q&A chatbot
  - 다국어 번역

---
## 아키텍처

```
┌─────────────────────────────────────────────────────────┐
│                    ScholarLens AI                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────┐         ┌────────────────────┐     │
│  │   Frontend      │ ◄─────► │     Backend        │     │
│  │   (Next.js)     │  REST   │    (FastAPI)       │     │
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

본 저장소는 Frontend와 Backend를 Git Submodule로 관리합니다.

```
ScholarLensAI/
├── scholarlensAI-FE/        # Frontend 서브모듈
├── scholarlensAI-BE/        # Backend 서브모듈
├── README.md                # 메인 문서
├── QUICK_GUIDE.md          # 빠른 시작 가이드
└── .gitmodules             # 서브모듈 설정
```


## 개발 로드맵

### Phase 1: MVP
- [x] Backend API 서버
- [x] PDF 파싱 및 업로드
- [x] 기본 웹 UI

### Phase 2: 핵심 기능
- [x] 섹션별 요약
- [x] 정보 추출 (Figures, Tables, Equations)
- [x] 번역 기능
- [x] Q&A 챗봇 UI

### Phase 3: 배포 (계획)
- [ ] CI/CD 파이프라인
- [ ] 클라우드 배포
- [ ] 성능 최적화
- [ ] 모니터링 시스템

---

## 주요 API 엔드포인트

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/health` | GET | 서버 상태 확인 |
| `/api/upload` | POST | PDF 업로드 |
| `/api/document/parse` | GET | 파싱 결과 조회 |
| `/api/summarize/section` | POST | 섹션 요약 |
| `/api/extract/{type}` | GET | 정보 추출 |
| `/api/chat` | POST | Q&A 채팅 |

**전체 API**: http://localhost:8000/docs
**상세 설명**: [백엔드.md](scholarlensAI-BE/README.md)


## 🔗 문서 가이드
각 문서의 목적과 대상:

| 문서 | 목적 |
|------|------|
| **README.md** (현재) | 프로젝트 전체 개요 및 아키텍처 |
| **[QUICKSTART.md](QUICKSTART.md)** | 설치 및 실행 가이드 |
| **[백엔드.md](./scholarlensAI-BE/README.md)** | Backend API 및 서비스 상세 |
| **[프론트엔드.md](scholarlensAI-BE @ 664b0a8/README.md)** | Frontend 컴포넌트 및 페이지 |
