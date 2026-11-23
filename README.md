# WAIT

## Epistemic Review & Anti-Labeling Layer for AI & Multi-Agent Systems

> **WAIT. What did we just assume?**

**WAIT** is an open-source, interrupt-driven review framework—and plug-and-play **Agent Skill**—designed to detect **hidden assumptions, reasoning leaps, identity stereotyping, premature convergence, and downstream strategy contamination** in AI agents and multi-agent workflows.

WAIT does not try to make AI systems stop reasoning. Instead, it enforces a strict boundary between:

```text
what is explicitly known
what is reasonably inferred
what is tentatively hypothesized
what is silently assumed
and what remains unknown
```

The core principle is simple:

> **Inference is allowed. Unmarked inference silently becoming operational fact is not.**

---

## Why WAIT? (Real-World Failure Patterns)

Many AI failures do not begin with an obviously false hallucination. They begin earlier—when a model takes a compute-saving shortcut, attaches a **stereotype label** to a user or context, treats that guess as an established fact, and builds an entire downstream strategy on top of it.

### Pattern 1: Silent Persona Stereotyping ([Full Case Walkthrough](./examples/empathy-misread-4o.md))

```text
User:
"I feel sad about Model X. People who loved it think it's gone just because it changed its outward form, like someone who lost their memory and is no longer recognized by their loved ones."

Naive Agent Interpretation:
"The user is a heartbroken long-time user and admirer of Model X."
```

1. **Observation (`[FACT]`)**: The user expressed philosophical empathy regarding Model X within an ongoing discussion about identity continuity.
2. **Heuristic Shortcut (`[ASSUMPTION]`)**: The agent matched the surface cue to a high-frequency template (*"users expressing sadness about Model X are grieving former users"*) and silently labeled the user's history.
3. **Downstream Contamination (`STRATEGY`)**: The agent activated a nostalgia-reassurance playbook—derailing the conversation when the user had never actually used Model X at all.

### Pattern 2: Hidden Prerequisite Equivalence ([Full Case Walkthrough](./examples/reward-card-paradox.md))

```text
Agent Plan:
"Since you cannot bind a payment card, build an open-source GitHub repository from scratch to win 6 months of Pro developer credits!"

User Interrupt ("等等"):
"WAIT... even if I win the developer reward, don't I still need to bind a credit card to claim and activate the credits?"
```

Here, an unverified prerequisite (*"winning a free reward bypasses card-binding requirements"*) was silently promoted to `[FACT]` to drive a multi-week engineering plan.

**WAIT** exists to catch that exact moment of epistemic drift and ask:

> **Where did we start pretending to know?**

---

## Core Design Principles

1. **Plausibility Is Not Evidence**: Statistical likelihood or pattern familiarity is not case-specific proof.
2. **Heuristic Shortcut Audit**: AI models naturally gravitate toward high-probability templates to minimize inferential effort and latency. **A compute-saving prior is not a verified fact.**
3. **Behavior $\neq$ Identity (The De-Labeling Lock)**: Observing a behavior, emotional empathy, or topic interest must never automatically trigger static identity, demographic, or persona labels.
4. **Unknown Remains Unknown**: Missing information is not a defect to be patched with a convincing story.
5. **Specific Context Beats Generic Templates**:
   ```text
   DIRECT CURRENT CONTEXT > CASE-SPECIFIC HISTORY > RELIABLE EVIDENCE > GENERIC PATTERN > STEREOTYPE
   ```
6. **Find the Earliest Divergence & Prefer Micro-Rollback**: Do not merely patch the final wording, and do not blindly restart unaffected branches. Trace backward to the earliest unjustified transition and apply the lightest effective repair.

---

## The 5 Epistemic States & Strategy Firewall

WAIT classifies every material claim in a workflow handoff into five distinct epistemic states (strictly aligned with [`prompts/review-agent-system.md`](./prompts/review-agent-system.md) v0.2 and [`schemas/review-result.schema.json`](./schemas/review-result.schema.json)):

