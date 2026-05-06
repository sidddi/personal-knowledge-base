---
title: Evals (Agent Evaluation)
topic: Observability
subtopic: evals
status: stable
last_updated: 2026-04-21
sources:
  - https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents
  - https://www.adaline.ai/blog/complete-guide-llm-ai-agent-evaluation-2026
  - https://aws.amazon.com/blogs/machine-learning/evaluating-ai-agents-real-world-lessons-from-building-agentic-systems-at-amazon/
related:
  - wiki/ai-agents.md
  - wiki/prompt-engineering.md
---

## TL;DR
Evals are systematic tests that measure whether an agent does what it's supposed to do. Without evals, "it seems to work" is the only criterion — and that's not enough to build reliable product.

## What It Is
An eval converts a feeling ("the result seems correct") into a measurable, repeatable criterion. For agents, it's especially critical because behavior is non-deterministic and multi-step: the same input can produce different outputs, and an error in an intermediate step may not be visible in the final result.

## How It Works

### The three ingredients

**Tasks** — an input + an unambiguous success criterion.
Golden rule: *"A good task is one where two independent experts would reach the same pass/fail verdict."* If the criterion is ambiguous, the test is worthless.

**Trials** — repeat the same task k times.
LLMs are not deterministic. A single attempt says nothing about reliability.

**Graders** — who scores the result:

| Type | When to use | Limitation |
|------|-------------|------------|
| Code (string match, test) | Objective results: "did it call the right tool?", "did it save the file?" | Brittle with valid variations |
| LLM-as-judge | Subjective results: "is the summary useful?" | Non-deterministic, requires calibration |
| Human | Calibrate the other two, complex cases | Expensive and slow |

### Pass@k vs Pass^k

- **Pass@k** — "at least one success in k attempts": for internal tools where you can retry.
- **Pass^k** — "all k attempts must succeed": for user-facing product. If one in every three runs is bad, your users will see it.

### Eval types by agent type

- **Coding agents**: deterministic tests ("does the code pass?") + quality rubrics
- **Conversational agents**: task completion + interaction quality + safety, via simulated personas
- **Research agents**: groundedness + coverage + source quality
- **Computer use agents**: environment isolation + backend state verification

### Three-layer framework (Amazon)

```
Top:    Output quality + task completion
Middle: Components — intent, memory, tool-use, reasoning
Bottom: Foundation model benchmarking
```

## Practical Implications

**How to start without drowning:** 20–50 real cases extracted from failures — don't wait for perfect datasets. Every input you've sent to your bot and you know what the result should be is a candidate test.

**Balance positives and negatives:** If all tests are "should classify correctly", you'll never know when it should say "no". A good eval set has hard cases, edge cases, and cases where the correct result is "nothing".

**Read the transcripts:** Evals tell you *what* fails. Transcripts tell you *why*. Without reading them, you can't trust the graders.

**Watch for saturation:** If 95%+ pass, either the tests are too easy or you don't have enough negative cases. When an agent passes almost everything, increase the difficulty.

**Evals don't replace monitoring:** Evals = pre-production. Monitoring = post-production. You need both. Evals don't capture real edge cases that appear in production.

## At Work / Domain Context

For agents in regulated or high-stakes contexts (governance validation, eligibility determination, case routing):
- Pass^k is the right criterion — a false positive has real consequences
- Human graders are especially important here: governance criteria are often not objectively codifiable
- The audit trail compliance requires is essentially a production eval system — connecting them is natural

## Open Questions
- Which tools (Langfuse, DeepEval, Braintrust) integrate best with simple Python agents?
- How to build an eval set from existing production logs?
- LLM-as-judge with the same model that powers the agent: is there bias? (recommended pattern: use a different model as judge)
- At what vault size does context overload invert the trend in knowledge-base systems?

## Sources
- **Anthropic Engineering — Demystifying evals for AI agents**: best practical resource. Components, graders, Pass@k/Pass^k, roadmap.
- **Adaline — Complete Guide 2026**: overview, three eval types (prompt/RAG/agent), platform capabilities.
- **AWS — Real-world lessons from Amazon**: three-layer framework, real cases (shopping assistant, customer service, multi-agent).
