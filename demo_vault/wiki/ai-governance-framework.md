---
title: AI Governance Framework — Three-Tier Model
topic: AI Governance
subtopic: framework
status: stable
last_updated: 2026-04-15
sources:
  - https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai
related:
  - wiki/ai-governance-public-sector.md
  - wiki/agent-orchestration-benefits.md
---

## TL;DR
A three-tier governance model for AI agents: behavioral layer (what the agent says it won't do), executable layer (what the architecture prevents it from doing), and audit layer (what happened and when). Only the executable and audit layers are reliable in regulated contexts.

## What It Is
A practical framework for governing AI agent deployments in organizations where compliance, auditability, and accountability matter. Built from patterns observed in public sector and regulated enterprise deployments.

## How It Works

### Tier 1 — Behavioral Layer
Defined in system prompts and agent configuration:
- What topics can the agent address?
- What actions require escalation?
- What are the explicit refusal rules?

**Reliability: low.** Prompts can be circumvented, hallucinated around, or simply fail under distribution shift. This layer sets intent, not guarantees.

### Tier 2 — Executable Layer
Enforced by architecture, independent of model behavior:
- DLP policies blocking access to specific data categories
- Tool and connector allow-lists (the agent physically cannot call unapproved APIs)
- Role-based access control on knowledge sources
- Network segmentation

**Reliability: high.** The model cannot do what the infrastructure won't permit. This is where real governance lives.

### Tier 3 — Audit Layer
- Immutable log of every action, tool call, and data access
- Conversation retention aligned with records management law
- Anomaly detection on usage patterns
- Incident response runbooks

**Reliability: retrospective.** Doesn't prevent bad outcomes but makes them reconstructable and actionable.

## Practical Implications

**The governance hierarchy:**
```
Tier 1 (Behavioral)  →  states intent
Tier 2 (Executable)  →  enforces limits
Tier 3 (Audit)       →  proves compliance
```

All three are necessary. Organizations that rely only on Tier 1 ("we told the AI not to do X") are one prompt injection away from a compliance incident.

**Common failure modes:**
- Deploying Tier 1 without Tier 2: agent says it won't access sensitive data, but nothing stops it technically
- Deploying Tier 2 without Tier 3: compliant by design but can't prove it in an audit
- Treating the audit log as optional: in regulated contexts, it's the primary deliverable, not a nice-to-have

**Governance committee cadence:**
- Weekly during initial deployment (first 60 days)
- Monthly once stable: review usage, scope creep signals, cost trends
- Triggered reviews: on any incident, on model updates, on new data source connections

## Open Questions
- How to detect scope creep in production agents before it becomes a compliance issue?
- At what agent count does the governance committee model break down and need tooling?
- How to handle third-party agents (vendors) that don't expose Tier 2/3 evidence?

## Sources
- EU AI Act (2024), Articles 9–17: risk management, data governance, transparency, human oversight.
- NIST AI RMF (2023): govern, map, measure, manage framework.
