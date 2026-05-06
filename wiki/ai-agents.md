---
title: AI Agents
topic: AI Agents
subtopic: architecture
status: stable
last_updated: 2026-04-20
sources:
  - https://www.anthropic.com/research/building-effective-agents
related:
  - wiki/mcp.md
  - wiki/rag.md
  - wiki/multi-agent-orchestration.md
---

## TL;DR
An agent is an LLM with access to tools that runs in a loop until it completes an objective. Most tool calls serve to **gather information** from the context, not to modify it. Without tools, there is no agent.

## What It Is
An LLM that:
1. Receives an objective
2. Calls tools to inspect the environment
3. Advances toward the objective
4. Repeats until complete or error

The model knows nothing on its own — it depends entirely on tools to understand the context.

## How It Works

### Basic loop
```
objective → call tool → inspect environment → advance → repeat
```

### Design principles
- **Small toolset:** few, well-defined tools. Connecting tools without criteria → context bloat → response degradation.
- **High-value, low-risk tasks:** if an error has an acceptable cost, agents are appropriate. If the error is catastrophic, humans need to be in the loop.
- **Evals are non-optional:** without evals, you don't know if the agent is working correctly.

### Real examples
| Agent | Tools | Memory mechanism |
|-------|-------|-----------------|
| Claude Code | read file, write file, run tests | `CLAUDE.md` |
| Computer Use | screenshot, click, type | screenshot as observation |

**Claude Code additional:** can parallelize tasks with Git worktrees (each instance on its own branch). Expanded by connecting MCP servers: Sentry, Jira, Slack.

**Computer Use:** runs in an isolated Docker container. Useful for QA testing and workflow automation.

## Rule-based Agent vs LLM Agent

Two radically different agent paradigms:

| | **Rule-based Agent** | **LLM Agent** |
|---|---|---|
| **Behavior** | Deterministic ("if X then Y") | Emergent from model reasoning |
| **Generalization** | None — must anticipate every case | High — generalizes to unpredicted cases |
| **Auditability** | Total — every decision is traceable | Partial — requires explicit explainability |
| **Cost** | Low | Higher (inference) |

**When to choose rules:** closed and well-defined domain, unacceptable error rate, strict regulation (AI Act high-risk systems), exact audit requirement.

**When to choose LLM agent:** open domain, variable inputs, natural language interpretation with ambiguity, cost of anticipating every case manually is unachievable.

**Hybrid pattern in production:** rules for governance and critical actions ("executable governance"), LLM for interpretation and reasoning. The agent decides what action to take, but what it can do and which data it can access is governed by the architecture layer (MCP + Rules), not by the model.

## Practical Implications
- Keep the toolset as minimal as possible — each added tool increases the probability of error
- Design for reversibility: if an action is irreversible (deleting data, sending emails), add human confirmation
- Evals are not optional — without measurement, there is no improvement
- RAG and agents are not mutually exclusive: RAG for static knowledge, tools for dynamic context inspection
- **Choose the right paradigm:** not everything needs an LLM agent. Ticket routing by category → rules. Natural language query interpretation → LLM. Most production systems combine both.

## At Work / Domain Context
When deploying agents in organizational contexts, the key governance questions are:
- What tools can an agent call on behalf of a user?
- How are agent actions audited?
- Who is responsible if an agent takes an incorrect action?

The "high-value, low-risk tasks" principle is especially relevant in regulated environments (public administration, healthcare, finance).

## Open Questions
- How to scale Claude Code worktree patterns to multi-tenant agent systems?
- What native Human-in-the-Loop mechanisms exist in common agent frameworks?
- In which specific domains is the domain sufficiently closed to use rules instead of LLM?

## Sources
- **Anthropic — Building Effective Agents**: core concepts. Definition, basic loop, examples, relationship with MCP and RAG.
