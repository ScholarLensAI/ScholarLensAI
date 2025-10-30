# ScholarLens AI

Upstage AI 기반 논문 리딩 어시스턴트

---

## 프로젝트 개요

ScholarLens AI는 연구자와 학생들이 논문을 효율적으로 읽고 이해할 수 있도록 돕는 AI 어시스턴트입니다. Upstage의 Document Parse API와 Solar LLM을 활용하여 논문을 자동으로 파싱하고, 섹션별 요약, 정보 추출, Q&A 기능을 제공합니다.

### 주요 기능

- **PDF 파싱**: 논문 업로드 시 자동으로 구조화된 데이터로 변환
- **섹션별 요약**: Introduction, Methods, Results 등 각 섹션 자동 요약
- **정보 추출**: Figures, Tables, Equations, References 자동 추출
- **Q&A 챗봇**: 논문 내용에 대한 실시간 질의응답
- **번역**: 영어 ↔ 한국어 실시간 번역 (개발 중)

---

## 기술 스택

| 구분 | 기술 |
|------|------|
| **Backend** | FastAPI, Python 3.9+ |
| **Frontend** | Next.js 14, React 18, TypeScript |
| **AI** | Upstage Document Parse, Solar LLM |
| **Styling** | Tailwind CSS, shadcn/ui |
| **Infrastructure** | Docker (예정) |

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

---

## 빠른 시작

### 1. 저장소 복제

```bash
# 서브모듈 포함 복제
git clone --recursive https://github.com/ScholarLensAI/PJT-ScholarLensAI.git

# 이미 복제한 경우
git submodule update --init --recursive
```

### 2. Backend 실행

```bash
cd scholarlensAI-BE/backend

# 의존성 설치
pip install -r requirements.txt

# 환경변수 설정
export UPSTAGE_API_KEY="up_your_api_key_here"

# 서버 실행
python main.py
```

Backend 서버: http://localhost:8000

### 3. Frontend 실행

```bash
cd scholarlensAI-FE

# 의존성 설치
npm install

# 개발 서버 실행
npm run dev
```

Frontend 서버: http://localhost:3000

자세한 설치 및 실행 방법은 [Quick Start Guide](QUICKSTART.md)를 참고하세요.

---

## 상세 문서

각 서브모듈의 상세한 설명은 아래 문서를 참고하세요:
- **Backend**: [scholarlensAI-BE/README.md](scholarlensAI-BE/README.md)
- **Frontend**: [scholarlensAI-FE/README.md](scholarlensAI-FE/README.md)

---

## 개발 로드맵

### Phase 1: MVP (완료)
- [x] Backend API 서버 구축
- [x] PDF 업로드 및 파싱
- [x] 기본 웹 UI

### Phase 2: 핵심 기능 (진행 중)
- [x] 섹션별 요약
- [x] 번역 기능
- [ ] 정보 추출
- [ ] Q&A 챗봇 인터페이스

### Phase 3: 배포
- [ ] Docker 컨테이너화
- [ ] CI/CD 파이프라인
- [ ] 클라우드 배포

---

## 서브모듈 관리

### 서브모듈 업데이트

```bash
# 모든 서브모듈 최신화
git submodule update --remote --merge

# 특정 서브모듈만 업데이트
git submodule update --remote scholarlensAI-FE
```

### 서브모듈 변경사항 커밋

```bash
# 서브모듈 내에서 작업 후
cd scholarlensAI-BE
git add .
git commit -m "Update backend"
git push

# 메인 저장소에서 서브모듈 참조 업데이트
cd ..
git add scholarlensAI-BE
git commit -m "Update backend submodule reference"
git push
```

---

## 환경 변수

### Backend
```bash
UPSTAGE_API_KEY=up_your_api_key_here
UPSTAGE_BASE_URL=https://api.upstage.ai/v1
LOG_LEVEL=INFO
```

### Frontend
```bash
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=ScholarLens
```

---

## 목표 사용자

- 연구자 및 대학원생
- 산업 분야 R&D 종사자
- 논문 리뷰어 및 저널 편집자
- 학부생 및 논문 입문자

---

## 참고 자료

- [Upstage API 문서](https://developers.upstage.ai/)
- [FastAPI 문서](https://fastapi.tiangolo.com/)
- [Next.js 문서](https://nextjs.org/docs)