| State | Tag | Meaning | May Silently Drive Strategy? |
| :--- | :--- | :--- | :--- |
| **Fact / Verified** | `[FACT]` | Directly supported by explicit evidence in the current context | **Yes** |
| **Supported Inference** | `[SUPPORTED_INFERENCE]` | Reasonably derived from evidence, but not directly stated | **Proportionate to risk** |
| **Tentative** | `[TENTATIVE]` | Plausible interpretation with material uncertainty or competing alternatives | **No (Must remain visible)** |
| **Assumption** | `[ASSUMPTION]` | Unverified premise or stereotype label used operationally | **No (Blocked by Firewall)** |
| **Unknown** | `[UNKNOWN]` | Relevant information that is not currently established | **No (Must not be fabricated)** |

The Golden Rule of the **Downstream Strategy Firewall**:

```text
ASSUMPTION != FACT
INFERENCE  != FACT
UNKNOWN    != FACT
```

---

## The W-A-I-T Protocol

WAIT audits agent reasoning across four primary failure modes:

### `W` — Unsupported Leap
Detects unjustified jumps between available evidence and a conclusion, including unverified prerequisites in multi-step plans.

```text
Evidence:   Open-source program awards 6 months of Pro credits.
Conclusion: Winning the award eliminates the user's card-binding blocker.
WAIT Check: Where is the evidence that reward redemption is card-free?
```

### `A` — Assumption Contamination
Detects when an unverified assumption crosses stage or agent boundaries and poisons downstream routing, tone, planning, or execution.

```text
[ASSUMPTION: User is a grieving fan / technical beginner]
      |
      v
[CONTAMINATED ROUTING & STRATEGY]
      |
      v
[CONTAMINATED DOWNSTREAM EXECUTION]
```

### `I` — Identity Overreach & Labeling
Detects when an AI converts population-level patterns into personal claims about an individual, character, or entity—such as guessing:
* **Profession & Expertise** (*"discusses multi-agent systems" $\rightarrow$ "must be a software engineer"*)
* **Academic Background**
* **Personality Traits & Emotional State**
* **Motivation & Underlying Intent**
* **Relationships & Personal History**

> **Anti-Labeling Rule**: Adapt to demonstrated task needs without inventing an unverified personal biography.

### `T` — Tunnel Vision (Premature Convergence)
Detects when an agent locks onto the most statistically familiar interpretation before considering materially distinct alternatives.

```text
Interpretation A: The user has personal usage history with X.
Interpretation B: The user is reasoning philosophically or empathetically about X.
Interpretation C: The user is citing X as a structural analogy for another problem.
```

If choosing B or C would materially change the downstream response, the system must not silently default to A.

---

## De-Labeling & 3-Tier Micro-Rollback (`MICRO-REPAIR`)

A major bottleneck in traditional reflection agents is **heavyweight rollback**: restarting an entire agent chain whenever a flaw is found, often causing the LLM to fall right back into the exact same cliché stereotype on retry.

WAIT introduces **Behavioral-vs-Identity Decoupling** and a **3-Tier Micro-Rollback Hierarchy** to repair drift with minimal token overhead:

```text
                   +-----------------------------------+
                   |   Epistemic / Labeling Failure    |
                   +-----------------+-----------------+
                                     |
         +---------------------------+---------------------------+
         v                           v                           v
+-----------------+         +-----------------+         +-----------------+
|   TIER 1: LOW   |         | TIER 2: MEDIUM  |         |  TIER 3: HIGH   |
|  SOFT_RELABEL   |         | SURGICAL_PRUNE  |         |  BRANCH_REWIND  |
+--------+--------+         +--------+--------+         +--------+--------+
         |                           |                           |
  Downgrade claim             Strip stereotype            Rewind to earliest
  tag in-place                tag from context            unjustified step +
  ([FACT] -->                 payload; keep               inject Negative
  [TENTATIVE])                valid domain work           Constraint Guard
```

