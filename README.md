# NaTGen: Self-Healing LLM Generation Pipeline

> **Compiler-style Software Generation System**: Natural Language → Structured Config → Pydantic Validation → Surgical Field Repair → Executable 3-Tier Flask Application.

NaTGen is a multi-stage software generator that converts natural language product requests into strict, validated application schemas (`AppSchema`) and compiles them into runnable, standalone 3-tier web applications (Flask + SQLAlchemy + SQLite + HTML UI) with zero LLM calls at runtime.

---

## 🌟 Key Architecture & Capabilities

```
User Prompt
    │
    ▼
[Stage 1] Intent Extraction (intent_extraction.j2)
    │
    ▼
[Stage 2] Schema Generation (schema_generation.j2 → JSON Schema / Few-Shot)
    │
    ▼
[Stage 3] Pydantic Contract Validation (schemas.py)
    │
    ├─ ✅ PASS ─────────┐
    │                   │
    └─ ❌ FAIL          │
        ▼               │
  [Stage 4] Surgical Field Repair (repair.py + refinement.j2)
        │               │
        └─ Retry (3x) ──┘
                        │
                        ▼
          [Deterministic Code Generator] (runtime/generator.py)
                        │
                        ▼
          Standalone Executable App (Flask + SQLite)
```

1. **Strict Type Contracts (`schemas.py`)**: Uses Pydantic models for UI, API endpoints, database tables, and auth permissions with auto-normalizing field validators (`"date"` → `"datetime"`, `"int"` → `"integer"`, `"text"` → `"string"`).
2. **Surgical Field-Level Repair (`repair.py`)**: Parses exact `ValidationError` paths (`loc`) and re-prompts the LLM to fix **only** the broken fields, preserving valid output instead of executing full pipeline retries.
3. **Deterministic 3-Tier Code Generator (`runtime/generator.py`)**: Compiles validated schemas into executable Flask applications with live SQLite database CRUD operations (`db.session.add`, `db.session.commit`) and interactive frontend UI elements.
4. **100% Evaluation Reliability**: Tested against 20 benchmark prompts (10 real-world + 10 adversarial edge cases) with a **100% success rate**.

---

## 📁 Repository Structure

```
D:\SELF-HEALING-LLM\
├── app.py                  # Main Web Demo server (Flask, port 5000)
├── pipeline.py             # 4-Stage orchestrator (Intent, Schema, Validate, Repair)
├── repair.py               # Surgical field-level error parser & reprompt engine
├── schemas.py              # Pydantic contract definitions & type normalizers
├── requirements.txt        # Python dependencies
├── .env                    # Environment variables (GROQ_API_KEY)
├── .gitignore              # Protects secret keys & build artifacts
│
├── prompts/                # Jinja2 Prompt Templates
│   ├── intent_extraction.j2
│   ├── schema_generation.j2
│   └── refinement.j2
│
├── runtime/                # Code Generator Engine
│   ├── generator.py        # Compiles AppSchema → Flask/SQLAlchemy project
│   └── templates/          # Code generation templates (app.py, models.py, HTML)
│       ├── app_py.j2
│       ├── models_py.j2
│       ├── base_html.j2
│       └── page_html.j2
│
├── evaluation/             # Evaluation Harness Benchmark
│   ├── prompts.json        # 20 benchmark prompts (10 real-world + 10 adversarial)
│   ├── harness.py          # Benchmarking script (tracks SR, retries, latency)
│   └── results.json        # Detailed evaluation metrics output
│
├── templates/
│   └── index.html          # Dark-themed Web UI frontend
│
└── generated_apps/         # Output directory for generated executable applications
```

---

## 🚀 Quick Start & Installation

### 1. Prerequisites
- Python 3.10+ or Anaconda Python
- A free API key from [Groq Console](https://console.groq.com/keys) or OpenAI / Gemini

### 2. Install Dependencies
```bash
git clone <repository-url>
cd SELF-HEALING-LLM
pip install -r requirements.txt
```

### 3. Configure API Key
Create a `.env` file in the root directory:
```env
GROQ_API_KEY=gsk_your_groq_api_key_here
```

---

## 💻 How to Use

### 1. Launch the Live Web Demo
Start the Web UI server:
```bash
python app.py
```
Open **`http://localhost:5000`** in your browser.

1. Type an open-ended app prompt (e.g. *"Build a CRM with contacts, deals pipeline, and role-based access"*).
2. Click **⚡ Generate Schema** to inspect the live 4-stage pipeline execution and output JSON.
3. Click **⚙ Generate App** to compile the schema into a working Flask project.

---

### 2. Run a Generated Application
Navigate to the generated app directory and launch it:
```bash
cd generated_apps/e_commerce_app
python app.py
```
Open **`http://localhost:5001`** in your browser.
- **Database**: SQLite database automatically created at `app.db`.
- **Backend API**: Live Flask REST endpoints with SQLAlchemy ORM persistence.
- **Frontend UI**: Interactive forms with `fetch()` posting directly to the backend database.

---

### 3. Run the Evaluation Benchmark
Run the automated evaluation suite across 20 real-world and adversarial prompts:
```bash
python evaluation/harness.py
```

---

## 📊 Evaluation Results

| Metric | Benchmark Result | Target | Status |
|---|---|---|---|
| **Total Test Prompts** | **20** | 20 | ✅ |
| **Overall Success Rate** | **100.0%** (20/20) | ≥90.0% | ✅ |
| **Real-World Success Rate** | **100.0%** (10/10) | ≥90.0% | ✅ |
| **Adversarial Success Rate** | **100.0%** (10/10) | ≥80.0% | ✅ |
| **Avg Retries / Request** | **0.20** | <1.00 | ✅ |
| **Avg Pipeline Latency** | **23.62s** | <30.00s | ✅ |

---

## ⚖️ System Architecture & Tradeoffs

1. **Compiler Scaffolding vs. Raw Code Synthesis**:
   - We chose a **Compiler + Scaffolding Architecture** over raw code generation. By constraining the LLM to output a validated schema (`AppSchema`) and compiling via Jinja2 templates, we achieve **100% execution syntax safety** and **zero runtime LLM cost**, while standardizing business logic to 3-tier CRUD patterns.
2. **LLM Provider Tradeoff**:
   - Using Groq (`llama-3.1-8b-instant`) offers ultra-low latency (~2-3s per stage) and free execution, while Pydantic normalization handles smaller model variances cleanly.

---

## 📜 License
MIT License
