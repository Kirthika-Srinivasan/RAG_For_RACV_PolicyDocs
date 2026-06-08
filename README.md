# RACV Policy Documents RAG (Retrieval-Augmented Generation)

An intelligent Retrieval-Augmented Generation (RAG) system to parse, index, and answer questions over RACV (Royal Automobile Club of Victoria) policy and roadside assistance documents.

This repository implements a reproducible pipeline for: parsing PDFs into structured markdown, chunking content while preserving tables and headers, generating vector embeddings, persisting vectors in a ChromaDB store, and executing RAG queries with an LLM (examples use Anthropic/Claude).

**Quick Links**
- Code: [main.py](main.py)
- Notebooks: [RAG/02-pdf_loader.ipynb](RAG/02-pdf_loader.ipynb), [RAG/01-document.ipynb](RAG/01-document.ipynb)
- Data: `data/pdfs/`, `data/vector_store/`

**Quick Start**

Prerequisites: Python 3.10+, Git, virtualenv/venv. GPU optional for faster embeddings.

1. Create and activate a virtual environment (PowerShell example):

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

2. Install dependencies:

```powershell
pip install -r requirements.txt
```

3. Add API keys to a `.env` file (example keys used in notebooks):

- `CLAUDE_API_KEY`
- `LLAMA_CLOUD_API_KEY`
- `LANGSMITH_API_KEY`

4. Run ingestion and indexing: open [RAG/02-pdf_loader.ipynb](RAG/02-pdf_loader.ipynb) and run the cells to parse PDFs and persist the vector store to `data/vector_store`.

5. Query examples: use `rag_simple`, `rag_advanced` or `AdvancedRAGPipeline` in the notebooks to retrieve answers with citations.

**Architecture (Data Flow)**

```mermaid
flowchart LR
	A[PDF files (data/pdfs)] --> B[LlamaParse (markdown)]
	B --> C[Markdown chunking (MarkdownNodeParser)]
	C --> D[EmbeddingManager (SentenceTransformer)]
	D --> E[ChromaDB VectorStore (data/vector_store)]
	E --> F[RAGRetriever]
	F --> G[LLM (Claude / Anthropic)]
	G --> H[Answer + Citations]
```

**Recent additions / changelog (summary of your work)**

- Data ingestion: PDF parsing with `LlamaParse` using a strict system prompt to preserve tables, quantities, and structure.
- Chunking: `MarkdownNodeParser` to produce RAG chunks that keep markdown tables and headers intact.
- Embeddings: `EmbeddingManager` using `sentence-transformers` (default: `nomic-ai/nomic-embed-text-v1.5`) and `generate_embeddings()` helper.
- Vector store: `VectorStore` wrapper for ChromaDB persistent client; stores embeddings and metadata to `data/vector_store` (includes `chroma.sqlite3`).
- Retriever: `RAGRetriever` with similarity score conversion and threshold filtering.
- RAG pipelines: `rag_simple`, `rag_advanced` (LangSmith-traceable), and `AdvancedRAGPipeline` with streaming simulation, citations, summarization, and history.
- Tracing: LangSmith tracing enabled via environment variables for reproducible traces.

**Files of interest**

- [RAG/02-pdf_loader.ipynb](RAG/02-pdf_loader.ipynb) — ingestion, chunking, embedding, vector store initialization, retriever, and RAG examples.
- [main.py](main.py) — (if present) project entry points and helpers.
- `data/vector_store/` — persistent ChromaDB files.

**Notes & next steps**

- Keep API keys out of source control; use `.env` or secure secret management.
- Consider adding `CONTRIBUTING.md` and `CHANGELOG.md` for collaborative work.
- If you want a packaged CLI or a lightweight API server for queries, I can scaffold that next.

---

If you'd like any wording changes, more file references, or a different diagram style, tell me and I'll update before pushing.
