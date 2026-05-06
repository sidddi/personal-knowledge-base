---
title: Skills in Claude Code (SKILL.md)
topic: AI Agents
subtopic: claude-code
status: stable
last_updated: 2026-04-20
sources:
  - https://docs.anthropic.com/en/docs/claude-code/skills
related:
  - wiki/ai-agents.md
  - wiki/agent-modes-rules-skills.md
  - wiki/persistent-context-patterns.md
---

## TL;DR
Claude Code Skills are SKILL.md files containing specialized instructions for a specific task. Claude loads them dynamically when needed, without them always being in context. Direct analogue to the on-demand skill pattern in enterprise agent frameworks — and the right way to avoid context bloat.

## What It Is
A Claude Code mechanism for encapsulating task-specific knowledge and instructions in separate files. Invoked with `/skill-name` and Claude loads the file content into context at that moment.

## How It Works

### Anatomy of a SKILL.md
```markdown
---
name: skill-name
description: When and why to use this skill. Claude uses this to decide when to load it.
---

# Skill: [skill name]

## Purpose
[One-line description of when to use this skill]

## Instructions
[Specific steps, rules, expected formats]

## Required context
[What Claude needs to know to execute it]

## Expected output
[Format and content of the response]
```

### Concrete example — vault ingestion skill
```markdown
---
name: ingest
description: Ingest a raw/ file into the wiki following CLAUDE.md schema
---

# Skill: ingest

## Instructions
1. Read the raw/ file
2. Identify the main concepts
3. Create or update the relevant wiki/ pages
4. Update index.md
5. Append to log.md
6. Delete the raw/ file
```

### Relationship with CLAUDE.md
- **CLAUDE.md**: instructions always in context (project rules, schema, invariants)
- **SKILL.md**: instructions loaded on-demand for specific tasks

### Storage locations
```
~/.claude/skills/skill-name/SKILL.md   ← global, all projects
.claude/skills/skill-name/SKILL.md     ← local, one specific project
```

On claude.ai: Settings → Features → upload `.zip` with the skill folder. Available on Pro, Max, Team, Enterprise plans.

### Skill vs Agent Mode — key distinction (GitHub Copilot)

Within GitHub Copilot, the distinction between skill and agent mode is equivalent to tool vs full agent:

**Skill (tool)**
- A concrete capability Copilot can invoke: search the repo, read a file, run terminal, etc.
- Doesn't act alone — the user still directs the conversation and decides what to do with the result.
- Example: Copilot uses the "search codebase" skill to answer a question.

**Agent Mode**
- Copilot takes control of the complete flow: plans, executes multiple skills in sequence, observes results, corrects errors and retries — all autonomously.
- Autonomous loop: goal → action → observation → next action.
- Can open files, edit code, run tests, read terminal output and react.
- The user defines the objective; Copilot decides how to get there.

**Analogy:**
- Skill = a hammer Copilot knows how to use when you ask.
- Agent Mode = a carpenter who picks up the hammer, nails, and wood, and builds the shelf for you.

This distinction maps directly to the Modes/Rules/Skills framework: a Skill is an atomic capability, an Agent Mode is a complete orchestration loop.

## Practical Implications
- A poorly written SKILL.md degrades agent quality as much as a bad prompt
- Skills must be self-contained: don't assume Claude remembers context from a previous skill
- Name skills with verbs: `ingest`, `query`, `summarize`, `validate` — not abstract nouns
- Version skills like code — they're in git, you can diff changes
- **Skills vs CLAUDE.md**: if an instruction is needed for every session, put it in CLAUDE.md. If it's only needed for specific tasks, put it in a skill.

## At Work / Domain Context
The skill concept is directly transferable to other enterprise agent frameworks:
- Each "topic" in Copilot Studio is effectively a skill
- Knowledge sources within a topic = the skill-specific context
- Keeping topics/skills focused and separate avoids context bloat

A useful skill for this vault: an `ingest` skill that encapsulates the full ingestion workflow, so you don't have to repeat instructions every time.

## Open Questions
- Is there an emerging standard for skill file formats across different agent CLIs?
- How to handle skill versioning when the underlying process changes?
- When does a skill become complex enough to warrant being its own agent?

## Sources
- **Anthropic Docs — Claude Code Skills**: storage locations, invocation, structure, difference from CLAUDE.md.
