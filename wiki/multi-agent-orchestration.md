---
title: Multi-Agent Orchestration — Patterns
topic: AI Agents
subtopic: orchestration
status: stable
last_updated: 2026-05-06
sources:
  - https://www.anthropic.com/research/building-effective-agents
  - https://agentcrew.sh
related:
  - wiki/ai-agents.md
  - wiki/mcp.md
---

## TL;DR
Four main patterns for orchestrating multiple agents. The choice depends on whether tasks are sequential or parallel, whether they need central coordination, and what the cost of error is. For regulated contexts, Orchestrator-Worker or Supervisor with Specialists are the most appropriate.

## What It Is
A multi-agent system coordinates multiple specialized LLMs for complex tasks that a single agent cannot handle efficiently. Each agent has a role, tools, and limited context.

## How It Works

### The 4 main patterns

#### 1. Orchestrator-Worker
```
Orchestrator (plans and delegates)
    ├── Worker A (specific task)
    ├── Worker B (specific task)
    └── Worker C (specific task)
```
- The orchestrator decomposes the objective and assigns subtasks
- Each worker returns results to the orchestrator
- **Frameworks**: LangGraph, AutoGen, CrewAI
- **When**: tasks with independent and parallelizable subtasks

#### 2. Sequential Pipeline
```
Agent A → Agent B → Agent C → Final result
```
- Each agent processes the output of the previous one
- Clear, predictable, easy to debug
- **When**: linear process where each step depends on the previous

#### 3. Peer-to-Peer Mesh
```
Agent A ↔ Agent B ↔ Agent C
         ↕
      Agent D
```
- Agents communicate directly with each other
- Flexible but difficult to control and audit
- **When**: few production cases — too complex for governance

#### 4. Supervisor with Specialists
```
Supervisor (quality criteria and routing)
    ├── Specialist A (domain X)
    ├── Specialist B (domain Y)
    └── Specialist C (domain Z)
```
- The Supervisor doesn't execute but validates and routes
- Specialists have deep knowledge in specific areas
- **When**: systems where quality and specialization matter more than speed

### Practical case — benefits eligibility processing
```
Orchestrator (coordinates the flow)
    ├── Document validation agent (checks all documents are present)
    ├── Eligibility criteria agent (verifies requirements compliance)
    ├── Amount calculation agent (determines the benefit amount)
    └── Decision generation agent (drafts the administrative resolution)
```
**Data zones** (3 zones):
- Public zone: regulations, scales, forms → all agents
- Protected zone: applicant data → agents with actual need
- Sensitive zone: other case data → no agent without explicit authorization

### Orchestration tools and frameworks

#### AgentCrew (agentcrew.sh)
Positions itself as the **"N8N for AI agents"** — orchestrates agent teams via Markdown documentation, without code. **Leader + Workers** model: a leader agent coordinates, specialized workers execute.

Relevant technical aspects:
- Each agent runs in its own **container** with isolated filesystem and tools
- Inter-agent communication via **NATS** (asynchronous pub/sub message bus)
- **Native MCP**: external tools shared via MCP, configured once for the whole team
- **Open source, self-hosted**: Docker + Kubernetes
- Triggers: Schedule, Webhook, Chat

Comparison vs Claude Code (single conversational agent):
| | **AgentCrew** | **Claude Code** |
|---|---|---|
| Focus | Multi-agent teams | Single agent |
| Trigger | Schedule, Webhook, Chat | Chat |
| Communication | NATS async | Sequential |
| Definition | Markdown docs | JSON config |
| Stack | Go + Docker/K8s | Node.js |

#### Managed vs Self-Hosted — key criterion

For contexts with sensitive data: **sensitive data → always self-hosted**. The "black box" of a managed service is incompatible with the traceability and auditability required by compliance frameworks (AI Act, HIPAA, GDPR).

### Memory for Agents — Taxonomy

Agents need to persist information between interactions. Four types:

| Type | Description | Typical Implementation | Governance implication |
|------|-------------|----------------------|----------------------|
| **Episodic** | History of past conversations, actions and results | Vector database, logs | Who can read the log? Data retention (GDPR) |
| **Semantic** | Factual and domain knowledge (what the agent "knows") | RAG, vector search, knowledge base | Which sources are authorized? Oversharing? |
| **Procedural** | How to do things: skills, tools, workflows | Code, function definitions | Who can modify the tools? Execution audit trail |
| **Long-term** | Persistent user preferences and learning | Dedicated database, user profile | Personal data (GDPR), consent, right to deletion |

**Practical criterion**: procedural and semantic memory can be governed via executable governance (DLP, connector approval). Long-term memory requires explicit legal decision (legal basis, retention, right of access).

## Practical Implications
- **Separation of responsibilities**: each agent should have the minimum necessary access (principle of least privilege)
- **Auditability**: in Orchestrator-Worker it's easy to trace who did what; in Mesh it's nearly impossible
- **Costs**: each agent = one API call. A system with 5 agents can cost 5x more than a single agent
- **Latency**: sequential pipeline adds latency at each step; Orchestrator allows parallelism
- **Error handling**: if a worker fails, the orchestrator must handle it — design for failure explicitly

## At Work / Domain Context
AI Act — high-risk systems (art. 14): any agent that participates in decisions affecting fundamental rights (benefits, sanctions, access to services) **requires Human-in-the-Loop**. This is not optional.

Practical implication: the orchestrator must include an explicit human review step before irreversible actions.

## Open Questions
- LangGraph vs native platform tools for multi-agent systems: what criteria decide?
- How to implement a complete audit trail in an Orchestrator-Worker system?
- NATS vs other message buses for inter-agent communication: when is the async model necessary?
- **Permission propagation between agents**: if a low-privilege agent calls a high-privilege agent, who governs the resulting access? The risk is that the invocation chain bypasses zone restrictions through involuntary propagation.

## Sources
- **Anthropic — Building Effective Agents**: 4 orchestration patterns, design principles, error handling.
- **AgentCrew**: NATS communication, isolated containers, native MCP, Leader+Workers pattern.
