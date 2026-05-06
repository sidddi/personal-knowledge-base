---
title: RAG (Retrieval-Augmented Generation)
topic: RAG
subtopic: architecture
status: stable
last_updated: 2026-04-12
sources:
  - https://www.anthropic.com/research/contextual-retrieval
related:
  - wiki/llm-wiki-pattern.md
  - wiki/ai-agents.md
---

## TL;DR
Technique for passing only the necessary context to an LLM when documentation is too large for the full context window. Documents are split into chunks, vectorized, and the most relevant fragments are retrieved for each query. Alternative to passing everything (expensive and slow) or passing nothing (generic responses).

## What It Is
A 5-step pipeline that converts documents into retrievable knowledge:

```
Documents → Chunks → Embeddings → Vector DB → Similarity Retrieval → Context
```

## How It Works

### Step 1: Chunking — 3 techniques

| Technique | How | When to use |
|-----------|-----|-------------|
| **Fixed-size + overlap** | Cut every N tokens, with overlap | Fast, unstructured corpus |
| **By structure** | Split by sections/paragraphs | Better results, requires well-structured docs |
| **By semantics** | Group related sentences | Best results, more expensive and slow |

### Steps 2–3: Embeddings and Vector DB
- The embedding is a vector representation of text (a number or score)
- Allows comparing texts by numerical similarity
- The Vector DB compares the query vector against chunk vectors

### Steps 4–5: Query and retrieval
1. The user's question is vectorized the same way as the chunks
2. Compared against the Vector DB
3. The K most similar chunks are returned
4. Added to the prompt as context

### Advanced techniques

**BM25 (Hybrid Search):** combines semantic search (embeddings) with lexical search (classic text). Improves retrieval when the query contains specific terms or proper nouns.

**Reranking:** takes the returned chunks and asks the LLM to order them by relevance. Adds latency but improves precision.

**Contextual Retrieval:** passes chunks through an LLM to add additional context before indexing. Makes chunks more understandable in isolation.

## Practical Implications

**vs. LLM Wiki (Karpathy):** RAG re-derives knowledge on every query; the Karpathy pattern pre-compiles it. See [[llm-wiki-pattern]] for the full comparison.

**When RAG is the best option:**
- Large corpus (thousands of docs) — indexing is cheaper than compiling
- Precise retrieval of literal text (regulations, contracts)
- Constantly changing sources

**Key trade-offs:**
- Chunk too small → loses context → fragmented response
- Chunk too large → too much noise → degrades response
- Without reranking → cosine similarity chunks are not always the most useful

## At Work / Domain Context
When deploying RAG in an organizational context:
- Which sources are indexed? (sensitive data, PII)
- Who controls which chunks reach the model?
- How is what the model "read" to generate a response audited?

RAG is the underlying pipeline for most knowledge-base features in enterprise AI products (Copilot, Gemini for Workspace, etc.).

## Open Questions
- What is the optimal chunking for administrative or regulatory documents?
- How to integrate Contextual Retrieval with Azure AI Search or similar enterprise stacks?
- How to measure retrieval quality (recall@k, MRR) in production?

## Sources
- **Anthropic — Contextual Retrieval**: BM25 hybrid search, contextual retrieval technique, benchmark results.
