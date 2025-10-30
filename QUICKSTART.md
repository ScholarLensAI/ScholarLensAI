# Quick Start Guide

---

## 사전 준비

### 필수 요구사항
- Python 3.9+
- Node.js 18+
- Upstage API Key ([발급 받기](https://console.upstage.ai/))

### Upstage API Key 설정

```bash
# 환경변수로 설정
export UPSTAGE_API_KEY="up_your_api_key_here"

# 또는 .env 파일 생성
echo "UPSTAGE_API_KEY=up_your_api_key_here" > scholarlensAI-BE/backend/.env
```

---

## 1. 저장소 복제

### 서브모듈 포함 복제

```bash
git clone --recursive https://github.com/ScholarLensAI/PJT-ScholarLensAI.git
cd PJT-ScholarLensAI
```

### 이미 복제한 경우

```bash
cd PJT-ScholarLensAI
git submodule update --init --recursive
```

---

## 2. Backend 실행

```bash
# Backend 디렉토리로 이동
cd scholarlensAI-BE/backend

# 의존성 설치
pip install -r requirements.txt

# 서버 실행
python3 main.py
```

**서버 주소**: http://localhost:8000

**확인 방법**:
```bash
# Health Check
curl http://localhost:8000/health

# Swagger 문서
open http://localhost:8000/docs
```

---

## 3. Frontend 실행

새 터미널 창을 열어서 실행:

```bash
# Frontend 디렉토리로 이동
cd scholarlensAI-FE

# 의존성 설치
npm install

# 개발 서버 실행
npm run dev
```

**서버 주소**: http://localhost:3000

### Docker로 실행 (선택)

```bash
cd scholarlensAI-FE
docker compose up --build
```

---

## 4. 기본 사용법

### 웹 인터페이스
1. http://localhost:3000 접속
2. PDF 파일 업로드
3. 분석 결과 확인

### API 직접 호출

**PDF 업로드**
```bash
curl -X POST http://localhost:8000/api/upload \
  -F "file=@/path/to/paper.pdf"
```

**섹션 요약**
```bash
curl -X POST http://localhost:8000/api/summarize/section \
  -H "Content-Type: application/json" \
  -d '{"section_type": "introduction"}'
```

**정보 추출**
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

**Q&A 채팅**
```bash
curl -X POST http://localhost:8000/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "이 논문의 주요 기여는 무엇인가요?",
    "conversation_history": []
  }'
```

---

## 5. 주요 API 엔드포인트

| 기능 | 메서드 | 엔드포인트 | 설명 |
|------|--------|------------|------|
| 상태 확인 | GET | `/health` | 서버 상태 확인 |
| PDF 업로드 | POST | `/api/upload` | 논문 파일 업로드 및 파싱 |
| 파싱 결과 | GET | `/api/document/parse` | 파싱된 문서 내용 조회 |
| 섹션 요약 | POST | `/api/summarize/section` | 특정 섹션 요약 |
| 정보 추출 | GET | `/api/extract/{type}` | figures/tables/equations/references |
| Q&A 채팅 | POST | `/api/chat` | 논문 내용 질의응답 |
| 문서 삭제 | DELETE | `/api/document` | 업로드된 문서 삭제 |

전체 API 문서: http://localhost:8000/docs

---

## 6. 트러블슈팅

### Backend 문제

**API 키 오류**
```bash
# 환경변수 확인
echo $UPSTAGE_API_KEY

# .env 파일 확인
cat scholarlensAI-BE/backend/.env
```

**포트 충돌 (8000번 포트)**
```bash
# 다른 포트로 실행
uvicorn main:app --host 0.0.0.0 --port 8001
```

**패키지 설치 오류**
```bash
# 가상환경 사용 권장
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### Frontend 문제

**포트 충돌 (3000번 포트)**
```bash
# 다른 포트로 실행
npm run dev -- -p 3001
```

**의존성 오류**
```bash
# node_modules 삭제 후 재설치
rm -rf node_modules package-lock.json
npm install
```

**환경변수 인식 안 됨**
- `.env.local` 파일이 `scholarlensAI-FE/` 루트에 있는지 확인
- `NEXT_PUBLIC_` 접두사가 있는지 확인
- 서버 재시작

### CORS 오류
- Backend의 `config.py`에서 CORS 설정 확인
- Frontend의 `NEXT_PUBLIC_API_URL`이 올바른지 확인

---

## 7. 추가 정보

### 상세 문서
- **Backend 상세 문서**: [scholarlensAI-BE/README.md](scholarlensAI-BE/README.md)
- **Frontend 상세 문서**: [scholarlensAI-FE/README.md](scholarlensAI-FE/README.md)

### 개발 가이드
- API 엔드포인트 추가 방법
- 새로운 기능 개발
- 테스트 작성

각 서브모듈의 README를 참고하세요.

### 서브모듈 업데이트

```bash
# 모든 서브모듈 최신화
git submodule update --remote --merge

# 특정 서브모듈만 업데이트
git submodule update --remote scholarlensAI-BE
git submodule update --remote scholarlensAI-FE
```

---

## 다음 단계

1. **API 테스트**: Swagger UI (http://localhost:8000/docs)에서 API 테스트
2. **웹 사용**: http://localhost:3000 에서 PDF 업로드 및 분석
3. **개발**: 각 서브모듈의 README를 참고하여 기능 확장

---

## 문의 및 지원

- **Issues**: 버그 리포트 및 기능 제안
- **Documentation**: 각 서브모듈의 README 참고
- **Upstage API**: https://developers.upstage.ai/