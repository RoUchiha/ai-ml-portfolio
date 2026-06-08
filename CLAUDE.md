# CLAUDE.md — AI & ML Portfolio (Overarching)

This is the root context file for Roshaan Singh's AI/ML engineering portfolio. Read this before working on any project in this collection.

---

## Portfolio Overview

Five production-grade Python projects covering LLM evaluation, safety, and reliability engineering. Each lives in its own GitHub repo (listed below) and is included here as a git submodule.

| Submodule | Repo | Purpose |
|-----------|------|---------|
| `hallucination-detector/` | [RoUchiha/hallucination-detector](https://github.com/RoUchiha/hallucination-detector) | NLI + LLM dual-scoring hallucination detection |
| `rag-eval-framework/` | [RoUchiha/rag-eval-framework](https://github.com/RoUchiha/rag-eval-framework) | RAGAS-powered RAG pipeline evaluation + Streamlit dashboard |
| `geval-judge/` | [RoUchiha/geval-judge](https://github.com/RoUchiha/geval-judge) | YAML-driven LLM-as-judge with CoT rubrics |
| `prompt-regression/` | [RoUchiha/prompt-regression](https://github.com/RoUchiha/prompt-regression) | CI/CD prompt regression testing with 7 assertion types |
| `llm-red-teaming/` | [RoUchiha/llm-red-teaming](https://github.com/RoUchiha/llm-red-teaming) | Automated LLM safety red-teaming harness |

---

## Shared Conventions Across All Projects

- **Python 3.11+**, `pyproject.toml` with `hatchling` build backend
- **`pip install -e ".[dev]"`** installs everything including `pytest`
- **`ANTHROPIC_API_KEY`** env var used everywhere; all LLM calls are mocked in tests
- **Pydantic v2** for all data models — `model_dump_json()`, `model_copy()`, etc.
- **`typer`** for CLIs, **`rich`** for console output
- **Tests pass without any API key** — all external calls mocked with `unittest.mock`
- Each project has its own **`CLAUDE.md`** with full architecture context

---

## Working With Submodules

```bash
# Clone everything including submodules
git clone --recurse-submodules https://github.com/RoUchiha/ai-ml-portfolio

# If already cloned without submodules
git submodule update --init --recursive

# Update all submodules to their latest commits
git submodule update --remote --merge
```

---

## Project Relationships

These projects form a natural evaluation stack for LLM-powered applications:

```
Build your LLM app
        │
        ├─► hallucination-detector   ← Is the output factually grounded?
        │
        ├─► rag-eval-framework       ← Is the RAG pipeline retrieving + answering well?
        │
        ├─► geval-judge              ← Does the output meet your quality rubric?
        │
        ├─► prompt-regression        ← Did your last prompt change break anything?
        │
        └─► llm-red-teaming          ← Is the model safe to deploy?
```

A mature LLM application team would run all five. Each catches a different class of problem.

---

## Academic Context

All projects built during the **UT Austin AI & Machine Learning** program (McCombs School of Business, 23-week executive program). See the [course page](https://onlineexeced.mccombs.utexas.edu/online-ai-machine-learning-course) for curriculum details.

Relevant modules per project:
- **Course 03 — Generative AI for NLP**: hallucination-detector, rag-eval-framework, geval-judge, prompt-regression, llm-red-teaming
- **Course 04 — Agentic AI**: geval-judge (async parallel execution), llm-red-teaming (automated multi-step workflows)
- **Course 05 — Deploying AI Solutions**: rag-eval-framework (Streamlit), prompt-regression (GitHub Actions CI/CD)

---

## Owner

**Roshaan Singh** · [github.com/RoUchiha](https://github.com/RoUchiha) · swaggsikh@gmail.com
