# RAG Project (LangChain + Pinecone + Groq)

**Student:** Daniel Rodríguez

This repository contains a Retrieval-Augmented Generation (RAG) notebook built for the lab assignment. The implementation uses a custom, self-authored knowledge base and avoids copying external project content.

## Purpose

The objective is to answer questions using retrieved evidence instead of relying only on model memory.

Current stack in the notebook:

- **Groq (`ChatGroq`)** for answer generation
- **Pinecone** for vector indexing and similarity retrieval
- **PineconeEmbeddings** (`multilingual-e5-large`) for embeddings
- **LangChain LCEL** for chain composition

Tutorial context used as reference:

- LangChain Quickstart: https://docs.langchain.com/oss/python/langchain/quickstart
- LangChain RAG tutorial: https://python.langchain.com/docs/tutorials/rag/

## System Flow

```text
Ingestion (one-time)
Custom knowledge entries -> Chunking -> Embeddings -> Pinecone index

Question time (per query)
Question -> Retriever (top-k chunks) -> Prompt with context -> Groq -> Final answer + sources
```

## Main Building Blocks

| Block | Implementation |
|---|---|
| Orchestration | LangChain with LCEL (`RunnablePassthrough`, parser, prompt pipeline) |
| Knowledge source | Custom `Document` objects created in `notebook.ipynb` |
| Text splitting | Manual `split_text` function (chunk size 700, overlap 100) |
| Embeddings | `PineconeEmbeddings` with `multilingual-e5-large` |
| Vector DB | `PineconeVectorStore` |
| LLM | `ChatGroq` with `GROQ_MODEL` |
| Resilience | Local keyword retriever fallback when Pinecone is unavailable |

## Setup Instructions

### 1) Create and activate a virtual environment

```bash
python -m venv .venv
```

Windows:

```powershell
.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
source .venv/bin/activate
```

### 2) Define credentials

Windows PowerShell:

```powershell
$env:GROQ_API_KEY="your_groq_key"
$env:PINECONE_API_KEY="your_pinecone_key"
```

macOS/Linux:

```bash
export GROQ_API_KEY="your_groq_key"
export PINECONE_API_KEY="your_pinecone_key"
```

Optional overrides:

```bash
export GROQ_MODEL="llama-3.1-8b-instant"
export PINECONE_INDEX_NAME="rag-daniel-original-kb"
```

## How to Run

Open `notebook.ipynb` and run cells top-to-bottom.

Execution stages:

1. Install dependencies (`langchain`, `langchain-groq`, `langchain-pinecone`, `pinecone`, `python-dotenv`)
2. Load and clean environment keys
3. Build custom knowledge base and chunks
4. Initialize Pinecone and ingest vectors (or fallback retriever)
5. Build LCEL RAG chain with `ChatGroq`
6. Ask demo questions and print sources

## Troubleshooting

- If the chain cell fails with authentication errors (401), verify `GROQ_API_KEY` and rerun the environment + chain cells.
- If Pinecone initialization fails, the notebook automatically switches to a local fallback retriever so the demo can still run.
- If outputs look stale, restart the kernel and run all cells in order.

## References

- https://python.langchain.com/docs/tutorials/rag/
- https://docs.langchain.com/oss/python/langchain/quickstart
- https://python.langchain.com/docs/integrations/vectorstores/pinecone/
- https://python.langchain.com/docs/integrations/chat/groq/
