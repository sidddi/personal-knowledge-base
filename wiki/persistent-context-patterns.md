---
title: Persistent Context Patterns Between LLM Sessions
topic: AI Agents
subtopic: context-management
status: stable
last_updated: 2026-04-20
sources:
  - https://x.com/karpathy (LLM Wiki pattern discussion)
related:
  - wiki/llm-wiki-pattern.md
  - wiki/skills-claude-code.md
  - wiki/ai-agents.md
---

## TL;DR
The problem: every agent CLI session starts with no memory. The solution: persistent context files the agent reads at the start of each session. Three main patterns: global instructions file (CLAUDE.md / GEMINI.md), hot session file (hot.md), and skills catalog. Same problem, same solution, in any agent CLI.

## What It Is
A set of patterns for maintaining context and preferences between sessions in agent CLIs (Claude Code, Gemini CLI, OpenCode, etc.). Model-agnostic — the concept transfers across tools.

## How It Works

### The 3 core patterns

#### Pattern 1: Global instructions file (CLAUDE.md / GEMINI.md)
```
project/
└── CLAUDE.md  ← read automatically at every session
```
- Contains: project schema, rules, invariants, how to work with the codebase
- **Always-on**: always in context
- Use: instructions that must apply to ALL sessions
- Limit: don't put anything there that isn't always necessary (it costs context)

#### Pattern 2: Hot session file (hot.md)
```
wiki/
└── hot.md  ← written at end of each session, read at start of next
```
- Contains: current project state, recent decisions, context of work in progress
- **Rotates**: updated at the end of each session
- Use: "bridge" between sessions — avoids rebuilding context from scratch
- Equivalent: ChatGPT's memory, but explicit and controlled

#### Pattern 3: On-demand workspace (Gemini CLI style)
```
.gemini/
└── settings.yaml  ← workspace configuration
    ├── system_prompt: reference to context file
    ├── tools_enabled: [codeExecution, googleSearch]
    └── context_files: [GEMINI.md, hot.md]
```
- Allows configuring which files load and in what order
- Useful for multiple roles in the same project (developer, architect, reviewer)

### Session strategy for a knowledge vault

```
New Claude Code session in the vault:
1. Read CLAUDE.md (always) → rules and schema
2. Read wiki/hot.md (if exists) → state from previous session
3. Read wiki/index.md → catalog of available knowledge
4. Read relevant wiki pages for the task → specific knowledge
```

### Advanced patterns: Skills+MCPs, SDD, and Memory OS+git

Three complementary patterns that emerge as the problem becomes complex:

#### Pattern A: Skills + MCPs — Persistent context + system access

**Skills** = *who you are and where you are* (static context injected into the system)
**MCPs** = *what you can do and see* (dynamic access to real systems)

Without the skill, the MCP doesn't know *for whom* it's searching. Without the MCP, the skill can't access up-to-date information. Together they eliminate amnesia.

```
You → "where did we leave off?"
CLAUDE.md / Skill → "you're the AI architect, working on these projects..."
MCP → [reads real files, queries docs, checks repo state]
LLM → synthesizes and responds with real context
```

**File architecture:**
```
~/.claude/CLAUDE.md                   ← global context (who you always are)
projects/my-project/CLAUDE.md         ← project-specific context
projects/my-project/skills/
  ├── architect.md                    ← your role and working style
  └── domain-context.md              ← institutional or domain context
```

**Useful MCPs for an AI architect:**
- MCP Filesystem → access to local documents, notes, project docs
- MCP GitHub → see latest commits, repository state, create issues
- MCP Web Search → up-to-date documentation, current regulations

**Limitation**: the skill trigger is not deterministic — the LLM decides probabilistically which skill/tool to use. This is a known limitation of the paradigm.

#### Pattern B: Spec Driven Development (SDD) — Process as the source of truth

Pattern A solves amnesia. Pattern B solves stale context.

```
Pattern A (Skills):  "I'm the AI architect, working on RAG"
                     → Describes who you are and where you are

Pattern B (SDD):     "When I design an architecture, I first do X,
                     then Y, and always consider Z"
                     → Describes how you think and how you work
```

**Architecture:**
```
project/CLAUDE.md          ← entry point (index to the .md files)
project/specs/
  ├── architecture.md       ← decisions made
  ├── design-process.md    ← how you approach a problem
  ├── decision-criteria.md ← when to use X vs Y
  └── constraints.md       ← what you can never do (GDPR, AI Act, etc.)
project/status/
  └── current-status.md   ← live current state of the project
```

The `CLAUDE.md` with SDD doesn't contain context — it contains **instructions for reading the other files**:
```
Before responding, read in order:
1. specs/constraints.md   (what we can never ignore)
2. status/current.md      (where we are now)
3. specs/design-process.md (how we work together)
```

