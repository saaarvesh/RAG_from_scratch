# RAG — Retrieval-Augmented Generation Project

This repository contains a Retrieval-Augmented Generation (RAG) application implemented from scratch. It includes custom chunking strategies, an end-to-end RAG pipeline, integration with Supabase + pgvector for vector search, and evaluation tooling.

The project was built to experiment with and compare different chunking methods and to produce a complete, production-ready RAG flow that can be adapted to multiple LLMs and vector stores.

## Key features

- Multiple chunking strategies implemented from scratch:
  - Fixed-size chunking (simple sliding windows / fixed token/character count)
  - Semantic chunking (using semantic boundaries / sentence or paragraph-aware segmentation)
  - Recursive chunking (split-and-merge to respect context boundaries and maximum chunk size)
  - LLM-based chunking (using an LLM to decide logical chunk boundaries or to rewrite/summarize before embedding)
- Full RAG pipeline from ingestion to answering:
  - Ingestion scripts to read and preprocess source documents
  - Chunk creation and embedding generation
  - Storage of embeddings and metadata in Supabase (pgvector)
  - Vector search (kNN) to retrieve relevant chunks at query time
  - Prompt assembly (retrieved context + system prompt + user query)
  - LLM inference to produce final answers with citations
  - Frontend support for clickable citations (see webApp/rag-chat)
- Evaluation and metrics:
  - Automatic evaluation of retrieval quality and end-to-end RAG answers
  - Comparison of chunking strategies to measure retrieval relevance and downstream answer quality

## Architecture & Flow

High level flow (see `diagram/flow.tex` for the TikZ diagram source):

1. User query
2. Query embedding (example model: BAAI/bge-small-en)
3. Vector search in Supabase + pgvector
4. Similarity check / decision threshold
	- If similarity > threshold (e.g. 0.3): retrieve top-k chunks and assemble prompt with context
	- Else: fallback to general knowledge / LLM-only answer
5. Send assembled prompt to LLM (example used: Gemini 2.5 Flash, but the pipeline is model-agnostic)
6. LLM returns answer + citations
7. Frontend displays the answer with clickable citations linking back to original documents/chunks

This project implements the entire flow in `webApp/` and ingestion helpers in the repository root. The `diagram/flow.tex` produces the flow diagram used above.

## Tech stack

- Python: ingestion, chunking, embedding generation scripts (see `ingest.py`, `ingest_open.py`, `test_embeddings.py`)
- Supabase + PostgreSQL + pgvector: vector storage and vector search
- JavaScript / Next.js: frontend chat UI (`webApp/rag-chat`), API routes for chat
- LLMs: pipeline designed to be model-agnostic; examples reference BGE embeddings and Gemini (or other LLM providers)
- Notebooks: experiments and evaluation (`scr/LLM_Prod_RAG.ipynb`, `scr/RAG_Chunking_Strategies.ipynb`)

## Files & folders of interest

- `ingest.py` / `ingest_open.py` — ingestion and preprocessing scripts that create chunks and push embeddings to Supabase
- `dataset/text_chunks_and_embeddings_df.csv` — example dataset of text chunks and embeddings produced by the pipeline
- `webApp/rag-chat` — Next.js application and API route that implement the chat UI
- `diagram/flow.tex` — TikZ source for the RAG flow diagram
- `scr/` — notebooks used for development, testing and evaluation

## Chunking strategies (technical details)

1. Fixed-size chunking
	- Split the text into equal-sized windows (characters or tokens). Optionally use overlap to preserve context between chunks.
	- Pros: simple, consistent chunk sizes. Cons: may break semantic units in awkward places.

2. Semantic chunking
	- Use sentence/paragraph boundaries and heuristics (e.g., max tokens per chunk) to create chunks that align with natural language structure.
	- Pros: better semantic coherence. Cons: implementation complexity; may produce variable-sized chunks.

3. Recursive chunking
	- Recursively split large documents: at each step, split on the largest available semantic boundary (section > paragraph > sentence) until chunks fit within a max size. Optionally merge small adjacent chunks to reach a sensible minimum size.
	- Pros: balances chunk size and semantic boundaries, reduces context loss.

4. LLM-based chunking
	- Use an LLM to suggest chunk boundaries or to summarize and combine content before embedding. For example, ask the model to extract the main points or to split the document into coherent sections with size limits.
	- Pros: highly semantic, can be tuned to downstream tasks. Cons: costs and latency.

## Evaluation

- Retrieval evaluation: measure vector search performance using similarity scores, precision@k / recall@k against a labeled set, and mean reciprocal rank (MRR).
- End-to-end evaluation: compare final LLM answers against ground truth using exact-match, F1, or semantic similarity metrics (embedding-based similarity or LLM-based scoring).
- Chunking comparison: run ablation studies to compare fixed/semantic/recursive/LLM chunking and measure retrieval + answer quality differences.

Scripts and notebooks under `scr/` contain the experiments and evaluation code used during development.

## How to run (basic)

1. Environment
	- Python 3.10+ recommended
	- Node.js (for the frontend) 18+
	- Supabase account + PostgreSQL with pgvector extension

2. Setup Supabase
	- Create a Supabase project and a table for embeddings. The ingestion scripts expect columns for id, text, metadata, and vector embedding (pgvector).
	- Set the required environment variables in `.env` (or `webApp/rag-chat/.env.local` for the frontend API) for Supabase URL and keys.

3. Ingest data
	- Run the ingestion script to chunk documents, compute embeddings, and push them to Supabase.
	- Example (powershell):

```powershell
# from repository root (adjust if using a virtual environment)
python .\ingest.py
```

4. Run the frontend (Next.js chat)
	- Install packages and start the dev server in `webApp/rag-chat`:

```powershell
cd .\webApp\rag-chat
npm install
npm run dev
```

5. Query the API
	- Use the web chat UI or call the API route in `webApp/rag-chat/src/app/api/chat/route.ts` to send queries, which will run the RAG retrieval and LLM inference.

## Notes, assumptions & suggestions

- This README documents the high-level design and how the pieces fit together. Specific details (environment variable names, exact Supabase table schema, which LLM endpoints to call) are implemented in the code (see `ingest.py`, `ingest_open.py`, and the Next.js API route).
- Assumptions made while writing this README:
  - The embeddings model is BAAI/bge-small-en (or similar).
  - The LLM used for final answers is Gemini 2.5 Flash (pipeline is model-agnostic).
  - Supabase + pgvector is used for vector storage and kNN search.

## Next steps / improvements

- Add automated unit tests for chunking functions and a small integration test for ingestion -> search -> answer.
- Add CI that runs linting and the integration test against a small test dataset.
- Provide a Docker-compose setup that launches a local Postgres + pgvector for local development.
- Add a more detailed docs directory with examples of the Supabase table schema, environment variables, and developer onboarding steps.

## Completion

README added/updated to describe the project, pipeline, chunking strategies, integrations, how to run, and next steps. For a visual guide, see `diagram/flow.tex`.

