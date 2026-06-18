<p align="center">
  <img width="1920" height="600" alt="interviewpilot_banner" src="https://github.com/user-attachments/assets/d7be4ec4-fcf1-41e2-a7ae-d1f0bb621aa3" />

</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/LangGraph-0.1.14-6C63FF?style=for-the-badge&logo=chainlink&logoColor=white"/>
  <img src="https://img.shields.io/badge/FastAPI-0.111-009688?style=for-the-badge&logo=fastapi&logoColor=white"/>
  <img src="https://img.shields.io/badge/OpenAI-GPT--4o-412991?style=for-the-badge&logo=openai&logoColor=white"/>
  <img src="https://img.shields.io/badge/PostgreSQL-16-336791?style=for-the-badge&logo=postgresql&logoColor=white"/>
  <img src="https://img.shields.io/badge/Streamlit-1.35-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white"/>
  <img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge"/>
</p>

<p align="center">
  <strong>An end-to-end multi-agent platform that turns a resume + job description into a complete, personalised interview preparation experience — automatically.</strong>
</p>

---

## 📌 Overview

**InterviewPilot AI** is a production-grade multi-agent interview preparation system built for the **Damco AI Engineer Challenge 2026**.

Traditional interview prep takes 3–4 hours per role: manually reading the JD, comparing it to your resume, identifying gaps, sourcing questions, practising, and building a study plan. InterviewPilot AI collapses the entire workflow into a single pipeline driven by seven specialised AI agents coordinated by a **LangGraph StateGraph**.

```
Resume + JD  ──►  7 Agents  ──►  Skill Gap Report + Questions + Mock Interview + Score + Learning Plan
```

---

## 🧠 The Problem

| Pain Point | Impact |
|---|---|
| ❌ Manual JD analysis | 45–60 min per application |
| ❌ Inconsistent skill gap identification | Candidates miss critical gaps |
| ❌ Generic mock interview questions | Not tailored to the actual role |
| ❌ No structured feedback | Candidates don't know what to improve |
| ❌ Learning plans created from scratch | Repeated effort for every application |

**Result:** 3–4 hours of prep per interview, high effort, inconsistent quality.

---

## ✅ The Solution

InterviewPilot AI automates the complete preparation lifecycle:

```
You upload:   Resume (PDF / text)  +  Job Description
You receive:  Skill Gap Report  →  9 Personalised Questions  →  Mock Interview
              →  Scored Evaluation  →  4-Week Learning Plan
```

Total time: **under 2 minutes** vs 3–4 hours manually.

---

## 🏗️ Architecture

### High-Level System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                         USER                                │
│               Upload Resume + Job Description               │
└─────────────────────────┬───────────────────────────────────┘
                          │
                ┌─────────▼─────────┐
                │   Streamlit UI    │
                └─────────┬─────────┘
                          │
                ┌─────────▼─────────┐
                │     FastAPI       │
                │  Auth · API · Val │
                └─────────┬─────────┘
                          │
                ┌─────────▼──────────────┐
                │  LangGraph Orchestrator │
                └─────┬──────────────────┘
                      │
        ┌─────────────┼─────────────────┐
        ▼             ▼                 ▼
   Resume Agent   JD Agent       Gap Agent
        └─────────────┼─────────────────┘
                      │
               Question Agent
                      │
             Mock Interview Agent
                      │
             Evaluation Agent
                      │
           ┌──────────┴──────────┐
        Weak Score?           Pass / Hire
           │                     │
     Learning Plan          Finish
      + Re-interview
           │
          END
                      │
        ┌─────────────┼──────────────┐
        ▼             ▼              ▼
   PostgreSQL      ChromaDB      OpenAI API
```

---

## 🤖 Agent Pipeline

| # | Agent | Responsibility | Output |
|---|---|---|---|
| 1 | **Resume Agent** | Extract skills, projects, experience from resume | `ResumeData` |
| 2 | **JD Agent** | Parse requirements, responsibilities, context | `JDData` |
| 3 | **Gap Analysis Agent** | Compare resume vs JD, score match | `GapReport` |
| 4 | **Question Generator** | Create technical, behavioral & system design questions | `List[InterviewQuestion]` |
| 5 | **Mock Interview Agent** | Stateful adaptive conversation | `List[CandidateAnswer]` |
| 6 | **Evaluation Agent** | Score 0–10 per answer, overall feedback | `InterviewEvaluation` |
| 7 | **Learning Plan Agent** | Build week-by-week personalised roadmap | `LearningPlan` |

---

## 🔄 LangGraph State Flow

```
START
  │
  ▼
