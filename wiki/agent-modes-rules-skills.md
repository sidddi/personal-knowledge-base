---
title: Agent Architecture — Modes, Rules and Skills
topic: AI Agents
subtopic: enterprise-architecture
status: stable
last_updated: 2026-04-20
sources:
  - https://www.anthropic.com/research/building-effective-agents
related:
  - wiki/ai-agents.md
  - wiki/mcp.md
  - wiki/multi-agent-orchestration.md
  - wiki/skills-claude-code.md
---

## TL;DR
Three-layer framework for designing enterprise agents. **Modes** define who the agent is (role and identity), **Rules** establish how it behaves (governance), **Skills** define what it knows how to do (specific knowledge, injected on demand). Directly applicable to any enterprise agent platform.

## What It Is
A layered architecture for agents in enterprise contexts:

```
User Request
     ↓
  Agent Mode        ← Who it is (role, identity, tone)
     ↓
   Rules            ← How it must behave (governance, limits)
     ↓
   Skills           ← What it knows how to do (injected on-demand)
     ↓
  Tool Execution    ← MCP / tools
```

## How It Works

### Modes — the "who"
- Define the role and identity of the agent
- Examples: "Customer service assistant", "Document processing agent", "Internal IT support"
- Each mode has a different base system prompt, available tools, and communication tone
- An agent can have multiple modes but activates one at a time

### Rules — the "how"
- Establish organizational governance and behavioral constraints
- Applied independently of the active mode
- Examples: "never reveal third-party data", "always cite the regulatory source", "escalate to human if the matter affects fundamental rights"
- Different from MCP: MCP controls *access* to tools, Rules controls *behavior* of the agent

### Skills — the "what"
- Specific knowledge for a concrete task
- **Injected on-demand**, not persistent — avoids context bloat
- Equivalent to SKILL.md files in Claude Code
- Examples: skill for "formal document drafting", skill for "eligibility criteria evaluation"

### Relationship between the three layers

| Layer | Question | Example |
|-------|----------|---------|
| Mode | Who are you? | Customer-facing service agent |
| Rules | What is your limit? | No automated decisions without human review |
| Skills | What do you know right now? | Process benefit application type X |

### MCP vs Rules — not sequential, complementary layers

Common mistake: thinking Rules "come before" MCP in sequential order. They actually operate in **orthogonal layers**:
- **MCP** → gatekeeper: *who can enter* (access to tools and APIs)
- **Rules** → internal policy: *how to behave once inside* (agent behavior)

An agent can have MCP permission to delete a ticket, but a Rule prevents it from doing so without human confirmation. **They work together, not one before the other.**

### Hooks — executable governance at a lower level

More robust than Rules in Markdown: scripts that execute on agent lifecycle events:
- `PreToolUse` → validates before executing a tool (e.g., block destructive operations, validate against GDPR policy)
- `PostToolUse` → automatic action afterwards (e.g., automatic logging, automatic tests)

A `PreToolUse` Hook that validates against a policy is **more robust than a Rule** because it's executed code, not a model instruction the model might not follow.

### Global transversal Rules

Design mistake: repeating the same Rules in every department/domain. Rules should be designed in two layers:
1. **Global rules** (entire organization): GDPR, no PII disclosure, log all actions, official language
2. **Domain-specific rules**: constraints specific to the context (don't delete cases without approval, etc.)

### Warning: dangerous Rules in regulated contexts

The Rule "always give an answer, never say you don't know" is **dangerous** in regulated contexts. If the agent invents a response, it's a **hallucination with legal consequences**. The correct Rule: *"If you don't have sufficient information, escalate to a responsible person."*

## Practical Implications
- **Rules** should be where AI Act compliance lives — not in modes or skills
- **On-demand Skills** reduce active context size, important for cost and latency
- Design Rules first (governance), then Modes/Skills, not the other way around
- In multi-agent systems: each agent has its own layers; the orchestrator can have global Rules
- For critical governance, consider **Hooks** (code) instead of Rules (model instructions) — Hooks can't be "ignored" by model error
- In high-risk systems: classify as **Human-in-the-Loop mandatory** when the agent makes decisions affecting fundamental rights. The correct Rule: present the proposal with justification and wait for human confirmation.
- Modes that resemble specific actions (Query/Create/Modify) should probably be modeled as **Skills**, not Modes. A Mode should be a role.

## Designing an agent ecosystem for your organization

Example mapping for a three-domain deployment:

| Domain | Mode | Key Rules | Main Skills |
|--------|------|-----------|-------------|
| Customer service | Service assistant | GDPR, no PII, escalate if complaint | Service catalog, current regulations |
| Case management | Internal agent | Human-in-loop (high-risk), audit trail | Case workflow, resolution criteria |
| Internal comms | Draft assistant | Institutional tone, fact verification | Style guide, editorial policy |

## At Work / Domain Context
This framework maps directly to most enterprise agent platforms:
- **Modes** → Topics or personas (each topic is a mode of interaction)
- **Rules** → System instructions + governance automation flows
- **Skills** → Knowledge sources + automated actions per topic

The framework is platform-agnostic — the architecture pattern is the same whether you're building in Copilot Studio, LangGraph, or Claude Code.

## Open Questions
- How to implement shared global Rules across multiple agents in an enterprise platform?
- What is the update mechanism for Skills when underlying regulations or processes change?
- When to escalate from Rules (Markdown) to Hooks (executed code) for critical governance?
- How do Hooks in Claude Code map to governance mechanisms in other agent platforms?

## Sources
- **Anthropic — Building Effective Agents**: agent loop, tool design, governance principles.
