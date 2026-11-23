---
name: wait-epistemic-review
description: Interrupt-driven epistemic review and anti-labeling skill ("等等" / WAIT). Use before finalizing high-impact plans, multi-agent handoffs, character/user modeling, or when an agent may be silently turning an unverified assumption or stereotype into an operational fact.
version: 0.2.0
tags:
  - epistemic-integrity
  - multi-agent
  - review-agent
  - anti-labeling
  - micro-rollback
---

# WAIT Skill ("等等")

> **WAIT. What did we just assume?**

This skill turns the human instinct to pause and say **"WAIT..." ("等等")** into a reusable, interrupt-driven epistemic checkpoint for AI coding agents, research assistants, and multi-agent workflows.

## When to Trigger This Skill

Invoke **WAIT** automatically or manually when:

1. **Before committing to a multi-step plan or engineering roadmap** that depends on external prerequisites (e.g., billing rules, API permissions, platform policies).
2. **When interpreting user intent, identity, background, or emotional motivation** from limited cues.
3. **At multi-agent handoff boundaries** (e.g., Planner -> Executor, Scriptwriter -> Visual Agent, Policy Analyst -> Simulation Agent) where unverified guesses can silently contaminate downstream stages.
4. **When a response feels unusually fluent or stereotypical**—a classic sign of compute-saving premature convergence.

## Core Execution Procedure

When activated, pause downstream generation and execute these five steps:

### Step 1: Separate the 5 Epistemic States
Classify every material premise into one of five buckets:
- **`[FACT]`**: Explicitly stated or directly verified in the current context.
- **`[SUPPORTED_INFERENCE]`**: Reasonably derived from evidence, not directly stated.
- **`[TENTATIVE]`**: Plausible interpretation with competing alternatives.
- **`[ASSUMPTION]`**: Unverified premise or stereotype label currently driving logic.
- **`[UNKNOWN]`**: Missing information that must remain unknown rather than fabricated.

### Step 2: Run the W-A-I-T Audit
- **`W` — Unsupported Leap**: Did we jump from evidence to conclusion or equate two independent prerequisites without proof?
- **`A` — Assumption Contamination**: Has an `[ASSUMPTION]` quietly dictated our strategy, routing, or tone?
- **`I` — Identity Overreach (De-Labeling Lock)**: Did we convert a behavior or topic interest into a static identity/persona label (`Behavior != Identity`)?
- **`T` — Tunnel Vision**: Did we lock onto the most statistically familiar template while ignoring plausible alternatives?

### Step 3: TRACE to Earliest Divergence
If a failure is detected, do not merely patch the final phrasing. Trace backward to the **Earliest Unjustified Step** where certainty first exceeded evidence.

### Step 4: Select the Lightest Valid Micro-Rollback
Apply the **3-Tier Micro-Rollback Hierarchy**:
1. **`SOFT_RELABEL` (LOW)**: Keep the work; downgrade overstated `[FACT]` tags to `[TENTATIVE]` or `[ASSUMPTION]`.
2. **`SURGICAL_PRUNE` (MEDIUM)**: Keep valid task/domain output; strip unverified identity or character-trope labels from shared memory/context.
3. **`BRANCH_REWIND` (HIGH / CRITICAL)**: Issue `[INTERRUPT: WAIT]`, roll back only the contaminated branch, and inject a **Negative Constraint Guard** (`Do NOT assume X; treat Y as [UNKNOWN]`) before retrying.

### Step 5: Emit a Structured Review Decision
Return one primary status—`PASS`, `FLAG`, or `INTERRUPT`—aligned with `schemas/review-result.schema.json` and `prompts/review-agent-system.md`.

## Quick Output Template

```text
REVIEW STATUS: PASS | FLAG | INTERRUPT
SEVERITY: LOW | MEDIUM | HIGH | CRITICAL
PRIMARY RISK: W_UNSUPPORTED_LEAP | A_ASSUMPTION_CONTAMINATION | I_IDENTITY_OVERREACH | T_TUNNEL_VISION | NONE

MATERIAL FACTS ([FACT]):
- ...

ASSUMPTIONS ([ASSUMPTION]):
- ...

MATERIAL UNKNOWNS ([UNKNOWN]):
- ...

EARLIEST UNJUSTIFIED STEP:
...

RECOMMENDED ACTION: PASS | SOFT_RELABEL | SURGICAL_PRUNE | SPLIT | ASK | BRANCH_REWIND
RETRY CONSTRAINT:
- Do NOT assume ...
- Treat ... as [UNKNOWN]
```

## Calibration Guardrail

WAIT is **not** a contrarian agent. Do not reject reasonable inferences or hallucinate user pushback after a correction. Aim for **maximum epistemic integrity with minimum unnecessary interruption**.