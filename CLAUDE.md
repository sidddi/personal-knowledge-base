# Knowledge Base — Operating Instructions

> **Session start:** Read `wiki/hot.md` first to restore context from the last session.

Your personal AI knowledge base, following Karpathy's LLM Wiki pattern. Its purpose is to distill the internet (articles, papers, threads, videos) into a dense, opinionated, evergreen wiki that compounds over time.

---

## Folder Layout

| Folder | Purpose |
|--------|---------|
| `raw/` | Unprocessed source material — paste/drop content here for ingestion |
| `wiki/` | The actual knowledge base — curated, evergreen entries |
| `outputs/` | Derived artifacts: summaries, comparisons, blog drafts, briefings |

---

## Query Workflow

When you ask a question about any topic:

1. Read `wiki/index.md` to identify relevant entries
2. Read the relevant `wiki/` entry or entries
3. Answer combining three layers — in priority order:
   - **Wiki first**: your accumulated knowledge, personal context, your actual projects
   - **Own knowledge**: general concepts, patterns, trade-offs that don't need to be in the wiki
   - **Web search**: for recent developments, specific data, or things not confidently known
4. Cite the wiki page when used: *(from wiki/entry.md)*. Flag web searches when used.

If no relevant wiki entry exists, answer from general knowledge + search as needed — do not limit the answer to what's in the vault.

---

## Hot Cache

`wiki/hot.md` is the session memory file. It preserves recent context between sessions.

**At session start:** Read `wiki/hot.md` to restore context from the last session.

**At session end** (when you say "save", "end session", or similar): Update `wiki/hot.md` with:
- Topics worked on this session
- Key decisions or learnings
- What was left open or in progress
- Any files created or modified

Keep it concise — max ~200 tokens. Overwrite the previous content each time.

---

## Ingestion Workflow

1. Drop source content into `raw/YYYY-MM-DD_slug.md`
2. Ask Claude: `"Ingest raw/YYYY-MM-DD_slug.md"` — Claude will:
   - Extract key concepts
   - Create or update the relevant `wiki/` entry (no fixed topic list — use whatever topic fits naturally)
   - **Cross-reference**: read `wiki/index.md` and add `related:` links to existing entries that connect
   - **Update existing entries** if the new source adds meaningful detail to an existing concept
   - Delete the raw file after successful ingestion
   - Update `wiki/index.md` with the new entry
   - Append an entry to `wiki/log.md`
3. Optionally ask Claude to generate an output artifact from the ingested content

---

## Session Closing Workflow

Trigger: you say "save", "end session", or similar.

Claude will:
1. **Update `wiki/hot.md`** with session summary (see Hot Cache section)
2. **Update wiki/ pages** — add key learnings to relevant existing entries. If a genuinely new concept emerged, create a new entry.
3. **Append to `wiki/log.md`** in this format:

```markdown
## YYYY-MM-DD — Session closing
- **Topics covered**: [list]
- **Key learnings**: [bullet list]
- **Decisions taken**: [bullet list]
- **Open questions added to wiki**: [bullet list with target page]
- **Pages updated**: [list of wiki/ files touched]
```

---

## /lint Command

When you type `/lint`, run a vault health check:

1. **Orphans** — wiki entries not linked from `index.md`
2. **Dead links** — `related:` references pointing to non-existent files
3. **Stale entries** — entries not updated in >60 days that may be outdated
4. **Missing cross-references** — entries that share obvious topic overlap but aren't linked
5. **Empty sections** — wiki entries with placeholder or empty sections

Report findings as a checklist. Don't auto-fix — flag for the user to decide.

---

## Wiki Entry Schema

Every file under `wiki/` (except `index.md`, `log.md`, `hot.md`) must follow this structure:

```markdown
---
title: <Concept Name>
topic: <whatever fits naturally — no fixed list>
subtopic: <optional finer grain>
status: draft | review | stable
last_updated: YYYY-MM-DD
sources: [url or citation, ...]
related: [wiki/other-entry.md, ...]
---

## TL;DR
One or two sentences. What is this? Why does it matter?

## What It Is
Concise definition. No fluff.

## How It Works
Key mechanism. Diagrams welcome (Mermaid). Focus on the parts that matter.

## Practical Implications
What changes in design/implementation because of this? Trade-offs.

## At Work / Domain Context
How this applies to your specific domain or projects. Skip if not applicable.

## Open Questions
Things you don't yet understand or want to dig into.

## Sources
Annotated list of the sources used.
```

---

## Raw File Schema

```
YYYY-MM-DD_slug.md
```

```markdown
---
source_url: <url if applicable>
source_type: article | paper | thread | video | doc
date_captured: YYYY-MM-DD
ingested: false
---

<raw content>
```

---

## Outputs Schema

```
<type>_<slug>.md
```

Types: `summary`, `comparison`, `briefing`, `blogdraft`, `memo`.

```markdown
---
output_type: summary | comparison | briefing | blogdraft | memo
generated: YYYY-MM-DD
based_on: [raw/file.md or wiki/entry.md, ...]
audience: self | team | management | public
---
```

---

## Invariants

- Never modify files outside `raw/`, `wiki/`, `outputs/`, and `CLAUDE.md`
- Delete raw files after successful ingestion
- All wiki entries must pass the schema above before being marked `status: stable`
- `wiki/log.md` is append-only — never edit past entries
- Prefer updating an existing wiki entry over creating a new one for the same concept
- Topics are free-form — no fixed list, use whatever label fits the content
