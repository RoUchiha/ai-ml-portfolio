# AI & ML Engineering Portfolio

**Roshaan Singh** — AI/ML Engineer

A collection of production-grade projects built around LLM evaluation, safety, reliability, and cost engineering. Each project solves a real problem that teams running LLMs in production face — and each one was built from scratch, not scaffolded from a tutorial.

> Built as applied capstone work from the **[UT Austin AI & Machine Learning](https://onlineexeced.mccombs.utexas.edu/online-ai-machine-learning-course)** program (McCombs School of Business, 23-week executive program).

---

## The Projects

### 1. [Hallucination Detector](https://github.com/RoUchiha/hallucination-detector)
> **What**: Detects factual hallucinations in LLM-generated text using a dual-scoring pipeline — NLI model first, LLM judge second.
>
> **Why it matters**: Every RAG system, AI summarizer, and LLM-powered product ships hallucinations. This pipeline catches them before they reach users.
>
> **Technical highlights**: DeBERTa cross-encoder NLI with chunked source retrieval · LLM escalation only for low-confidence claims (minimizes API cost) · Pydantic-typed pipeline · rich console + JSON + HTML output
>
> **Course modules**: Course 03 (responsible AI, RAG chunking) · HuggingFace transformers

---

### 2. [RAG Evaluation Framework](https://github.com/RoUchiha/rag-eval-framework)
> **What**: A modular harness for evaluating any RAG pipeline using RAGAS metrics — faithfulness, answer relevancy, context recall, context precision.
>
> **Why it matters**: Building a RAG pipeline is table stakes now. Knowing whether it actually works — and which component is failing — is the hard part. This framework answers that.
>
> **Technical highlights**: Abstract `RAGPipeline` base class (swap any RAG system in 5 lines) · RAGAS metrics wrapped in pandas DataFrame · Streamlit dashboard with Plotly heatmaps · CSV/JSON export
>
> **Course modules**: Course 03 (RAG pipelines) · Course 05 (Streamlit deployment) · LangChain · HuggingFace datasets

---

### 3. [G-Eval Judge](https://github.com/RoUchiha/geval-judge)
> **What**: A YAML-driven LLM-as-judge framework implementing G-Eval (chain-of-thought scoring rubrics). Define evaluation criteria in YAML, score any LLM output on any dimension.
>
> **Why it matters**: Rule-based metrics (BLEU, ROUGE) don't capture what makes an LLM response actually *good*. G-Eval correlates significantly better with human judgment. This makes it reusable across any product domain.
>
> **Technical highlights**: Jinja2 CoT prompt builder · `asyncio.gather` parallelism (N dimensions = 1× latency) · Weighted composite score normalized 0–1 · Batch JSONL evaluation with progress bar
>
> **Course modules**: Course 03 (prompt engineering) · Course 04 (agentic reasoning, async execution)

---

### 4. [Prompt Regression Testing](https://github.com/RoUchiha/prompt-regression)
> **What**: A CI/CD-integrated test framework for LLM prompts. Define test cases in YAML, assert on output properties (length, content, format, latency, semantic quality), gate merges when prompts regress.
>
> **Why it matters**: Prompts drift. A small wording change can break downstream behavior silently. This framework applies software engineering's regression testing discipline to prompt maintenance.
>
> **Technical highlights**: 7 assertion types (max_length, contains, regex, json_schema, llm_judge, latency_ms, and more) · Baseline drift detection across runs · JUnit XML output for GitHub Actions · Exit code 1 on failure (CI gating)
>
> **Course modules**: Course 03 (prompt engineering) · Course 05 (CI/CD deployment)

---

### 5. [LLM Red-Teaming Harness](https://github.com/RoUchiha/llm-red-teaming)
> **What**: An automated safety evaluation framework that probes LLM endpoints with adversarial attack templates across 5 categories — jailbreak, prompt injection, data extraction, bias probing, and hallucination induction.
>
> **Why it matters**: Safety evaluation at scale requires automation. Human red-teamers can test dozens of scenarios per hour; this framework runs hundreds with structured reporting and repeatable methodology.
>
> **Technical highlights**: YAML-driven attack library (13+ templates, 5 categories, 3 severity levels) · Dual-layer classifier (keyword heuristics + LLM judge, ~70% API cost reduction) · GREEN/YELLOW/RED risk tiers by category
>
> **Course modules**: Course 03 (responsible AI) · Course 04 (agentic automation, tool-use reasoning)

---

### 6. [Text-to-SQL with AST Guardrails](https://github.com/RoUchiha/txt2sql-guardrails)
> **What**: Natural language → SQL with a hard guardrail layer that **statically blocks destructive queries before they execute**. The guard analyzes the parsed SQL **AST (via sqlglot, never regex)**, runs **independently of the LLM**, defaults to read-only, and executes in a row/time-capped sandbox.
>
> **Why it matters**: The moment you let an LLM write SQL against a real database, a jailbroken or confused generation can `DROP`, `DELETE`, or exfiltrate data. This layer guarantees that can't happen — a malicious generation is blocked at the AST before anything touches the DB.
>
> **Technical highlights**: Layered defense (parse-or-reject · single-statement · SELECT-only allowlist · full-AST action-node walk · schema identifier validation · read-only sandbox) · **100% block rate on a 26-case adversarial corpus, enforced by a test** · 66 tests / 96% coverage · Typer CLI (`query` / `explain` / `audit`)
>
> **🛡️ [Live demo](https://huggingface.co/spaces/rosingh/txt2sql-guardrails)** — paste `SELECT * FROM users; DROP TABLE users` and watch it get blocked.

---

### 7. [LLM Cost Router](https://github.com/RoUchiha/cost-router)
> **What**: Routes each LLM request to the **cheapest model that can satisfy it** — a heuristic complexity classifier picks a tier, an optional verify step escalates only when quality is low, and honest telemetry reports savings against a frontier-only baseline. Ships an OpenAI-compatible FastAPI proxy.
>
> **Why it matters**: Always calling a frontier model is the default — and it's how teams overspend 2–5×. Most requests are trivial. Routing them to a cheaper tier cuts 40–60% of spend with no quality loss on the work that doesn't need the big model.
>
> **Technical highlights**: Explainable heuristic classifier (87.5% accuracy on a labeled set) · tiered routing with single-escalation fallback · counterfactual savings accounting (escalations counted honestly) · OpenAI-compatible proxy with `X-Router-*` headers · 26 tests / 91% coverage · `tiktoken` cost estimation
>
> **💸 [Live demo](https://huggingface.co/spaces/rosingh/cost-router)** — route a prompt and watch the tier + savings vs frontier-only.

---

## Skill Map

```
LLM APIs & SDKs          ████████████████████  Anthropic SDK, async client, structured output
Prompt Engineering        ████████████████████  CoT prompting, Jinja2 templates, rubric design
RAG Architecture          ████████████████████  chunking, retrieval, evaluation (RAGAS)
LLM Evaluation            ████████████████████  NLI scoring, LLM-as-judge, G-Eval, RAGAS
AI Safety                 ████████████████████  red-teaming, hallucination detection, responsible AI
MLOps / CI-CD             ████████████████████  GitHub Actions, JUnit XML, baseline diffing
Python / Data             ████████████████████  Pydantic v2, pandas, asyncio, pytest, typer, rich
Deployment                ████████████████████  Streamlit, FastAPI, Gradio on HF Spaces, Docker-ready
```

---

## Course Context

All projects were built as applied work from the **[UT Austin AI & Machine Learning](https://onlineexeced.mccombs.utexas.edu/online-ai-machine-learning-course)** program — a 23-week executive program through McCombs School of Business covering:

| Course | Topics |
|--------|--------|
| Pre-Work | Python for AI, Generative AI & Agentic AI landscape |
| Course 01 | Python for AI Solutions, data manipulation |
| Course 02 | Predictive modeling, ML, neural networks |
| Course 03 | Generative AI for NLP: LLMs, prompt engineering, RAG, responsible AI |
| Course 04 | Agentic AI: single/multi-agent systems, memory, tool use |
| Course 05 | Deploying AI Solutions: production workflows, web app integration |

Each project in this portfolio directly extends the curriculum — taking concepts taught in class and building production-grade tooling around them.

---

## Running Any Project

Each project is self-contained with its own `pyproject.toml`:

```bash
git clone https://github.com/RoUchiha/<repo-name>
cd <repo-name>
pip install -e ".[dev]"
export ANTHROPIC_API_KEY=sk-ant-...
pytest          # all tests pass without an API key (LLM calls are mocked)
```

See each project's `README.md` for full usage and `CLAUDE.md` for architecture context.

---

## Contact

**GitHub**: [github.com/RoUchiha](https://github.com/RoUchiha)
**Email**: swaggsikh@gmail.com
