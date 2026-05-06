---
title: AI Governance in the Public Sector
topic: AI Governance
subtopic: framework
status: stable
last_updated: 2026-05-06
sources:
  - https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai
  - https://www.microsoft.com/en-us/trust-center/privacy/gdpr-overview
related:
  - wiki/ai-agents.md
  - wiki/multi-agent-orchestration.md
  - wiki/evals.md
---

## TL;DR
AI governance in public sector organizations is structurally different from enterprise governance: citizen data, legal accountability, EU AI Act compliance, and auditability requirements make every architectural decision a governance decision. A three-tier model (behavioral, executable, audit) provides a practical framework.

## What It Is
A governance framework for deploying AI agents in public administration contexts, where:
- Actions affect citizen rights (benefits, permits, services)
- Data includes PII and sensitive personal information
- Regulatory constraints include GDPR, EU AI Act, and sector-specific law
- Accountability is public, not just contractual

## How It Works

### The three-tier governance model

#### Tier 1 — Behavioral Layer (what the agent is allowed to do)
Defined in system prompts, rules, and agent configuration:
- Which topics can the agent address?
- Which actions require human approval?
- What are the explicit refusal rules?

#### Tier 2 — Executable Layer (technical enforcement)
Enforced by architecture, not prompts:
- Data Loss Prevention (DLP) policies
- Connector and tool approval workflows
- Role-based access control on knowledge sources
- Network segmentation (which systems can the agent call?)

#### Tier 3 — Audit Layer (traceability and compliance)
- Complete log of agent actions and data accessed
- Conversation retention policies (aligned with records management law)
- Monitoring dashboards for usage anomalies
- Incident response procedures

### Data zones for agents

```
Public zone        → all agents (public regulations, forms, FAQs)
Protected zone     → agents with demonstrated need (citizen personal data)
Sensitive zone     → no agent without explicit authorization + DPIA
```

### Agent lifecycle governance

| Phase | Governance action |
|-------|------------------|
| **Request** | Requester submits agent brief + data inventory |
| **Review** | Governance committee reviews: data sources, tools, scope |
| **Approval** | Security, DPO, and legal sign-off |
| **Deploy** | Publish to approved environment with audit logging |
| **Monitor** | Monthly review: usage, errors, scope creep |
| **Retire** | Inactivity threshold (e.g., 60 days) triggers review; orphaned agents are deleted |

### EU AI Act — high-risk systems (Art. 14)
Any agent that participates in decisions affecting fundamental rights requires:
- **Human-in-the-Loop**: not "the user can review if they want" — a mandatory review step before irreversible actions
- **Transparency**: users must know they're interacting with an AI system
- **Auditability**: decision trail must be reconstructable

## Practical Implications
- **Executable governance beats prompt governance**: DLP policies and connector restrictions are more reliable than "the agent is instructed not to". Architecture enforces what prompts can't.
- **Tier 2 is the hard part**: behavioral rules are easy to write; getting the infrastructure right (access control, DLP, logging) takes real engineering work
- **Orphan agent risk**: agents deployed and forgotten accumulate risk. A defined retirement process is not optional
- **Cost visibility**: consumption-based AI costs in public sector need approval chains just like any other procurement — build this into the workflow from day one

## At Work / Domain Context
This framework applies to any organization deploying AI agents in regulated contexts: public administration, healthcare, financial services, education.

The key mindset shift: **every agent deployment is also a data governance decision**. What data the agent can access determines what harm it can cause — and in public sector, that harm is borne by citizens.

## Open Questions
- How to implement automated scope-creep detection in production agents?
- What's the right cadence for governance committee reviews as agent count scales?
- How to handle cross-departmental agents that access data from multiple zones?
- GDPR Article 22 (automated decision-making): at what level of agent autonomy does it apply?

## Sources
- **EU AI Act (2024)**: high-risk system classification, human oversight requirements (Art. 14), transparency obligations.
- **GDPR Art. 22**: automated decision-making, right to human review, legal basis requirements.
- **Microsoft Trust Center — GDPR**: data residency, subprocessor obligations, DPO resources.