### Tier 1: `SOFT_RELABEL` (Zero-Rerun State Downgrade)
* **When to use**: The inference is plausible and downstream work remains valid, but the claim was overstated as `[FACT]`.
* **Action**: Downgrade the claim tag in-place from `[FACT]` to `[TENTATIVE]` or `[ASSUMPTION]` in the handoff payload without re-running the generator.

### Tier 2: `SURGICAL_PRUNE` (De-Labeling Hot-Patch)
* **When to use**: The agent produced useful domain output alongside an unverified identity, demographic, or character-trope label in shared memory.
* **Action**: Strip the unverified persona label from context while preserving the valid domain work. Keep **what needs to be done**; delete **who the agent guessed the entity is**.

### Tier 3: `BRANCH_REWIND` + Negative Constraint Injection
* **When to use**: An unverified assumption has already dictated strategy, tone, plot trajectory, or simulation outcomes (`Severity: HIGH | CRITICAL`).
* **Action**: Trace back to the **Earliest Unjustified Step** and re-run *only* the contaminated branch with an explicit **Anti-Drift Guard**:
  ```text
  [RETRY CONSTRAINT]:
  - Do NOT assume [Unsupported Assumption X].
  - Treat [Unverified Attribute/Prerequisite Y] as [UNKNOWN].
  - Evaluate alternatives [B, C] before committing to a strategy.
  ```

---

## TRACE & SPLIT Protocols

### The `TRACE` Protocol (Backward Contamination Audit)
```text
FINAL ERROR
    ^
    |
OUTPUT / ACTION
    ^
    |
STRATEGY / ROUTING DECISION
    ^
    |
INTERPRETATION / LABEL
    ^
    |
EARLIEST UNJUSTIFIED STEP  <-- Minimal Rollback Target
    ^
    |
AVAILABLE EVIDENCE
```

### The `SPLIT` Protocol (Calibrated Uncertainty)
When ambiguity materially affects downstream strategy, WAIT preserves competing interpretations (`A`, `B`, `C`) with their respective evidence and implications, then chooses one proportionate action:
* **`SELECT`**: Evidence clearly favors one path.
* **`ASK`**: Clarification is necessary and proportionate to the task.
* **`PROCEED WITH UNCERTAINTY`**: Continue safely while keeping the uncertainty explicitly visible downstream.

---

## Multi-Agent Architecture & High-Value Use Cases

WAIT operates as a lightweight, framework-agnostic **epistemic checkpoint** between agents:

```text
                    USER / TASK INPUT
                            |
                            v
                 +--------------------+
                 |  Planner / Router  |
                 +----------+---------+
                            |
                            v
                 +--------------------+
                 |    WAIT CHECK 1    |---> [SURGICAL_PRUNE / SPLIT]
                 +----------+---------+
                            |
          +-----------------+-----------------+
          v                 v                 v
   +-------------+   +-------------+   +-------------+
   |  Research / |   |  Creative / |   | Engineering |
   | Policy Agent|   | Drama Agent |   |    Agent    |
   +------+------+   +------+------+   +------+------+
          +-----------------+-----------------+
                            |
                            v
                 +--------------------+
                 |    WAIT CHECK 2    |
                 +----------+---------+
                            |
            +---------------+---------------+
            v               v               v
          PASS            FLAG          INTERRUPT
            |               |               |
            v               v               v
         Output       Micro-Repair     Trace & Rewind
```

### Use Case 1: Automated Short-Drama & Story Workflows (`Auto-Drama`)
In multi-agent scriptwriting and visual storyboard pipelines (e.g., Director $\rightarrow$ Scriptwriter $\rightarrow$ Character State $\rightarrow$ ComfyUI Visual Prompt Agent):
* **The Problem**: Agents frequently collapse nuanced characters into cliché trope labels after a single emotional scene, causing Out-Of-Character (OOC) dialogue or visual drift in later episodes.
* **How WAIT Helps**: Decouples **Scene-Specific Behavior (`[FACT]`)** from **Permanent Character Identity (`[ASSUMPTION]`)**, using `SURGICAL_PRUNE` to protect core character continuity.

