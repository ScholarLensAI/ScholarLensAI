# Quick Start Guide

This guide helps you launch both Frontend (Next.js) and Backend (FastAPI) for the Scholarlens service at once.
For detailed information about each service, please refer to the respective README files.

---

## Prerequisites

### Required
- Python 3.10+
- Node.js 18+
- Upstage API Key ([Get your key](https://console.upstage.ai/))

### Setting Up Upstage API Key

1. Access [Upstage Console](https://console.upstage.ai/)
2. Sign up / Log in
3. Navigate to **API Keys** menu → **Create New Key**
4. Copy the generated key (e.g., `up_abcd1234...`)

    > ⚠️ **Security Notice**
    > - **Never commit API keys to Git**
    > - Ensure `.env` files are included in `.gitignore`
    > - If a key is exposed on GitHub/GitLab, revoke it immediately and generate a new one

---

## Installation

#### Step 1: Clone the Repository

```bash
# Clone with submodules
git clone --recursive https://github.com/ScholarLensAI/ScholarLensAI.git
cd ScholarLensAI

# If already cloned, update submodules
git submodule update --init --recursive
```

### Step 2: Configure Environment Variables
```bash
# Apply to current terminal session only
export UPSTAGE_API_KEY="up_your_api_key_here"

```


**Applies to:**
- ✅ FastAPI Backend
- ✅ Next.js Frontend
- ✅ Docker Compose

<details>
<summary><b>Alternative Environment Variable Setup Methods</b></summary>

#### Method 2: Create a `.env` file
Create a `.env` file in the project root:

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

#### Method 3: Shell Configuration File (Permanent)

```bash
# Add to ~/.bashrc or ~/.zshrc
echo 'export UPSTAGE_API_KEY="up_your_api_key_here"' >> ~/.bashrc
source ~/.bashrc
```
</details>

---

### Step 3: Launch Services


#### Method 1: Docker Compose (Recommended)

Run Frontend + Backend together:

```bash
# Build and run
docker compose up --build

# Or pass API key directly
UPSTAGE_API_KEY="up_your_api_key_here" docker compose up --build

# Run in background
docker compose up -d --build

# Stop
docker compose down
```

#### Method 2: Local Development Environment (Run Separately)

Run each service independently for development, modification, and testing.

##### A. Run Backend
```bash
# 1. Navigate to Backend directory
cd scholarlensAI-BE

# 2. Create and activate Python virtual environment
python3 -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Configure environment variables (use root .env or create new)
cat > .env <<'EOF'
UPSTAGE_API_KEY=up_your_api_key_here
UPSTAGE_BASE_URL=https://api.upstage.ai/v1
LOG_LEVEL=INFO
DEBUG=False
EOF

# 5. Run server
python3 main.py
# or
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

##### B. Run Frontend

Open a new terminal window and execute:

```bash
# 1. Navigate to Frontend directory
cd scholarlensAI-FE

# 2. Install dependencies
npm install

# 3. Configure environment variables
cat > .env.local <<'EOF'
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
NEXT_PUBLIC_APP_NAME=ScholarLens AI
EOF

# 4. Run development server
npm run dev
```

#### Step 4: Verify Access

| Service | URL | Description |
|--------|-----|------|
| **Frontend** | http://localhost:3000 | Web UI |
| **Backend API** | http://localhost:8000 | API Server |
| **API Docs** | http://localhost:8000/docs | Swagger UI |

---
## How to Use

### 1. Upload Paper

#### Using Web UI
1. Access http://localhost:3000
2. Click **Upload PDF** button or drag and drop
  ![alt text](<images/Screenshot from 2025-12-06 19-47-11.png>){: width="65%" height="65%"}
3. Select PDF (max 50MB)
4. Wait for parsing to complete (60 seconds)


#### Upload via API (curl)

```bash
curl -X POST http://localhost:8000/api/summary/upload \
  -F "file=@/path/to/paper.pdf"
```

**Response Example:**
```json
{
  "document_id": "doc_abc123xyz",
  "filename": "paper.pdf",
  "status": "success",
  "message": "Document parsed successfully"
}
```

---

### 2. View Analysis Results

#### Generate Full Paper Summary

```bash
curl http://localhost:8000/api/summary/generate/{document_id}
```

#### Retrieve Parsed Sections List

```bash
curl http://localhost:8000/api/summary/sections/{document_id}
```

**Response Example:**
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

### 3. Q&A Chatbot
Ask questions and receive answers based on the paper content.


```bash
curl -X POST http://localhost:8000/api/chat/message \
  -H "Content-Type: application/json" \
  -d '{
    "document_id": "{document_id}",
    "message": "What are the main contributions of this paper?"
  }'
```


---

### 4. Translation

```bash
curl -X POST http://localhost:8000/api/translation/translate \
  -H "Content-Type: application/json" \
  -d '{
    "text": "This paper presents a novel approach to natural language processing.",
    "source_language": "en",
    "target_language": "ko"
  }'
```

**Response Example:**
```json
{
  "translated_text": "이 논문은 자연어 처리에 대한 새로운 접근법을 제시합니다.",
  "source_language": "en",
  "target_language": "ko"
}
```

#### Check Supported Languages List

```bash
curl http://localhost:8000/api/translation/languages
```


---

## Key API Endpoints

| Method | Path | Description |
|--------|------|------|
| GET | `/health` | Check server status and validate API key |
| POST | `/api/summary/upload` | Upload PDF and start parsing |
| GET | `/api/summary/sections/{document_id}` | Retrieve parsed sections list |
| GET | `/api/summary/generate/{document_id}` | Generate full paper summary |
| POST | `/api/summary/section` | Summarize specific section |
| POST | `/api/translation/translate` | Translate text/section |
| GET | `/api/translation/languages` | Get supported languages list |
| POST | `/api/chat/message` | Document-based Q&A |
| GET | `/api/highlights/{document_id}` | Retrieve highlight areas |

- All APIs can be tested in Swagger UI
  **Swagger UI**: http://localhost:8000/docs

---

## Troubleshooting
- **API Key**:
  - Verify `UPSTAGE_API_KEY` is set and restart with `docker compose down && docker compose up --build`
- **Port Conflicts**:
  - Terminate processes occupying ports 3000 (Frontend), 8000 (Backend) or change ports
    - (`npm run dev -- -p 3001`, `uvicorn main:app --port 8001`)
- **Environment Variables**:
  - Frontend: Check `NEXT_PUBLIC_BACKEND_URL`
  - Backend: Verify `.env` or system variables
- **Dependency Errors**:
  - FE: `rm -rf node_modules package-lock.json && npm install`
  - BE: Recreate virtual environment and run `pip install -r requirements.txt`

---

## Next Steps

### 1. Test with Sample Paper

```bash
# Download Transformer paper
wget https://arxiv.org/pdf/1706.03762.pdf -O attention_is_all_you_need.pdf

# Upload
curl -X POST http://localhost:8000/api/summary/upload \
  -F "file=@attention_is_all_you_need.pdf"
```

### 2. Explore APIs with Swagger UI

1. Access http://localhost:8000/docs
2. Click each endpoint to view details
3. Test directly using **Try it out** button

### 3. Utilize Web UI

1. Access http://localhost:3000
2. Upload by dragging and dropping PDF
3. Verify automatic section-by-section summary generation
4. Ask questions about paper content using chatbot
5. Convert between English↔Korean using translation feature


## 📖 Additional Documentation

| Document | Content |
|------|------|
| **[README.md](README.md)** | Project overview and architecture |
| **[Backend README](scholarlensAI-BE/README.md)** | Backend API and service details |
| **[Frontend README](scholarlensAI-FE/README.md)** | Frontend structure and component descriptions |
