# spec-to-playwright

> **Archived (read only).** A 2024 prototype, kept for reference. It is not maintained,
> dependencies are pinned to mid-2024 versions, and it is unlikely to run as-is without
> adjustment. Originally named `RandomTempProj`.

A LangGraph RAG agent that turns a **software requirements document** into a **test suite**.
You upload a requirements PDF and it walks a fixed pipeline: extract the requirements, generate
test scenarios, model them as a statechart, build a traceability matrix, and emit runnable
Playwright scripts.

## What it does

```
Requirements PDF
      │  ingest → split → embed → ChromaDB
      ▼
Self-RAG retrieval (with grading + Google-search fallback)
      │
      ▼   five generation stages
1. Extract software requirements
2. Generate test scenarios
3. Create an XState statechart for the scenarios
4. Build a traceability matrix (requirement → scenario)
5. Generate Playwright scripts (Page Object Model)
```

Retrieval is graded along the way: a **retrieval grader** checks relevance, a **hallucination
grader** checks the answer is grounded in the retrieved context, an **answer grader** checks it
addresses the question, and a **Google search** tool is the fallback when local retrieval is weak.

## Stack

- **Orchestration:** LangChain + LangGraph (0.2 / 0.1)
- **LLM + embeddings:** Ollama (`deepseek-coder-v2`, `mxbai-embed-large` / Nomic) — local, no cloud key needed to run
- **Vector store:** ChromaDB
- **Ingestion:** `pypdf`, LangChain text splitters
- **UI:** Streamlit chat
- **Output target:** Playwright (Page Object Model)
- **Evaluation:** `evaluate.py` (OpenAI-keyed, optional)

## Layout

| File | Role |
|------|------|
| `app.py` | Streamlit entry point (chat UI) |
| `Workflow.py` | the LangGraph pipeline (the five stages above) |
| `agent.py` / `rag_app.py` | RAG chains + the graders |
| `document_loader.py`, `embeddings.py`, `vectorstore_retriever.py` | ingest → embed → retrieve |
| `prompts.yaml` | the stage and grader prompts |
| `application.yaml` | models, collection, API keys (blank in the repo) |
| `evaluate.py` | offline evaluation |
| `site/` | a tiny sample web app + its `requirements.md`, used as example input |
| `Dockerfile`, `docker-compose.yml` | containerized run against a host Ollama |

## Running it (historical)

```bash
pip install -r requirements.txt
# fill models / keys in application.yaml, have an Ollama daemon running
streamlit run app.py        # or: docker compose up
```

Then upload a requirements PDF (see `site/requirements.md` for the kind of input it expects).

## Status

Archived prototype. Kept read only as a snapshot of the idea: generating a test suite from a
requirements spec with a self-grading RAG agent. No support, no updates.
