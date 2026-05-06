# personal-knowledge-base

A personal AI knowledge base following [Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

The idea: instead of re-deriving knowledge from scratch on every query (classic RAG), an LLM **compiles and maintains a persistent wiki** of interlinked Markdown files. Knowledge accumulates and connects over time — instead of being re-derived each session.

> *"Obsidian is the IDE. The LLM is the programmer. The wiki is the codebase."* — Karpathy

---

## How it works

1. Drop source material (articles, papers, threads, notes) into `raw/` as Markdown
2. Ask Claude: `"Ingest raw/YYYY-MM-DD_slug.md"`
3. Claude extracts key concepts, creates or updates the relevant `wiki/` entry, cross-references related entries, and deletes the raw file
4. Over time, you build a dense, opinionated, evergreen knowledge base that compounds

The `CLAUDE.md` file is the operating manual — it tells Claude exactly how to structure the wiki, run ingestion, and manage session context.

---

## Structure

```
personal-knowledge-base/
├── CLAUDE.md          ← operating instructions for Claude
├── wiki/              ← the actual knowledge base (curated, evergreen)
│   ├── index.md       ← navigation index
│   ├── hot.md         ← session memory (restored at session start)
│   ├── log.md         ← append-only ingestion log
│   └── *.md           ← one file per concept
├── raw/               ← drop source material here for ingestion
├── outputs/           ← derived artifacts: summaries, blog drafts, briefings
└── demo_vault/        ← fictional example to see the system in action
```

---

## Getting started

1. Clone this repo and open it as an Obsidian vault (or any Markdown editor)
2. Install [Claude Code](https://claude.ai/code) and open the repo
3. Edit `CLAUDE.md` to reflect your domain and interests
4. Drop your first article into `raw/` and ask Claude to ingest it
5. Ask questions — Claude will answer using your wiki as primary context

---

## Demo vault

The `demo_vault/` directory contains fictional example entries to show what a populated vault looks like. Open it in Obsidian to explore the structure before building your own.

---

## Wiki entry schema

Every `wiki/` file follows this structure:

```markdown
---
title: <Concept Name>
topic: <topic — free-form, no fixed list>
subtopic: <optional>
status: draft | review | stable
last_updated: YYYY-MM-DD
sources: [url or citation, ...]
related: [wiki/other-entry.md, ...]
---

## TL;DR
## What It Is
## How It Works
## Practical Implications
## At Work / Domain Context
## Open Questions
## Sources
```

---

## Included wiki entries

This repo ships with translated, anonymized versions of real wiki entries on:

- [LLM Wiki Pattern (Karpathy)](wiki/llm-wiki-pattern.md)
- [AI Agents](wiki/ai-agents.md)
- [RAG (Retrieval-Augmented Generation)](wiki/rag.md)
- [MCP (Model Context Protocol)](wiki/mcp.md)
- [Evals (Agent Evaluation)](wiki/evals.md)
- [Prompt Engineering](wiki/prompt-engineering.md)
- [Multi-Agent Orchestration](wiki/multi-agent-orchestration.md)
- [AI Governance in Public Sector](wiki/ai-governance-public-sector.md)

---

## Credits

Pattern by [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). Built and maintained with [Claude Code](https://claude.ai/code).