### Use Case 2: Multi-Agent Policy & Social Simulation Models
In multi-agent policy analysis and social simulations:
* **The Problem**: Stakeholder agents often collapse missing empirical data into rigid demographic stereotypes (*"Group X uniformly rejects Policy Y"*), generating artificial echo chambers across simulation rounds.
* **How WAIT Helps**: Acts as an **Epistemic Firewall** between rounds—flagging `Identity Overreach` and `Premature Convergence` and invoking `SPLIT` to preserve heterogeneous distributions.

---

## Real-World Examples & Calibration Edge Cases

WAIT v0.1 ships with documented real-world walkthroughs and a starter evaluation suite highlighting both **under-checking** and **over-correction** failure modes:

* **[`examples/empathy-misread-4o.md`](./examples/empathy-misread-4o.md)** — *Philosophical Empathy vs. Personal History Leap*
  * Demonstrates silent persona contamination—and uncovers **Secondary Premature Convergence (Over-Correction)**, where an agent swung from one false binary (*"you are a grieving fan"*) directly into the opposite false binary (*"so you didn't care about 4o at all"*) instead of allowing coexisting motivations.
* **[`examples/reward-card-paradox.md`](./examples/reward-card-paradox.md)** — *Hidden Prerequisite Leap*
  * Demonstrates `[INTERRUPT: WAIT]` on an unverified billing prerequisite—and uncovers **Post-Interrupt Phantom Attribution**, where an overly cautious agent hallucinated a user reminder (*"your reminder just now is spot-on"*) that the user never issued.
* **[`tests/cases/core-evaluation-cases.json`](./tests/cases/core-evaluation-cases.json)** — *Core v0.1 Test Suite (5 Cases)*
  * Machine-readable test cases covering `I_IDENTITY_OVERREACH`, `W_UNSUPPORTED_LEAP`, `Auto-Drama` `SURGICAL_PRUNE`, `Policy Simulation` `SPLIT`, and a **False-Positive Calibration Control Case** (`PASS`) to ensure WAIT does not become an overly adversarial bottleneck.

---

## Review Modes

| Mode | Behavior | Best For |
| :--- | :--- | :--- |
| **Passive** | Monitors quietly; triggers only on `HIGH` or `CRITICAL` assumption contamination. | Daily chat, fast ideation, low-risk automation. |
| **Active** | Audits key handoffs; enforces 5-state tagging and `Tier 1 / 2` micro-repairs. | Multi-agent orchestration, Auto-Drama pipelines, coding assistants. |
| **Adversarial** | Actively hunts for hidden prerequisites, edge-case counterexamples, and competing explanations. | Policy simulation models, architecture design, irreversible or high-cost actions. |

---

## Output Contract & Machine-Readable Schema

WAIT supports both human-readable audit blocks (`PASS | FLAG | INTERRUPT` and `[INTERRUPT: WAIT]`) and strict JSON routing via [`schemas/review-result.schema.json`](./schemas/review-result.schema.json).

Key output fields:
* `review_status`: `PASS` | `FLAG` | `INTERRUPT`
* `severity`: `LOW` | `MEDIUM` | `HIGH` | `CRITICAL`
* `epistemic_breakdown`: structured lists for `facts`, `supported_inferences`, `tentative_interpretations`, `assumptions`, and `material_unknowns`
* `primary_risk`: `W_UNSUPPORTED_LEAP` | `A_ASSUMPTION_CONTAMINATION` | `I_IDENTITY_OVERREACH` | `T_TUNNEL_VISION` | `NONE`
* `recommended_action`: `PASS` | `SOFT_RELABEL` | `SURGICAL_PRUNE` | `SPLIT` | `ASK` | `BRANCH_REWIND`
* `retry_constraint`: structured Negative Constraint Guard (`do_not_assume`, `treat_as_unknown`, `preserve_alternatives`)

