# Quick Start Guide

---

## 사전 준비

### 필수 요구사항
- Python 3.9+
- Node.js 18+
- Upstage API Key ([발급 받기](https://console.upstage.ai/))

### Upstage API Key 설정

1. [Upstage Console](https://console.upstage.ai/) 접속
2. 회원가입 / 로그인
3. **API Keys** 메뉴 → **Create New Key**
4. 생성된 키 복사 (예: `up_abcd1234...`)

```bash
# 환경변수로 설정 (임시)
export UPSTAGE_API_KEY="up_your_api_key_here"

# 영구 설정 (권장)
echo 'export UPSTAGE_API_KEY="up_your_api_key_here"' >> ~/.bashrc
source ~/.bashrc
```

---

## Installation

### 방법 1: Docker Compose (권장)

**장점**: 한 번에 Frontend + Backend 모두 실행

#### Step 1: 저장소 복제
```bash
# 서브모듈 포함 복제
git clone --recursive https://github.com/ScholarLensAI/ScholarLensAI.git
cd ScholarLensAI

# 또는 이미 복제한 경우
git submodule update --init --recursive
```

#### Step 2: 실행
```bash
# API 키와 함께 실행
UPSTAGE_API_KEY="up_your_key" docker compose up --build
```

**또는 .env 파일 사용**
```bash
# .env 파일 생성
cat > .env << EOF
UPSTAGE_API_KEY=up_your_api_key_here
EOF

# 실행
docker compose up --build
```

#### Step 3: 접속 확인
- **Frontend**: http://localhost:3000
- **Backend**: http://localhost:8000
- **API Docs**: http://localhost:8000/docs

```bash
# Health check
curl http://localhost:8000/health
# 응답: {"status":"healthy"}
```

#### 종료
```bash
# Ctrl+C 후
docker compose down
```

---

### 방법 2: 개별 실행

**장점**: 개발 시 각 서비스를 독립적으로 수정 가능

#### A. Backend 실행

```bash
# 1. Backend 디렉토리 이동
cd scholarlensAI-BE

# 2. 가상환경 생성 (선택)
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. 의존성 설치
pip install -r requirements.txt

# 4. 환경변수 설정
cat > .env << EOF
UPSTAGE_API_KEY=up_your_api_key_here
UPSTAGE_BASE_URL=https://api.upstage.ai/v1
LOG_LEVEL=INFO
DEBUG=False
EOF

# 5. 서버 실행
python main.py
```

**확인**: http://localhost:8000/health

#### B. Frontend 실행

**새 터미널을 열어서 실행**

```bash
# 1. Frontend 디렉토리 이동
cd scholarlensAI-FE

# 2. 의존성 설치
npm install

# 3. 환경변수 설정
cat > .env.local << EOF
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=ScholarLens
EOF

# 4. 개발 서버 실행
npm run dev
```

**확인**: http://localhost:3000

---

## How to Use

### 1. 논문 업로드

#### 웹 UI 사용
1. http://localhost:3000 접속
2. **Upload PDF** 버튼 클릭 또는 드래그 앤 드롭
3. PDF 선택 (최대 50MB)
4. 파싱 완료 대기 (10-30초)

#### curl 사용
```bash
curl -X POST http://localhost:8000/api/upload \
  -F "file=@/path/to/paper.pdf"
```

**응답 예시**
```json
{
  "document_id": "doc_123",
  "filename": "paper.pdf",
  "status": "success"
}
```

---

### 2. 분석 결과 확인

#### 파싱 결과 조회
```bash
curl http://localhost:8000/api/document/parse
```

#### 섹션별 요약
```bash
# Introduction 요약
curl -X POST http://localhost:8000/api/summarize/section \
  -H "Content-Type: application/json" \
  -d '{"section_type": "introduction"}'

# Methods 요약
curl -X POST http://localhost:8000/api/summarize/section \
  -H "Content-Type: application/json" \
  -d '{"section_type": "methods"}'
```

**section_type 옵션**
- `introduction`
- `methods`
- `results`
- `discussion`
- `conclusion`

---

### 3. 정보 추출

```bash
# Figures 추출
curl http://localhost:8000/api/extract/figures

# Tables 추출
curl http://localhost:8000/api/extract/tables

# Equations 추출
curl http://localhost:8000/api/extract/equations

# References 추출
curl http://localhost:8000/api/extract/references
```

---

### 4. Q&A 챗봇

```bash
curl -X POST http://localhost:8000/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "이 논문의 주요 기여는 무엇인가요?",
    "conversation_history": []
  }'
```

**질문 예시**
- "이 연구의 핵심 방법론은 무엇인가요?"
- "실험 결과를 간단히 설명해주세요"
- "이 논문의 한계점은 무엇인가요?"

---

### 5. 번역

```bash
curl -X POST http://localhost:8000/api/translate \
  -H "Content-Type: application/json" \
  -d '{
    "text": "This paper presents a novel approach",
    "target_language": "ko"
  }'
```

---

## 트러블슈팅

### Backend 문제

#### API 키 오류
```
Error: Invalid API key
```

**해결**
```bash
# 1. 환경변수 확인
echo $UPSTAGE_API_KEY

# 2. .env 파일 확인
cat scholarlensAI-BE/.env

# 3. API 키 테스트
curl -H "Authorization: Bearer $UPSTAGE_API_KEY" \
  https://api.upstage.ai/v1/models
```

---

#### 포트 충돌 (8000)
```
Error: Address already in use
```

**해결**
```bash
# 포트 사용 프로세스 확인
lsof -i :8000  # macOS/Linux
netstat -ano | findstr :8000  # Windows

# 다른 포트로 실행
uvicorn main:app --port 8001

# Frontend도 변경 필요
# .env.local: NEXT_PUBLIC_API_URL=http://localhost:8001
```

---

#### 패키지 설치 오류

**해결**
```bash
# pip 업그레이드
pip install --upgrade pip

# 가상환경 재생성
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

#### 파일 업로드 실패
```
Error: File too large
```

**원인**
- 파일 크기 제한: 50MB
- 지원 형식: PDF, PNG, JPG, JPEG

**해결**
```bash
# 업로드 디렉토리 권한 확인
mkdir -p uploads
chmod 755 uploads
```

---

#### 파싱 시간 초과

**원인**
- 대용량 PDF (100+ 페이지)
- 네트워크 불안정

**해결**
- 자동 처리: 100페이지 기준 동기/비동기 자동 전환
- Upstage API 상태 확인: [status.upstage.ai](https://status.upstage.ai)

---

### Frontend 문제

#### 포트 충돌 (3000)
```
Error: Port 3000 is already in use
```

**해결**
```bash
# 다른 포트로 실행
npm run dev -- -p 3001

# 또는 package.json 수정
"scripts": {
  "dev": "next dev -p 3001"
}
```

---

#### 의존성 오류
```
Error: Cannot find module
```

**해결**
```bash
# node_modules 재설치
rm -rf node_modules package-lock.json
npm install

# npm 캐시 정리
npm cache clean --force
npm install
```

---

#### 환경변수 인식 안 됨

**증상**: API 호출 실패, undefined 오류

**해결**
```bash
# 1. 파일 위치 확인
ls -la .env.local  # scholarlensAI-FE/ 루트에 있어야 함

# 2. NEXT_PUBLIC_ 접두사 확인
cat .env.local
# ✅ NEXT_PUBLIC_API_URL=http://localhost:8000
# ❌ API_URL=http://localhost:8000

# 3. 서버 재시작 (필수!)
# Ctrl+C 후 npm run dev
```

---

#### 빌드 오류

**해결**
```bash
# Next.js 캐시 삭제
rm -rf .next

# 재빌드
npm run build
```

---

### Docker 문제

#### Docker Compose 실행 실패

**해결**
```bash
# Docker 서비스 확인
docker info

# 이전 컨테이너 정리
docker compose down -v
docker system prune -a

# 재빌드
docker compose up --build --force-recreate
```

---

#### 메모리 부족
```
Error: Cannot allocate memory
```

**해결**
```bash
# Docker 메모리 증가
docker run -m 2g ...

# Docker Desktop 설정에서 메모리 할당 증가
```

---

### CORS 오류

```
Access to fetch blocked by CORS policy
```

**해결**

1. **Backend CORS 설정** (`config.py`)
```python
CORS_ORIGINS = [
    "http://localhost:3000",
    "http://127.0.0.1:3000"
]
```

2. **Frontend API URL 확인**
```bash
# .env.local
NEXT_PUBLIC_API_URL=http://localhost:8000  # https 아님!
```

3. **Backend 재시작**

---

## 주요 API 엔드포인트

| 엔드포인트 | 메서드 | 설명 |
|-----------|--------|------|
| `/health` | GET | 서버 상태 |
| `/api/upload` | POST | PDF 업로드 |
| `/api/summarize/section` | POST | 섹션 요약 |
| `/api/chat` | POST | Q&A |
| `/api/translate` | POST | 번역 |

**Swagger UI**: http://localhost:8000/docs

---

## next step

### 1. 샘플 논문 테스트
```bash
# arXiv 논문 다운로드
wget https://arxiv.org/pdf/1706.03762.pdf -O attention.pdf

# 업로드
curl -X POST http://localhost:8000/api/upload \
  -F "file=@attention.pdf"
```

### 2. API 테스트
- Swagger UI: http://localhost:8000/docs
- 각 엔드포인트 직접 테스트

### 3. 코드 이해
- Backend 구조: [백엔드.md](scholarlensAI-BE/README.md)
- Frontend 구조: [프론트엔드.md](scholarlensAI-FE/README.md)

---

## 추가 문서

- **[README.md](README.md)**: 프로젝트 전체 개요
- **[Backend_README.md](scholarlensAI-BE/README.md)**: Backend 기술 상세
- **[Frontend_README.md](scholarlensAI-FE/README.md)**: Frontend 기술 상세