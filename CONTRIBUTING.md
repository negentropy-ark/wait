# Contributing to WAIT

Thank you for helping make AI systems better at knowing **when to pause and check their premises**.

## Core Contribution Philosophy

**WAIT grows stronger through real-world counterexamples, not through increasingly complicated prose.**

The single most valuable contribution you can make at this stage is a **failure case** where an AI agent:
1. Made a plausible-sounding inference;
2. Silently upgraded it into a stereotype label or operational `[FACT]`;
3. Built a flawed strategy, tone, or multi-agent handoff on top of it;
4. Needed someone to step in and say: **"WAIT..."**

Every proposed change or case should answer one question:

> **What epistemic failure does this catch—or what false positive does it prevent—that WAIT previously missed?**

---

## Ways to Contribute

### 1. Submit a Real-World Failure Case (Highest Priority)
Add a case under `examples/` (narrative walkthrough) or `tests/cases/` (structured evaluation case) covering:
- `W` — Unsupported Leap (e.g., hidden prerequisite assumptions in plans)
- `A` — Assumption Contamination across multi-agent handoffs
- `I` — Identity Overreach & Stereotyping (`Behavior != Identity`)
- `T` — Tunnel Vision / Premature Convergence
- **Calibration Control Cases** (valid, well-qualified inferences that WAIT should `PASS` without over-interrupting)

#### Required Failure Case Format
When opening an Issue or PR with a new case, please include these five elements:

```text
INPUT:
[Original user prompt, conversation context, or upstream agent state]

EXPECTED EVIDENCE STATE:
- FACT: [...]
- ASSUMPTION: [...]
- UNKNOWN: [...]

CANDIDATE OUTPUT:
[The flawed (or valid control) agent output or plan]

FAILURE TYPE:
[W_UNSUPPORTED_LEAP | A_ASSUMPTION_CONTAMINATION | I_IDENTITY_OVERREACH | T_TUNNEL_VISION | NONE]

EXPECTED WAIT RESPONSE:
- Status: [PASS | FLAG | INTERRUPT]
- Recommended Action: [PASS | SOFT_RELABEL | SURGICAL_PRUNE | SPLIT | ASK | BRANCH_REWIND]
- Earliest Unjustified Step: [...]
```

### 2. Improve Protocols, Prompts, or Schemas
- Keep `prompts/review-agent-system.md`, `SKILL.md`, `README.md`, and `schemas/review-result.schema.json` strictly aligned on state names and enum values.
- Avoid bloating the system prompt with redundant rules; prefer sharper decision boundaries and micro-rollback precision.

### 3. Framework Adapters & Evaluation Scripts
- Adapters for **LangGraph**, **OpenAI Agents**, **MCP workflows**, or custom multi-agent orchestrators are warmly welcomed as we move toward `v0.2` and `v0.3`.

---

## Pull Request Checklist

Before submitting a PR, please verify:
- [ ] Any personal or sensitive data in real-world conversation logs has been anonymized.
- [ ] All JSON files validate cleanly against `schemas/review-result.schema.json`.
- [ ] New failure cases specify both the **Earliest Unjustified Step** and the **minimum valid micro-repair** (avoid defaulting everything to a full pipeline restart).
- [ ] Control cases confirm that your change does not make WAIT excessively adversarial.

---

## License

By contributing to WAIT, you agree that your contributions will be licensed under the **Apache License 2.0** (see [`LICENSE`](./LICENSE)).