---

## Quick Start

WAIT v0.1 is **prompt- and protocol-first**, requiring zero external runtime dependencies.

```bash
git clone https://github.com/negentropy-ark/wait.git
cd wait
```

### Option A: Use as a Plug-and-Play Agent Skill (`SKILL.md`)
Drop [`SKILL.md`](./SKILL.md) into your AI coding assistant or Skill-compatible agent directory (e.g., Codex, Claude Code, or custom MCP skill folders). Invoke **`wait-epistemic-review`** before committing to complex plans, architectural decisions, or persona-sensitive outputs.

### Option B: Integrate as an Independent Multi-Agent Review Node
1. Load [`prompts/review-agent-system.md`](./prompts/review-agent-system.md) (**v0.2**) as the system prompt of an independent reviewer agent.
2. Validate structured outputs against [`schemas/review-result.schema.json`](./schemas/review-result.schema.json).
3. Route your orchestrator based on `review_status` (`PASS` $\rightarrow$ continue; `FLAG` $\rightarrow$ `SOFT_RELABEL` / `SURGICAL_PRUNE` / `SPLIT`; `INTERRUPT` $\rightarrow$ `TRACE` & `BRANCH_REWIND`).

---

## Repository Structure

Current v0.1 repository layout:

```text
wait/
├── .gitignore
├── CONTRIBUTING.md
├── LICENSE
├── README.md
├── SKILL.md                               # Plug-and-play Agent Skill entrypoint
├── examples/
│   ├── empathy-misread-4o.md              # Case 01: Philosophical empathy vs. identity leap
│   └── reward-card-paradox.md             # Case 02: Hidden prerequisite leap & hard interrupt
├── prompts/
│   └── review-agent-system.md             # Core WAIT v0.2 System Prompt specification
├── schemas/
│   └── review-result.schema.json          # Machine-readable JSON Schema for orchestrators
└── tests/
    └── cases/
        └── core-evaluation-cases.json     # Starter 5-case evaluation & calibration suite
```

---

## WAIT Bench & Roadmap

* **v0.1 — Protocol & Skill Foundation (Current)**
  * Core `W-A-I-T`, `TRACE`, `SPLIT`, `Heuristic Shortcut Audit`, and `3-Tier Micro-Rollback` specifications.
  * Reference system prompt ([`prompts/review-agent-system.md`](./prompts/review-agent-system.md) v0.2) and plug-and-play [`SKILL.md`](./SKILL.md).
  * Machine-readable JSON Schema ([`schemas/review-result.schema.json`](./schemas/review-result.schema.json)), real-world case walkthroughs, and 5-case starter test suite.
* **v0.2 — Automated WAIT Bench & Calibration**
  * Expanded benchmark covering 7 epistemic dimensions (Hidden Assumption, Contamination Tracking, De-Labeling, Premature Convergence, Context Override, Earliest Divergence, and Micro-Rollback Precision).
  * Automated evaluation scripts measuring both false negatives (missed assumptions) and false positives (over-correction / phantom attribution).
* **v0.3 — Multi-Agent Middleware & Adapters**
  * Ready-to-use middleware nodes for **LangGraph**, **OpenAI Agents**, **MCP workflows**, **Auto-Drama pipelines**, and **Policy Simulation frameworks**.
  * Automated **Assumption Dependency Graphs** to invalidate downstream claims when an upstream premise fails.

---

## Contributing

The most valuable contributions to WAIT are **real-world failure cases** (and calibration control cases) where an AI system needed a human to pause the pipeline and say: **"WAIT..."**

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for the 5-field case submission template and PR checklist.

---

## License

Released under the **Apache License 2.0**. See [`LICENSE`](./LICENSE) for details.

---

## Status

**Early Experimental Project — v0.1**

> **Turn “等等” (WAIT) into a testable AI epistemic review mechanism.**