# Quick Start Guide

Scholarlens service를 실행하기 위한 Frontend(Next.js)와 Backend(FastAPI)를 한 번에 올릴 수 있는 가이드입니다.
서비스별 세부 내용은 각 하위 README를 참고하세요.

---

## 사전 준비

### 필수 요구사항
- Python 3.10+
- Node.js 18+
- Upstage API Key ([발급 받기](https://console.upstage.ai/))

### Upstage API Key 설정

1. [Upstage Console](https://console.upstage.ai/) 접속
2. 회원가입 / 로그인
3. **API Keys** 메뉴 → **Create New Key**
4. 생성된 키 복사 (예: `up_abcd1234...`)

    > ⚠️ **보안 주의사항**
    > - API 키를 **절대 Git에 커밋하지 마세요**
    > - `.env` 파일은 반드시 `.gitignore`에 포함되어야 합니다
    > - GitHub/GitLab에 키가 노출되면 즉시 폐기하고 재발급하세요

---

## Installation

#### Step 1: 저장소 클론

```bash
# 서브모듈 포함 클론
git clone --recursive https://github.com/ScholarLensAI/ScholarLensAI.git
cd ScholarLensAI

# 이미 클론한 경우 서브모듈 업데이트
git submodule update --init --recursive
```

### Step 2: 환경 변수 설정
```bash
# 현재 터미널 세션에만 적용
export UPSTAGE_API_KEY="up_your_api_key_here"

```


**적용 범위:**
- ✅ FastAPI Backend
- ✅ Next.js Frontend
- ✅ Docker Compose

<details>
<summary><b>다른 환경 변수 설정 방법</b></summary>

#### 방법 2: `.env` 파일을 생성
프로젝트 루트에 `.env` 파일을 생성합니다:

```bash
cat > .env <<'EOF'
UPSTAGE_API_KEY=up_your_api_key_here
UPSTAGE_BASE_URL=https://api.upstage.ai/v1
LOG_LEVEL=INFO
DEBUG=0

NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
BACKEND_INTERNAL_URL=http://backend:8000
EOF
```

#### 방법 3: Shell 설정 파일 (영구)

```bash
# ~/.bashrc 또는 ~/.zshrc에 추가
echo 'export UPSTAGE_API_KEY="up_your_api_key_here"' >> ~/.bashrc
source ~/.bashrc
```
</details>

---

### Step 3: 환경 변수 설정


#### 방법 1: Docker Compose (권장)

한 번에 Frontend + Backend 모두 실행:

```bash
# 빌드 및 실행
# 빌드 및 실행
docker compose up --build

# 또는 API 키 직접 전달
UPSTAGE_API_KEY="up_your_api_key_here" docker compose up --build

# 백그라운드 실행
docker compose up -d --build

# 종료
docker compose down
```

#### 방법 2: 로컬 개발 환경 (개별 실행)

개발 시 각 서비스를 독립적으로 수정하고 테스트할 수 있습니다.

##### A. Backend 실행
```bash
# 1. Backend 디렉토리 이동
cd scholarlensAI-BE

# 2. Python 가상환경 생성 및 활성화
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. 의존성 설치
pip install -r requirements.txt

# 4. 환경 변수 설정 (루트 .env 사용 또는 별도 생성)
cat > .env <<'EOF'
UPSTAGE_API_KEY=up_your_api_key_here
UPSTAGE_BASE_URL=https://api.upstage.ai/v1
LOG_LEVEL=INFO
DEBUG=False
EOF

# 5. 서버 실행
python3 main.py
# 또는
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

##### B. Frontend 실행

새 터미널 창을 열어서 실행:

```bash
# 1. Frontend 디렉토리 이동
cd scholarlensAI-FE

# 2. 의존성 설치
npm install

# 3. 환경 변수 설정
cat > .env.local <<'EOF'
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=ScholarLens AI
EOF

# 4. 개발 서버 실행
npm run dev
```

#### Step 4: 접속 확인

| 서비스 | URL | 설명 |
|--------|-----|------|
| **Frontend** | http://localhost:3000 | 웹 UI |
| **Backend API** | http://localhost:8000 | API 서버 |
| **API Docs** | http://localhost:8000/docs | Swagger UI |


---
## How to Use

### 1. 논문 업로드

#### 웹 UI 사용
1. http://localhost:3000 접속
2. **Upload PDF** 버튼 클릭 또는 드래그 앤 드롭
  ![alt text](<images/Screenshot from 2025-12-06 19-47-11.png>){: width="65%" height="65%"}
4. PDF 선택 (최대 50MB)
5. 파싱 완료 대기 (60초)


#### API로 업로드 (curl)

```bash
curl -X POST http://localhost:8000/api/summary/upload \
  -F "file=@/path/to/paper.pdf"
```

**응답 예시:**
```json
{
  "document_id": "doc_abc123xyz",
  "filename": "paper.pdf",
  "status": "success",
  "message": "Document parsed successfully"
}
```

---

### 2. 분석 결과 확인

#### 전체 논문 요약 생성

```bash
curl http://localhost:8000/api/summary/generate/{document_id}
```

#### 파싱된 섹션 목록 조회

```bash
curl http://localhost:8000/api/summary/sections/{document_id}
```

**응답 예시:**
```json
{
  "document_id": "doc_abc123xyz",
  "sections": [
    {"type": "introduction", "title": "Introduction", "page": 1},
    {"type": "methods", "title": "Methods", "page": 3},
    {"type": "results", "title": "Results", "page": 5},
    {"type": "discussion", "title": "Discussion", "page": 8},
    {"type": "conclusion", "title": "Conclusion", "page": 10}
  ]
}
```
---

### 3. Q&A 챗봇
논문 내용을 기반으로 질문하고 답변을 받을 수 있습니다.


```bash
curl -X POST http://localhost:8000/api/chat/message \
  -H "Content-Type: application/json" \
  -d '{
    "document_id": "{document_id}",
    "message": "이 논문의 주요 기여는 무엇인가요?"
  }'
```


---

### 4. 번역

```bash
curl -X POST http://localhost:8000/api/translation/translate \
  -H "Content-Type: application/json" \
  -d '{
    "text": "This paper presents a novel approach to natural language processing.",
    "source_language": "en",
    "target_language": "ko"
  }'
```

**응답 예시:**
```json
{
  "translated_text": "이 논문은 자연어 처리에 대한 새로운 접근법을 제시합니다.",
  "source_language": "en",
  "target_language": "ko"
}
```

#### 지원 언어 목록 확인

```bash
curl http://localhost:8000/api/translation/languages
```


---

## 주요 API 엔드포인트

| 메서드 | 경로 | 설명 |
|--------|------|------|
| GET | `/health` | 서버 상태 확인 및 API 키 검증 |
| POST | `/api/summary/upload` | PDF 업로드 및 파싱 시작 |
| GET | `/api/summary/sections/{document_id}` | 파싱된 섹션 목록 조회 |
| GET | `/api/summary/generate/{document_id}` | 전체 논문 요약 생성 |
| POST | `/api/summary/section` | 특정 섹션 요약 |
| POST | `/api/translation/translate` | 텍스트/섹션 번역 |
| GET | `/api/translation/languages` | 지원 언어 목록 |
| POST | `/api/chat/message` | 문서 기반 Q&A |
| GET | `/api/highlights/{document_id}` | 하이라이트 영역 조회 |

- 모든 API는 Swagger UI에서 테스트 가능
  **Swagger UI**: http://localhost:8000/docs

---

## 트러블슈팅
- **API 키**:
  - `UPSTAGE_API_KEY`가 설정되었는지 확인하고 `docker compose down && docker compose up --build`로 재시작
- **포트 충돌**:
  - 3000(Frontend), 8000(Backend) 점유 프로세스 종료 또는 포트 변경
    - (`npm run dev -- -p 3001`, `uvicorn main:app --port 8001`)
- **환경 변수**:
  - Frontend: `NEXT_PUBLIC_BACKEND_URL` 확인
  - Backend: `.env` 또는 시스템 변수 확인
- **의존성 오류**:
  - FE: `rm -rf node_modules package-lock.json && npm install`,
  - BE: 가상환경 재구성 후 `pip install -r requirements.txt`

---

## next step

### 1. 샘플 논문으로 테스트

```bash
# Transformer 논문 다운로드
wget https://arxiv.org/pdf/1706.03762.pdf -O attention_is_all_you_need.pdf

# 업로드
curl -X POST http://localhost:8000/api/summary/upload \
  -F "file=@attention_is_all_you_need.pdf"
```

### 2. Swagger UI로 API 탐색

1. http://localhost:8000/docs 접속
2. 각 엔드포인트 클릭하여 세부 정보 확인
3. **Try it out** 버튼으로 직접 테스트

### 3. 웹 UI 활용

1. http://localhost:3000 접속
2. PDF 드래그 앤 드롭으로 업로드
3. 섹션별 요약 자동 생성 확인
4. 챗봇으로 논문 내용 질문
5. 번역 기능으로 영문↔한글 변환


## 📖 추가 문서

| 문서 | 내용 |
|------|------|
| **[README.md](README.md)** | 프로젝트 전체 개요 및 아키텍처 |
| **[Backend README](scholarlensAI-BE/README.md)** | Backend API 및 서비스 상세 |
| **[Frontend README](scholarlensAI-FE/README.md)** | Frontend 구조 및 컴포넌트 설명 |