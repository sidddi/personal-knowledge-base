---
source_url: https://example.com/rag-clinical-decision-support
source_type: article
date_captured: 2026-05-06
ingested: false
---

# RAG for Clinical Decision Support — Lessons from a Regional Health Authority

A regional health authority recently completed a 6-month pilot deploying a RAG-based clinical decision support system across three hospital departments. The system helps physicians quickly surface relevant clinical guidelines and past treatment protocols for complex cases.

## The problem

Clinical guidelines are updated frequently (sometimes monthly), span hundreds of documents, and are rarely read in full. Physicians spend an average of 45 minutes per complex case searching for relevant guidance across multiple portals. Compliance with evidence-based guidelines was measured at 67% before the pilot.

## Architecture

The system uses a three-tier RAG architecture:

1. **Ingestion layer**: clinical guidelines from WHO, EMA, and internal protocols are ingested nightly. Chunking strategy: structural (by section) for guidelines, semantic for case notes.
2. **Retrieval layer**: hybrid search (BM25 + embeddings) with reranking. The reranker is fine-tuned on physician feedback from the first month of pilot.
3. **Synthesis layer**: Claude API generates a 3-bullet summary citing the relevant guidelines with source attribution.

## Key findings

- Query response time dropped from 45 minutes to 8 minutes average
- Guideline compliance improved from 67% to 91% after 3 months
- The reranker fine-tuning made the biggest single improvement (+12% precision)
- Physicians rejected ~15% of AI suggestions — all rejections were logged for future training data

## Governance challenges

- Patient data (case notes) required a separate, isolated retrieval index with strict access control
- GDPR Article 22 review was needed before any case-specific retrieval could be used in the synthesis step
- The audit trail requirement added ~200ms latency per query (logging every document accessed)

## What didn't work

- First chunking strategy (fixed-size 512 tokens) produced fragmented responses for multi-section guidelines — switched to structural chunking in week 3
- Physicians in one department refused to use the system until they could see exactly which sources were retrieved — source attribution was not in the original design
