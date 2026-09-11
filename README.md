# 🛰️ Intern Sentinel

An automated, event-driven pipeline and AI agent designed to scrape, filter, and semantically evaluate software engineering and data internship opportunities from tech companies (e.g., Amazon Jobs, Google Careers) and dispatch real-time alerts via Discord.

---

## 🎯 Motivation & Architecture

Finding relevant internship positions often requires manual, repetitive checks across dozens of career portals. This project automates the entire ingestion-to-notification lifecycle with an emphasis on **resilient data engineering** and **cost-efficient LLM inference**.

### Pipeline Flow

```text
[Career Portals / APIs]
           │
           ▼
     [Async Ingestion (httpx)]
           │
           ▼
     [Data Validation (Pydantic v2)]
           │
           ▼
     [State & Deduplication (SQLite / SQLAlchemy)]
           │
    (Is it a new job?)
     ├── No  ──► [Skip / Discard]
     └── Yes ──► [Deterministic Hard Filter]
                       │
             (Passes basic requirements?)
              ├── No  ──► [Mark as Skipped]
              └── Yes ──► [Semantic Evaluation (LLM Structured Outputs)]
                                │
                      (Score >= Threshold?)
                       ├── No  ──► [Save Evaluation]
                       └── Yes ──► [Discord Dispatcher (discord.py)]
```

---

## ✨ Key Engineering Highlights

* **Zero-Token-Waste Architecture:** Applies strict boolean filtering (graduation window, keywords, seniority exclusions) *before* invoking the LLM, cutting unnecessary API token costs.
* **Schema Enforcement:** Strict typing across the boundary using **Pydantic v2** for raw ingestion normalization and LLM Structured Outputs.
* **Asynchronous I/O:** High-throughput network requests using `httpx` and `asyncio`.
* **Idempotent State Store:** Local SQLite database managed via SQLAlchemy 2.0 to track processed job postings and eliminate duplicate notifications.
* **Headless CI/CD Automation:** Scheduled cron triggers via **GitHub Actions** for zero-cost serverless execution.
* **Real-time Dispatch:** Rich-formatted Discord embeds dispatched via `discord.py`.

---

## 🛠️ Tech Stack

| Layer | Technology |
| :--- | :--- |
| **Language** | Python 3.11+ |
| **Validation & Schema** | Pydantic v2 |
| **HTTP Client** | `httpx` (async) |
| **Persistence** | SQLite + SQLAlchemy 2.0 |
| **AI / Evaluation** | Google GenAI SDK / OpenAI (Structured Outputs) |
| **Alerting** | `discord.py` |
| **Automation** | GitHub Actions (Cron) |

---

## 🚀 Getting Started

### Prerequisites

* Python 3.11 or higher
* A Discord Bot Token and target Channel ID
* An API key for Gemini or OpenAI

### 1. Clone the repository

```bash
git clone https://github.com/Heitorab06/job-scout-agent.git
cd job-scout-agent
```

### 2. Set up virtual environment

```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -r requirements.txt
pip install -e .
```

### 3. Configure environment variables

Create a `.env` file in the root directory:

```env
DISCORD_BOT_TOKEN="your_discord_bot_token"
DISCORD_CHANNEL_ID="your_channel_id"
GEMINI_API_KEY="your_api_key_here"
MIN_FIT_SCORE=75
```

### 4. Run the pipeline

```bash
python -m src.main
```

---

## 🗺️ Roadmap

- [ ] **Phase 1:** Core Pydantic schemas (`RawJob`, `JobEvaluation`).
- [ ] **Phase 2:** Async scraper client for Amazon Jobs internal search endpoint.
- [ ] **Phase 3:** SQLite repository for deduplication and posting lifecycle.
- [ ] **Phase 4:** LLM evaluator with structured JSON outputs.
- [ ] **Phase 5:** Discord bot integration (`discord.py`) with rich embeds.
- [ ] **Phase 6:** GitHub Actions workflow with scheduled cron executions.

---

## 📄 License

MIT License. Feel free to use and adapt.