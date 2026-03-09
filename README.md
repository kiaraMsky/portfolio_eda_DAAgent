# 🧠 ada — Personal Agentic Data Analyst

> Chat with your CSV and Excel files like a senior analyst. Runs entirely on your laptop. Your data never leaves.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-14-black?logo=next.js)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-In%20Development-orange)
![LLM](https://img.shields.io/badge/LLM-OpenRouter%20%7C%20Azure%20OpenAI-blueviolet)

---

**ada** is a local-first, no-code data analysis assistant. Drop in a CSV or Excel file, give it a plain-English context file describing your data, and start asking questions. It asks clarifying questions when something is ambiguous, shows you its plan before running anything, lets you correct results inline, and remembers your preferences across sessions — all without sending your data to any server.

No PostgreSQL. No Docker required. No cloud upload. Just `pip install`, `npm install`, and you're running.

---

## ✨ Features

### 🗣️ Natural Language Analysis
Ask questions the way you'd ask a colleague. ada maps your words to the right columns and metrics using a business context file you define.

```
You: "Show me sales by region for Q3"
ada: "Which 'sales' metric — Net Revenue or Gross Revenue?"
You: "Net Revenue"
ada: [runs analysis, renders bar chart, writes 3-sentence summary]
```

### 🧩 Context-Aware Semantic Layer
Upload a `context.yaml` that describes your columns in plain English — synonyms, metric formulas, business rules. ada uses it to understand *your* terminology, not just column headers.

```yaml
columns:
  rev_amt:
    display_name: Net Revenue
    synonyms: [revenue, sales, income, net sales]
metrics:
  total_revenue:
    formula: "df['rev_amt'].sum()"
business_rules:
  - "Exclude rows where order_status == 'cancelled'"
```

### 🤔 Proactive Clarification (Consultant Pattern)
Before running anything, ada scores its own confidence across three dimensions — data completeness, schema clarity, and query confidence. If confidence falls below 95%, it pauses and asks targeted questions with clickable option pills. No silent wrong guesses.

### 📋 Plan Before Execution
Every analysis is broken into a step-by-step task tree. You review it and click **▶ Run This Plan** before any code executes. You can remove steps or ask for an explanation of any task.

### 🔒 Dual-Mode Secure Code Execution

All LLM-generated pandas code is sandboxed before running. Two modes, switchable in settings:

| | Mode A — Sandbox | Mode B — Security Agent |
|---|---|---|
| Isolation | Docker/Podman container | RestrictedPython + LLM security review |
| Network | `--network none` | Blocked via RestrictedPython |
| Requires Docker | Yes | No |
| LLM cost | None | ~1 cheap API call per execution |

### ✏️ Inline Iterative Correction
Every response has a **✏️ Correct this** button. Type what was wrong — ada makes the minimal code change, shows you what changed, and threads the corrected result under the original. Revert to any prior version at any time.

### 🧠 Three-Scope Memory
- **Turn memory** — full context within a single multi-turn query
- **Session memory** — confirmed term mappings apply to all queries this session
- **Cross-session memory** — stored in local SQLite; same preferences auto-apply next time you open the same dataset

### 🎨 Julius AI-Inspired UI
Dark-mode React/Next.js interface with inline Plotly charts, collapsible code blocks, a persistent file/context sidebar, and real-time execution progress streaming via WebSocket.

---

## 🖥️ Screenshot

> *(Coming once Phase 7 is complete)*

---

## 🚀 Quick Start

### Prerequisites
- Python 3.10+
- Node.js 18+
- An [OpenRouter](https://openrouter.ai) API key **or** an Azure OpenAI resource

### 1. Clone & install

```bash
git clone https://github.com/your-username/ada.git
cd ada

# Backend
pip install -r requirements.txt

# Frontend
npm install
```

### 2. Configure

```bash
cp .env.example .env
# Edit .env — add your OpenRouter or Azure OpenAI key
```

### 3. Run

```bash
# Option A — two terminals
uvicorn backend.main:app --reload   # terminal 1
npm run dev                         # terminal 2

# Option B — single command
./start.sh
```

Open [http://localhost:3000](http://localhost:3000)

---

## 📂 How to Use

### 1. Upload your data
Drag a `.csv`, `.xlsx`, or `.xls` file onto the upload zone. ada auto-profiles it and shows column types and sample values.

### 2. Add a context file *(recommended)*
Upload a `context.yaml` (or `.txt` / `.json`) that describes what your columns mean. ada checks coverage and asks about any columns it doesn't understand.

See [`docs/context-format.md`](docs/context-format.md) for the full YAML spec and examples.

### 3. Ask questions
Type any natural language question about your data. ada will clarify if needed, show its plan, and render the result inline with a chart and a plain-English summary.

### 4. Correct and refine
Click **✏️ Correct this** on any response to refine it. Your corrections are remembered for the rest of the session — and optionally saved for future sessions.

---

## 🗂️ Project Structure

```
ada/
├── backend/              # FastAPI Python backend
│   ├── core/             # Orchestrator, uncertainty scorer, HITL, session state
│   ├── agents/           # Planning, pandas, visualization, summary, correction agents
│   ├── knowledge/        # Context parser, semantic layer, ChromaDB, memory
│   └── execution/        # Sandbox (Mode A) and Security Agent (Mode B)
├── frontend/             # Next.js React frontend
│   ├── app/              # Pages (chat, settings)
│   └── components/       # ChatPanel, ResultsCard, PlanCard, CorrectionInline, etc.
└── data/                 # Local SQLite + ChromaDB (auto-created, gitignored)
```

---

## ⚙️ Configuration

Key `.env` options:

```bash
LLM_PROVIDER=openrouter          # openrouter | azure
OPENROUTER_API_KEY=sk-or-...
OPENROUTER_MODEL_PLANNING=anthropic/claude-3.5-sonnet
OPENROUTER_MODEL_FAST=meta-llama/llama-3.1-8b-instruct

EXECUTION_MODE=sandbox           # sandbox | agent_review
UNCERTAINTY_THRESHOLD=0.95
```

Full reference in [`.env.example`](.env.example).

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 14, React 18, Tailwind CSS, shadcn/ui |
| Charts | Plotly.js via react-plotly.js |
| Tables | TanStack Table |
| Backend | FastAPI, Python 3.10+ |
| Data | pandas, numpy, openpyxl |
| LLM APIs | OpenRouter (primary), Azure OpenAI (secondary) |
| Vector store | ChromaDB (embedded, no server) |
| Embeddings | sentence-transformers / all-MiniLM-L6-v2 (offline) |
| Code sandbox | Docker/Podman (Mode A) · RestrictedPython + LLM (Mode B) |
| Storage | SQLite (audit log + memory) |

---

## 🗺️ Roadmap

- [x] PRD v2.0 complete
- [ ] Phase 1 — Foundation (FastAPI + Next.js shell, file upload, auto-profiler)
- [ ] Phase 2 — Context layer (YAML parser, ChromaDB, coverage validator)
- [ ] Phase 3 — NL-to-analysis core (Planning Agent, Pandas Agent, results display)
- [ ] Phase 4 — Uncertainty & HITL (confidence scorer, clarification cards)
- [ ] Phase 5 — Dual-mode execution (sandbox + security agent)
- [ ] Phase 6 — Correction system (Correction Agent, threaded UI, memory update)
- [ ] Phase 7 — Visualisation & polish (Plotly auto-selection, cross-session memory)
- [ ] Phase 8 — Hardening (prompt tuning, large file testing, docs)

---

## 🔐 Privacy

- **Your data files never leave your machine.** Only the natural language query and column schema metadata (names and types — not values) are sent to LLM APIs.
- All analysis runs locally via pandas.
- API keys live in `.env` only, never in logs or code.
- The audit log (`./data/audit.db`) is local SQLite — readable with any standard tool.

---

## 📄 License

MIT — see [LICENSE](LICENSE)

---

## 🙋 About

A personal productivity tool. Designed around the idea that a good AI analyst should behave like a thoughtful junior consultant — not a magic 8-ball.

The architecture and design decisions are documented in full in [`docs/PRD.md`](docs/PRD.md).
