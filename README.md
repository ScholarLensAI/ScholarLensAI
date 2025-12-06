# ScholarLens AI

Upstage AI-based Research Paper Reading Assistant

---

## Project Overview

ScholarLens AI is an AI-powered analysis system that helps users read and understand research papers more efficiently.
It automatically parses paper structure through Upstage **Document Parse API** and provides the following features using **Solar Pro2 LLM**:

- Section-wise key summaries
- Context-based Q&A
- Semantic-based automatic highlighting
- Multilingual translation

#### Target Users

- Researchers and graduate students
- R&D professionals
- Paper reviewers and journal editors
- Undergraduate students and paper beginners

#### Key Features

- **Intelligent PDF Parsing**:
  - Automatic paper structure analysis with Upstage Document Parse upon paper upload
  - Automatic section recognition (Introduction, Methods, Results, Discussion, Conclusion)
- **AI Summary and Analysis**:
  - Section-wise key summaries
  - Real-time Q&A chatbot for paper content based on Solar LLM
  - Multilingual translation (Korean, English, Japanese, Chinese, etc.)
  - Automatic highlighting of key content

---
## Service Architecture
This project manages Frontend (Next.js) and Backend (FastAPI) as independent submodules, communicating via RESTful API.

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

## Tech Stack

| Category | Technology |
|------|------|
| **Backend** | FastAPI, Python 3.10+ |
| **Frontend** | Next.js, React 18, TypeScript |
| **AI** | Upstage Document Parse, Solar LLM |
| **Styling** | Tailwind CSS, shadcn/ui |
| **Infrastructure** | Docker |

---

## Project Structure

This project manages Frontend and Backend as Git Submodules.

```
ScholarLensAI/
├── scholarlensAI-FE/        # Frontend submodule
├── scholarlensAI-BE/        # Backend submodule
├── README.md                # Main documentation
├── QUICKSTART.md            # Quick start guide
└── .gitmodules              # Submodule configuration
```

## Development Roadmap
| Phase | Objective |
|--------|------|
| 1: MVP | Basic BE/FE setup, PDF parsing and UI construction |
| 2: Core Features | Section summary, information extraction, translation, Q&A |
| 3: Deployment | CI/CD, cloud operations, optimization and monitoring |

---

## Main API Endpoints

| Method | Path | Description |
|-------|------|------|
| GET | `/health` | Server status and API key verification |
| POST | `/api/summary/upload` | PDF upload and parsing request (returns document ID) |
| GET | `/api/summary/sections/{document_id}` | Retrieve parsed section list |
| GET | `/api/summary/generate/{document_id}` | Generate document section summaries |
| POST | `/api/summary/section` | Summarize specific section |
| POST | `/api/translation/translate` | Translate text/section |
| GET | `/api/translation/languages` | Retrieve supported languages |
| POST | `/api/chat/message` | Document-based Q&A |
| GET | `/api/highlights/{document_id}` | Retrieve document highlight areas |

📌 **Swagger UI** → `http://localhost:8000/docs`
📌 **Backend detailed documentation** → [Backend.md](https://github.com/ScholarLensAI/scholarlensAI-BE/blob/main/README.md)

## Quick Start
For the complete execution flow, refer to [QUICKSTART.md](QUICKSTART.md).
Summary is as follows:

1. Clone repository (including submodules)
```bash
git clone --recursive https://github.com/ScholarLensAI/ScholarLensAI.git
cd ScholarLensAI
```

2. **Environment Variable Setup**
```bash
# Create .env file
cp .env.example .env

# Enter API key in .env file
UPSTAGE_API_KEY=your_api_key_here
```

3. **Run Services**
```bash
docker compose up --build
# or
UPSTAGE_API_KEY="up_your_key" docker compose up --build
```

4. **Access**
   - Frontend → http://localhost:3000
   - Backend API → http://localhost:8000
   - API Documentation → http://localhost:8000/docs

---

## Troubleshooting

### Submodule Update
```bash
# Update all submodules to latest version
git submodule update --remote --merge
```

### Docker Build Failure
```bash
# Check Docker version (20.10+ recommended)
docker --version

# Check Docker Compose version (2.0+ recommended)
docker compose version

# Clean up existing containers and images
docker compose down -v
docker system prune -a
```

### Port Conflicts
If default ports 3000 and 8000 are already in use, change ports in `docker-compose.yml`:
```yaml
ports:
  - "3001:3000"  # Frontend
  - "8001:8000"  # Backend
```

### API Key Error
```bash
# Check .env file location
ls -la .env

# Validate API key format (no spaces)
cat .env | grep UPSTAGE_API_KEY

# Restart services
docker compose restart
```

---

### Service-specific Documentation Guide
| Documentation | Purpose |
|------|------|
| **README.md** (current) | Overall project overview and architecture |
| **[QUICKSTART.md](QUICKSTART.md)** | Installation and execution guide |
| **[Backend.md](https://github.com/ScholarLensAI/scholarlensAI-BE/blob/main/README.md)** | Backend API and service details |
| **[Frontend.md](https://github.com/ScholarLensAI/scholarlensAI-FE/blob/main/README.md)** | Frontend web interface execution and structure explanation |