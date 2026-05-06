---
title: Prompt Engineering
topic: Prompt Engineering
subtopic: techniques + evaluation
status: stable
last_updated: 2026-04-12
sources:
  - https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
related:
  - wiki/ai-agents.md
  - wiki/evals.md
---

## TL;DR
Prompt engineering is the art of instructing an LLM well. Evals are the measurement system to know if it works. Without evals, prompt engineering is intuition; with evals, it's engineering.

## What It Is

### Prompt Engineering — 4 basic principles
1. **Be specific** — vague instructions → vague responses
2. **Define the output format** — if you expect JSON, say so; if you expect a list, say so
3. **Use XML tags** — they structure the prompt and prevent ambiguities (`<context>`, `<instructions>`, `<examples>`)
4. **Add examples** — few-shot prompting; the model learns by analogy better than by abstract description

### Prompt Evals — the pipeline
```
Dataset of prompts → LLM → Response → Grader → Score
```

**Step by step:**
1. **Create dataset**: representative example prompts. Can be generated with an LLM.
2. **Send to model**: `system prompt + user prompt → response`
3. **LLM Grader**: an LLM evaluates response quality. *Forcing reasoning* (chain-of-thought) improves evaluation quality.
4. **Code Grader**: evaluates objective aspects (format, length, presence of required fields).
5. **Iterate**: modify the prompt, return to step 2.

## How It Works

### Anatomy of a good system prompt
```xml
<role>You are a specialized assistant in...</role>
<rules>
  - Rule 1
  - Rule 2
</rules>
<exceptions>When the user asks for X, do Y</exceptions>
<output_format>Always respond in JSON with fields: ...</output_format>
<examples>
  <example>
    <input>...</input>
    <output>...</output>
  </example>
</examples>
```

### LLM-as-a-Judge (Grader)
- The evaluating model must have explicit reasoning to be reliable
- Need a clear rubric: "score 1 to 5 where 5 is..."
- Avoid position bias (the model tends to prefer the first option presented)

## Practical Implications
- Evals must exist **before** deploying any system to production
- A small dataset (50–100 cases) is already useful for detecting regressions
- Code Grader is more reliable than LLM Grader for objective things — use it for format and structure
- Saving previous responses allows detecting regressions when you change the model or prompt
- Chain-of-thought in the grader prompt significantly improves scoring reliability

## At Work / Domain Context
Evals for domain-specific agents should be designed for:
- Cases specific to your domain (vocabulary, formats, procedures)
- Detection of out-of-scope responses (an HR agent should not respond about unrelated topics)
- Quality measurement across all languages your users speak

## Open Questions
- How to integrate automatic evals into CI/CD for agent deployments?
- What scoring threshold justifies deploying a new prompt to production?
- How to handle ambiguous cases where multiple responses are correct?

## Sources
- **Anthropic Docs — Prompt Engineering Overview**: 4 core techniques (specificity, format, XML tags, examples).
- **Anthropic Docs — Prompt Evaluations**: complete eval pipeline, LLM Grader, Code Grader, dataset generation.