Resume Agent ──► JD Agent ──► Gap Analysis ──► Generate Questions
                                                        │
                                                   Mock Interview
                                                        │
                                                  Answer Evaluation
                                                        │
                                              ┌─────────┴─────────┐
                                         Weak Score?           Pass / Strong Hire
                                              │                     │
                                         Learning Plan           Finish
                                              │
                                         Re-interview ◄─────────────┐
                                         (max 2 cycles)             │
                                              │                     │
                                             END ◄───────────────────┘
```

The conditional routing at the Decision Node is what makes this **truly adaptive**: low-scoring candidates are automatically routed through a learning plan before attempting a second interview session.

---

## 🗂️ Project Structure

```
interviewpilot-ai/
│
├── 📓 InterviewPilot_AI_Gourav_Rohit.ipynb   # Colab notebook (full prototype)
│
├── backend/
│   ├── main.py                               # FastAPI application entry point
│   ├── agents/
│   │   ├── resume_agent.py
│   │   ├── jd_agent.py
│   │   ├── gap_analysis_agent.py
│   │   ├── question_generator_agent.py
│   │   ├── mock_interview_agent.py
│   │   ├── evaluation_agent.py
│   │   └── learning_plan_agent.py
│   ├── orchestrator/
│   │   └── langgraph_orchestrator.py         # StateGraph definition
│   ├── models/
│   │   ├── pydantic_models.py                # Request/response schemas
│   │   └── db_models.py                      # SQLAlchemy ORM models
│   ├── core/
│   │   ├── config.py
│   │   ├── security.py                       # JWT + RBAC
│   │   └── observability.py                  # Structured logging + metrics
│   └── db/
│       └── database.py
│
├── frontend/
│   └── streamlit_app.py                      # Streamlit UI
│
├── infra/
│   ├── docker-compose.yml
│   ├── Dockerfile
│   └── k8s/
│       ├── deployment.yaml
│       └── service.yaml
│
├── docs/
│   └── architecture/                         # All architecture diagrams
│
├── requirements.txt
└── README.md
```

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | Streamlit | Candidate-facing UI |
| API | FastAPI + Uvicorn | Async REST API |
| Orchestration | LangGraph | Stateful multi-agent workflow |
| LLM | OpenAI GPT-4o | All agent reasoning |
| Embeddings / Vector Store | ChromaDB | Resume & JD semantic search |
| Relational DB | PostgreSQL | Users, interviews, answers, plans |
| Auth | JWT (HS256 / RS256) | Authentication & RBAC |
| Infrastructure | AWS ECS / Kubernetes | Container orchestration |
| CDN / Edge | AWS CloudFront | Low-latency global delivery |
| Observability | Prometheus + Grafana | Metrics, alerting |
| Logging | Structured JSON logs | Shipped to CloudWatch |

---

## 🗄️ Database Schema

```
Users ──────────────────┐
  user_id (PK)          │ 1:N
  name, email           │
                        ▼
                   Interviews
                     interview_id (PK)
                     user_id (FK)          ──► Answers
                     status, score              answer_id (PK)
                                                interview_id (FK)
                                                content, score
                                                       │
                                                       ▼
                                                  Feedback
                                                  feedback_id (PK)
                                                  score, comments

Users ──► Resumes          (resume_id, skills, experience)
Users ──► JobDescriptions  (jd_id, role_title, requirements)
Users ──► LearningPlans    (plan_id, topics, timeline)
```

---

## 🔐 Security Architecture

```
User ──► JWT Auth ──► API Gateway ──► FastAPI ──► RBAC ──► Encrypted Storage ──► DB
         HS256/RS256   Rate Limiting   CORS        Admin /      AES-256 at rest
         60-min expiry WAF rules       HTTPS       Candidate /  TLS 1.3 in transit
                       100 req/min     Input val   Reviewer
```

Key controls implemented:
- **JWT Authentication** with access + refresh token flow
- **Role-Based Access Control** (Admin / Candidate / Reviewer)
- **API Gateway** with rate limiting (100 req/min) and WAF rules
- **AES-256 encryption** at rest, TLS 1.3 in transit
- **Prompt Injection Protection** on all LLM inputs

---

## 📊 Observability & Monitoring

```
Application ──► Structured Logs (JSON) ──► Prometheus ──► Grafana Dashboards
                                                 │
                                           Alert Manager
                                           ┌─────┴─────┐
                                        Email        Slack
                                           └─────┬─────┘
                                              Engineer
