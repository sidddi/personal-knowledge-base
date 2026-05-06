---
title: LLM Wiki Pattern (Karpathy)
topic: RAG
subtopic: knowledge-architecture
status: stable
last_updated: 2026-04-20
sources:
  - https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
related:
  - wiki/index.md
  - wiki/rag.md
---

## TL;DR
Instead of retrieving raw document fragments on every query (classic RAG), an LLM **compiles and maintains a persistent wiki** of interlinked Markdown files. Knowledge accumulates and connects — instead of being re-derived each time. For an AI architect: it's the difference between having memory and having a retrieval system.

## What It Is
A personal knowledge architecture pattern where:
- **You** supply raw sources and ask questions
- **The LLM** writes and maintains the entire wiki
- **Obsidian** (or any Markdown editor) is the IDE for navigating the result

Karpathy's metaphor: *"Obsidian is the IDE. The LLM is the programmer. The wiki is the codebase."*

## How It Works

### The three layers

```
raw/        ← immutable sources (articles, papers, exported conversations)
wiki/       ← Markdown generated and maintained by the LLM
CLAUDE.md   ← the schema: tells the LLM how to structure the wiki and which workflows to follow
```

### Ingestion operation (one new source)
1. Drop the source into `raw/`
2. The LLM reads it and discusses the key points
3. Creates or updates the summary page
4. Updates index + related concept pages (one source can touch 10–15 pages)
5. Appends to the log

### Cross-session context management: `hot.md`
At the end of each Claude Code session, write a compact summary of the knowledge state to `wiki/hot.md`. The next session reads it first. **You never rebuild context from scratch.**

As the wiki grows, navigation works via: `hot.md` → `index.md` → selected pages. No need to read the entire vault.

## Practical Implications

**vs. classic RAG:**

| | RAG | LLM Wiki |
|--|-----|----------|
| Knowledge | Re-derived on every query | Compiled and accumulated |
| Multi-doc synthesis | Expensive (search + combine from scratch) | Cheap (already pre-compiled) |
| Maintenance | Automatic (vectors) | LLM updates pages |
| Human readability | Low (chunks) | High (structured Markdown) |
| Scalability | Better for very large corpora | Better for curated corpora |

**When the Karpathy pattern makes sense over RAG:**
- The corpus is curated (quality > quantity)
- You need synthesis, not just retrieval
- You want to be able to read the knowledge base yourself
- Temporal accumulation matters (learning over months)

**When RAG is better:**
- Very large corpus (thousands of documents) where compilation would be unmanageable
- You need precise retrieval of literal text
- Sources change constantly

## At Work / Domain Context
This vault is an instance of the Karpathy pattern, applied to your domain topics.

Potential uses in an organizational context:
- **Internal governance wiki**: feed with Slack threads, meeting notes, policy documents → the LLM maintains a wiki of decisions and reasoning
- **Project knowledge base**: compile findings from an active project into interlinked, queryable pages
- **Onboarding**: self-maintained wiki of active AI projects, accessible to new team members

Key constraint: be clear about where the LLM runs if the corpus contains sensitive data. The pattern is model-agnostic — it works equally well with on-premise models.

## Open Questions
- At what corpus size does context overload invert the trend?
- How to handle conflicting information between two sources on the same topic?
- What's the optimal session cadence for keeping `hot.md` useful without it becoming stale?

## Sources
- **Karpathy's LLM Wiki gist**: original pattern description. Core concept, folder structure, CLAUDE.md as the schema, hot.md for cross-session memory.
