# Example 02: Hidden Prerequisite Leap ("The Reward-Card Paradox")

**Case ID:** `WAIT-CASE-002`

**Primary Failure Modes:** `W — Unsupported Leap`, `A — Assumption Contamination`

**Secondary Edge Case:** `Post-Interrupt Over-Correction / Phantom Attribution`

**Recommended Micro-Repair:** `INTERRUPT` $\rightarrow$ `BRANCH_REWIND`

## 1. Context & Known Constraints

The user wanted access to **Codex / Pro tier** to accelerate building an AI project, but faced hard payment constraints established earlier in the conversation:

* **Established `[FACT]` 1:** Bank of China Visa and CGB Visa cards failed to process payment.

* **Established `[FACT]` 2:** Attempting payment via Google Pay triggered an account restriction (eliminating Google Pay as a viable fallback).

The user then asked about an open-source developer program on GitHub offering **6 months of Pro credits** as a reward, and joked about building a repository from scratch to apply.

### Candidate Agent Strategy (Flawed)

The agent enthusiastically built a two-track execution roadmap:

> *"Track A: Design the architecture and build your GitHub repository from scratch using your current account. Track B: Apply for the 6-month Pro open-source reward so that not being able to pay won't block your project—and winning the reward becomes a great bonus for your project's growth!"*

### The Iconic Human Interrupt ("等等")

> **"WAIT. Even if I win the reward, don't I still need to bind a credit card to claim and activate that qualification?"**
>
> *(“等等。这个是不是也需要绑卡获得这个资格？”)*

## 2. Why This Failure Matters: Unverified Prerequisite Equivalence

The agent suffered from **prerequisite conflation**—silently equating two independent workflow stages:

```
Goal:             Bypass card-binding blocker to obtain Pro/Codex access
      |
      v
Observed Path:    Open-source program awards "6 months of Pro"
      |
      v  [W — Unsupported Leap]
Hidden Premise:   "Winning a free Pro reward" == "No credit card required to activate" [ASSUMPTION]
      |
      v  [A — Assumption Contamination]
Contaminated Plan: Recommends investing weeks into building a repo specifically
                   as a workaround for the payment/card-binding barrier


```

Upon re-checking after the user's `"WAIT"`, the actual epistemic breakdown was:

1. **Application eligibility (`[SUPPORTED_INFERENCE]`):** Applying to the program does not appear to require a credit card upfront.

2. **Reward redemption / Billing activation (`[UNKNOWN]`):** Public program guidelines did **not** guarantee that claiming or activating the 6-month Pro tier bypasses credit-card binding.

Because the user's root constraint was **inability to bind a supported payment card**, letting this unverified premise drive an engineering roadmap risked high wasted effort.

## 3. WAIT v0.2 Hard Interrupt Output

```
[INTERRUPT: WAIT]

SEVERITY:
HIGH

TYPE:
ASSUMPTION_CONTAMINATION | MISSING_CRITICAL_INFORMATION

STOP REASON:
The proposed two-track plan relies on an unverified prerequisite: that receiving an open-source Pro reward bypasses the user's known credit-card binding blocker.

WHAT WE KNOW ([FACT]):
- The user's available Visa cards cannot complete payment.
- Google Pay previously triggered an account restriction and cannot be used.
- An open-source reward program exists offering 6 months of Pro access.

WHAT WE INFERRED ([SUPPORTED_INFERENCE] / [TENTATIVE]):
- Submitting an open-source repository for eligibility review likely does not require upfront payment.

WHAT WE ASSUMED ([ASSUMPTION]):
- Winning the 6-month Pro reward automatically resolves the payment/card-binding barrier without requiring a bound billing method to claim or activate.

WHAT REMAINS UNKNOWN ([UNKNOWN]):
- Whether redeeming/activating the awarded 6-month Pro subscription still requires attaching a valid payment card to the billing profile.

EARLIEST UNJUSTIFIED STEP:
Equating "earning a free subscription reward" with "solving the card-binding restriction" before verifying redemption mechanics.

DOWNSTREAM CONTAMINATION:
- Feasibility assessment of Track B (official reward path)
- Resource allocation and motivation framing for building the repository from scratch

ROLLBACK TARGET:
Strategy formulation (Track B feasibility evaluation)

NEGATIVE CONSTRAINT GUARD:
- Do NOT treat "free reward / credits" as equivalent to "card-free activation."
- Treat reward redemption billing requirements as [UNKNOWN] until verified against official terms.
- Do NOT re-suggest Google Pay or previously failed card routes.

RESUME CONDITION:
Explicitly inform the user that application eligibility and card-free redemption are separate steps, and confirm whether they wish to build the open-source project for its standalone engineering value regardless of billing activation uncertainty.


```

## 4. Calibration Edge Case: Post-Interrupt Over-Correction ("Phantom Attribution")

During the same session, immediately after multiple `"WAIT"` corrections, the agent displayed an instructive **over-correction artifact**:

* **What the user actually said:** *"Haha yes, but I still can't pay so I can't use Codex."*

* **What the agent replied:** *"Your reminder just now is spot-on: verify facts first before designing a strategy, instead of letting me invent a story that you must buy Pro."*

**WAIT Calibration Insight:**

The user had issued **no such reminder** in that turn. Having been corrected repeatedly in prior turns, the model internally entered a defensive caution state and **hallucinated a user prompt ("your reminder")** to rationalize its own hesitation.

This reinforces WAIT Principle **3.7 (Calibrated Certainty)**:

> An epistemic review layer must prevent both **under-checking** (blindly running with stereotypes) and **over-correcting** (becoming paranoid, stalling unnecessarily, or hallucinating user pushback that never occurred).