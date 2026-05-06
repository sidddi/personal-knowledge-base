---
title: Case Study — RAG for Clinical Decision Support
topic: RAG
subtopic: case-study
status: stable
last_updated: 2026-05-01
sources:
  - https://example.com/rag-clinical-decision-support
related:
  - wiki/rag.md
  - wiki/ai-governance-public-sector.md
---

## TL;DR
Regional health authority deployed RAG to surface clinical guidelines for physicians. Query time dropped from 45 minutes to 8 minutes. Key lesson: structural chunking and source attribution were non-negotiable requirements discovered post-launch.

## What It Is
A production RAG deployment in a regulated healthcare context. The system retrieves relevant clinical guidelines and summarizes them for physicians making treatment decisions — not a chatbot, but a structured decision support tool.

## How It Works

Three-tier architecture:
1. **Ingestion**: nightly pipeline for clinical guidelines (WHO, EMA, internal protocols). Structural chunking by section.
2. **Retrieval**: hybrid BM25 + embeddings search, with reranker fine-tuned on physician feedback.
3. **Synthesis**: 3-bullet summary with mandatory source attribution.

Patient case notes live in a **separate, isolated index** — different access controls, separate audit log, GDPR review required before use.

## Practical Implications

**What moved the needle most:**
- Reranker fine-tuning on physician feedback: biggest single improvement (+12% precision)
- Source attribution: physicians refused to use the system without it — must be in v1, not a nice-to-have
- Structural chunking: fixed-size produced fragmented responses for multi-section guidelines

**What cost more than expected:**
- Audit trail logging added ~200ms per query — plan for this in latency budgets
- GDPR Article 22 review blocked case-note retrieval for 6 weeks — involve DPO from day one

**Rejection rate**: 15% of AI suggestions were rejected by physicians. All rejections were logged as future training data — this is the right pattern, not a failure signal.

## Open Questions
- At what scale does reranker fine-tuning become cost-effective vs a larger base model?
- How to handle guideline conflicts (two authoritative sources disagree on protocol)?
- What's the right human review threshold before AI suggestions become binding?

## Sources
- Internal pilot report, Regional Health Authority (fictitious), 2026.
