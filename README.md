# IndustrialIQ — AI-Powered Industrial Knowledge Intelligence Platform

IndustrialIQ transforms static industrial documents (P&IDs, maintenance logs, inspection reports, compliance manuals) into an interactive, multi-modal knowledge graph. It enables plant engineers, safety auditors, and maintenance teams to instantly retrieve insights, perform root cause analysis, and verify compliance through natural language.

---

## 🚀 Live Demo & Deployment

This repository is configured for easy deployment on **Vercel** (frontend) and **Render** (backend).

- **Frontend**: [Deploy to Vercel](https://vercel.com/new)
- **Backend**: [Deploy to Render](https://render.com) (using the provided `Dockerfile` and `render.yaml`)

**Note for Judges**:
To make testing easier, we have committed a pre-ingested vector store (`data/chroma`) containing 10 sample industrial documents. This means **you do not need to run document ingestion** to test the system.

---

## 🛠️ Local Setup (For Judges)

Follow these steps to run the platform locally on your machine.

### 1. Prerequisites
- Python 3.11+
- Node.js 18+
- [Neo4j AuraDB Free Account](https://neo4j.com/cloud/aura/) (or local Neo4j desktop)
- OpenAI API Key

### 2. Configure Environment Variables
Copy the example config and fill in your keys:
```bash
cp .env.example .env
```
Inside `.env`, provide your:
- `OPENAI_API_KEY`
- `NEO4J_URI`, `NEO4J_USERNAME`, `NEO4J_PASSWORD`

*(Optional) If you don't have Neo4j, the app will gracefully fall back to vector-only search.*

### 3. Start the Backend
```bash
python -m venv .venv
# Activate venv:
# Windows: .venv\Scripts\activate
# Mac/Linux: source .venv/bin/activate

pip install -r requirements.txt

# Start the API server
uvicorn backend.api.main:app --reload --port 8000
```
*API docs will be available at [http://localhost:8000/docs](http://localhost:8000/docs).*

### 4. Start the Frontend
In a new terminal:
```bash
cd frontend
npm install
npm run dev
```
*The UI will be available at [http://localhost:3000](http://localhost:3000).*

---

## 🔍 Try These Queries!

Once the system is running, try asking these questions to see the multi-agent system in action:

1. **Maintenance & RCA**: *"What is the MTBF for Pump P-101 and what caused its last failure?"*
2. **Compliance & Safety**: *"Are we compliant with OSHA regulations for gas detectors?"*
3. **Multi-Hop Graph Search**: *"Show me the maintenance history for any equipment connected to Valve V-201."*
4. **Image Vision**: *"What are the operating parameters shown in the PID.jpg diagram?"*

---

## 🏗️ Architecture

```
User Query
    │
    ▼
FastAPI Gateway
    │
    ▼
LangGraph Supervisor 
    │
    ├─ 1. Query Classifier (single-fact / multi-hop / aggregation)
    │
    ├─ 2. Hybrid Retrieval
    │       ├── ChromaDB Vector Search (Semantic)
    │       └── Neo4j Graph Traversal (Relationships)
    │               └─ Weighted Reranker
    │
    ├─ 3. Specialist Agents (Parallel)
    │       ├── CopilotAgent (General Q&A)
    │       ├── MaintenanceAgent (RCA, work orders)
    │       ├── ComplianceAgent (OSHA, OISD)
    │       └── LessonsLearnedAgent (Failure history)
    │
    └─ 4. Synthesis Engine ── Final answer + citations + confidence
```



