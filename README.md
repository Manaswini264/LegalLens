# LegalLens
# LegalLens Civil — Working student prototype

LegalLens Civil is a local-first web application for exploring civil-document summarization and source-grounded document retrieval. It is an educational prototype, not legal advice or a production legal service.

## Features
- Responsive HTML/CSS/JavaScript frontend.
- FastAPI backend with health, stats, upload, list, view, analyze, search, and delete endpoints.
- Local SQLite storage for uploaded documents and analysis history.
- TXT, Markdown, CSV, text-based PDF, and DOCX extraction.
- Civil-document categories: property dispute, contract dispute, lease/tenancy, civil judgment, plaint/written statement, legal notice, and affidavit/other civil document.
- Optional Ollama model summarization; if unavailable, an extractive fallback works without a local LLM.
- Evidence excerpts labelled with source paragraph identifiers and PDF page numbers where detectable.
- Keyword-overlap case/document finder with match terms and evidence snippets.
- Duplicate document detection by SHA-256 content hash.
- Three clearly labelled synthetic civil sample documents.

## Important implementation status
This version is a functioning MVP, not the full proposed research architecture. It currently uses SQLite and lexical keyword-overlap retrieval. It does **not** implement FAISS vector search, a complete LangChain RAG pipeline, PostgreSQL, OCR for scanned PDFs, verified claim-by-claim citations, user accounts, or production security. Search scores are lexical match indicators, not legal relevance or precedent authority. Ollama summaries are model-generated and must be verified. The fallback is extractive and may still omit context.

## Run locally on Windows
1. Install Python 3.10 or newer.
2. Extract this folder.
3. Open CMD inside the `LegalLens_Civil` folder (File Explorer address bar → type `cmd` → Enter).
4. Run each command separately:

```bat
py -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
uvicorn app:app --reload
```

Open <http://127.0.0.1:8000>. API documentation: <http://127.0.0.1:8000/docs>.

## Optional Llama 3 summaries with Ollama
1. Install Ollama from <https://ollama.com/>.
2. In a separate terminal, run `ollama pull llama3` once.
3. Start/keep Ollama running. Restart LegalLens if needed.
4. `/api/health` reports whether Ollama is reachable. Without Ollama, the extractive fallback remains available.

Ollama is optional and runs on the same computer by default. A cloud-hosted web service cannot reach your personal computer's localhost Ollama service. Do not upload confidential legal or client records to a demo deployment.

## API routes
- `GET /api/health` — backend and optional Ollama status
- `GET /api/stats` — counts
- `GET /api/documents` — list documents; optional `?doc_type=Property%20dispute`
- `POST /api/upload` — multipart fields: `file`, optional `title`, `doc_type`
- `GET /api/document/{id}` — document details and latest saved analysis
- `GET /api/document/{id}/text` — source segments
- `POST /api/summarize/{id}` — generate and save summary
- `POST /api/search` — JSON body e.g. `{"query":"property ownership","doc_type":"All civil documents","limit":5}`
- `DELETE /api/document/{id}` — delete document and linked analysis history

## Deploy to Render (optional)
The repository includes `render.yaml`. Push this folder's contents to a GitHub repository, connect the repository in Render, and deploy as a web service. The included Render configuration uses a persistent disk for SQLite if the selected plan supports it. If no persistent disk is available, local database changes may be lost during redeploy/restart. For production, use a managed database and add authentication, access controls, encryption, rate limiting, and privacy/legal review.

## Research and evaluation direction
To develop the research version, add civil-specific labelled test data; hybrid lexical + embedding retrieval (e.g. FAISS); source-claim alignment checks; legal metadata filters; human review; and measured evaluation of factual consistency, source support, summary coverage, retrieval precision/recall, and latency. Do not claim hallucination reduction without a baseline and documented evaluation.

## Privacy and legal notice
The sample records are fictional. Do not upload confidential client records or sensitive personal information. This prototype does not determine legal rights, give legal advice, or establish that a retrieved case is binding precedent.
