# AI & ML Engineering Portfolio

**Roshaan Singh** — AI/ML Engineer

A collection of five production-grade projects built around LLM evaluation, safety, and reliability engineering. Each project solves a real problem that teams running LLMs in production face — and each one was built from scratch, not scaffolded from a tutorial.

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

## Skill Map

```
LLM APIs & SDKs          ████████████████████  Anthropic SDK, async client, structured output
Prompt Engineering        ████████████████████  CoT prompting, Jinja2 templates, rubric design
RAG Architecture          ████████████████████  chunking, retrieval, evaluation (RAGAS)
LLM Evaluation            ████████████████████  NLI scoring, LLM-as-judge, G-Eval, RAGAS
AI Safety                 ████████████████████  red-teaming, hallucination detection, responsible AI
MLOps / CI-CD             ████████████████████  GitHub Actions, JUnit XML, baseline diffing
Python / Data             ████████████████████  Pydantic v2, pandas, asyncio, pytest, typer, rich
Deployment                ████████████████████  Streamlit, FastAPI, Docker-ready packaging
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