```

**Metrics tracked:**

| Category | Metrics |
|---|---|
| Application | API response time, throughput, error rate, uptime |
| AI | Token consumption, agent execution time, prompt failure rate |
| Business | Interview completion rate, average score, plan generation success |

---

## ⚡ Quick Start

### Option 1 — Colab (Recommended for demo)

1. Click the **Open in Colab** badge above
2. `Runtime → Run all`
3. See the full pipeline output in the final cell

### Option 2 — Local Installation

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/interviewpilot-ai.git
cd interviewpilot-ai

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate   # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Set environment variables
cp .env.example .env
# Edit .env — add your OPENAI_API_KEY

# 5. Start the database
docker-compose up -d postgres chromadb

# 6. Run the API
uvicorn backend.main:app --reload --port 8000

# 7. Run the UI (new terminal)
streamlit run frontend/streamlit_app.py
```

### Option 3 — Docker Compose

```bash
docker-compose up --build
# API:  http://localhost:8000
# UI:   http://localhost:8501
# Docs: http://localhost:8000/docs
```

---

## 🔌 API Reference

```http
GET  /health
POST /api/v1/prepare
GET  /api/v1/questions/{session_id}
GET  /api/v1/evaluation/{session_id}
GET  /api/v1/learning-plan/{session_id}
```

### Example: Run full pipeline

```python
import httpx

response = httpx.post("http://localhost:8000/api/v1/prepare", json={
    "candidate_name": "Gourav Rohit",
    "resume_text": "...",   # full resume text
    "jd_text": "..."        # full job description
})

result = response.json()
print(f"Match score   : {result['gap_report']['match_score']}%")
print(f"Questions     : {result['questions_generated']}")
print(f"Overall score : {result['overall_score']}/10")
print(f"Recommendation: {result['recommendation']}")
```

### Example Response

```json
{
  "session_id": "a3f7e2b1-...",
  "candidate_name": "Gourav Rohit",
  "gap_report": {
    "match_score": 72.5,
    "matched_skills": ["Python", "LangGraph", "FastAPI"],
    "missing_skills": ["Apache Kafka", "Airflow", "LLM Fine-tuning"],
    "priority_gaps": ["Apache Kafka", "Airflow", "Kubernetes"]
  },
  "questions_generated": 9,
  "overall_score": 7.4,
  "recommendation": "pass",
  "learning_plan_weeks": 4,
  "processing_time_seconds": 12.3
}
```

---

## 🏛️ Design Decisions & Tradeoffs

| Decision | Chosen | Alternative | Reason |
|---|---|---|---|
| Orchestration | **LangGraph** | CrewAI | Stateful workflows, conditional routing |
| Backend | **FastAPI** | Flask | Async support, auto OpenAPI docs |
| Vector DB | **ChromaDB** | Pinecone | Lower cost, local-first |
| LLM | **GPT-4o** | Claude / Gemini | Strong reasoning + function calling |
| Storage | **PostgreSQL** | MongoDB | Structured relational data |
| Container | **ECS + K8s** | Raw EC2 | Auto-scaling, fault tolerance |

---

## 🔧 Environment Variables

```env
# Required
OPENAI_API_KEY=sk-...

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/interviewpilot

# Security
JWT_SECRET_KEY=your-secret-key-here
JWT_ALGORITHM=HS256
JWT_EXPIRE_MINUTES=60

# ChromaDB
CHROMA_PERSIST_DIR=./chroma_db

# Thresholds
WEAK_SCORE_THRESHOLD=6.0
MAX_REINTERVIEW_CYCLES=2
```

---

## 📈 Failure Handling

| Failure | Strategy |
|---|---|
| Resume parsing fails | Retry (×3) → Fallback parser → Human review queue |
| LLM timeout | Exponential back-off retry (×3) → Mock response fallback |
| Invalid input | FastAPI validation layer rejects at boundary |
| Database failure | Read replica failover + connection pool retry |
| Agent crash | LangGraph catches exception, logs to structured output, marks step failed |

---

## 👤 Author

**Gourav Rohit**   
📧 happyrohit.rg@gmail.com  
📱 +91 9329601169  


---

<p align="center">
  <em>Built with ❤️ and a lot of ☕ — because interview prep shouldn't take 4 hours.</em>
</p>