**The SDD ritual (post-session, 2 minutes):**
```
"Update current-status.md with what we did today.
Format: last thing we did, decisions made, blockers, what's next."
```

**Especially useful in regulated contexts**: when processes are legally defined (administrative procedures, compliance workflows), a `specs/process.md` with the legal flow is not just documentation — it's formalized compliance the agent will respect in every interaction.

#### Pattern C: Memory OS + git — Evolutionary and auditable knowledge

Pattern C solves a **time** problem: how the agent's knowledge evolves, and how you justify it.

```
Pattern A: The agent knows where it is        (static context)
Pattern B: The agent knows how to think       (manually updated process)
Pattern C: The agent remembers and learns     (evolutionary auditable knowledge)
```

**The difference between log and evolutionary memory:**
- **Log**: records what happened → grows infinitely and becomes unusable
- **Evolutionary memory**: extracts what's relevant and updates the knowledge base → distills and condenses

Git gives you both: the **current distillate** (file) and the **complete history** (commits).

**Architecture:**
```
project/memory/
  ├── domain-knowledge.md      ← what we've learned empirically (not theory)
  ├── architecture-decisions.md ← live decisions + reasoning
  ├── errors-and-solutions.md  ← failures we don't want to repeat
  └── intuitions.md            ← patterns that emerge empirically
```

**git diff as memory:**
```bash
git diff HEAD~10 memory/domain-knowledge.md
```
You see exactly **how your knowledge has changed** in the last 10 sessions.

**The Pattern C ritual (post-session):**
```
"Analyze what we did today. For each memory/ file:
 - Is there anything that contradicts what was there?
 - Is there a new intuition that emerges?
 - Is there an error to document?
Propose the changes. I'll approve and you commit."
```

**For compliance contexts (AI Act):** git is already the audit system. You can demonstrate which version of the knowledge was active when a response was given, who approved each change, and which experiment triggered it.

#### The three patterns combined

```
CLAUDE.md + Skills (P-A)
    ↓ eliminates amnesia

specs/ SDD (P-B)
    ↓ eliminates inconsistency

memory/ + git (P-C)
    ↓ eliminates forgetting and provides auditability

Result: an agent that knows where it is, how to think,
        and remembers everything it has learned — traceably
```

**When they activate:**
- **Session start** → P-A loads context, P-B loads process, P-C loads accumulated knowledge
- **During session** → P-B guides how you work, P-C informs decisions with past experience
- **Session end** → P-B updates status, P-C distills new learnings and commits

## Practical Implications
- **CLAUDE.md** must be concise — everything always in context has a cost
- **hot.md** should be written so it can be read in isolation (don't assume you remember the previous session)
- Separate clearly: "always necessary" (CLAUDE.md) vs "necessary to continue now" (hot.md) vs "necessary for this specific task" (skill/wiki page)
- The pattern is the same for Claude Code, Gemini CLI, OpenCode — learn it once, apply everywhere
- **Skills for context and domain knowledge** (regulations, processes, terminology); **MCPs for actions on real systems** (query databases, create records, send notifications)
- The central problem is not LLM capability — it's persistent context management. The difference between a useful agent and a frustrating one is not the model, it's whether it knows where it is when a new session opens

## At Work / Domain Context
Even in non-CLI agent systems (Copilot Studio, chatbots), the concept applies to system prompt design:
- Parts of the system prompt always present → CLAUDE.md equivalent
- Context specific to the user/session injected per interaction → hot.md equivalent
- Domain knowledge sources per topic → skills equivalent

## Prompt caching — persistent context within a session

Related but different: Claude's **prompt caching** avoids recomputing the static prefix of the context between turns within the same session.

Structure of an agent's context:
- **Static prefix**: system instructions, tool definitions, project context → identical every turn
- **Dynamic queue**: user messages, tool outputs → grows per turn

KV caching saves the Key-Value tensors of the prefix. Each turn reuses the computation instead of recalculating. Reduces complexity from O(n²) to O(n) per generated token.

Economics (Anthropic):
- Cache reads: 10% of base input price → 90% discount
- Cache writes: 25% more than base price → small premium to save

**For RAG systems**: a 20,000-token system prompt repeated across 50 turns = 1M redundant input tokens. With caching: paid once, the other 49 are cache reads.

## Open Questions
- Is there an emerging standard for context file formats across agent CLIs?
- How to implement automatic hot.md updates at the end of each Claude Code session?
- How to configure prompt caching in RAG systems to minimize API costs?
- At what context size does the static/dynamic split become worth engineering explicitly?

## Sources
- **Karpathy's LLM Wiki discussions (X, 2026)**: three advanced context patterns (Skills+MCPs, SDD, Memory OS+git). Originated from a community discussion on Gemini CLI workspace customization.
