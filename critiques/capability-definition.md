# Critique: Capability Definition

*Who Decides "Ready"?*

---

## The Problem

Reptilian Gates require clear capability definitions.

But in software, unlike biology, **failure is ambiguous**.

---

## In Nature

Death defines failure. Unambiguously.

```
Lion cub attempts hunt
       ↓
Succeeds → Survives → Reproduces
Fails → Dies → No offspring
```

The gate is ruthless and objective.

---

## In Software

```
Model attempts task
       ↓
Partially succeeds?
Sort of works?
Works for some inputs?
Works but slowly?
Works but not elegantly?
```

Who decides if that's "passing"?

---

## The Governance Challenge

| Question | Who Decides? |
|----------|--------------|
| What counts as "syntactically valid"? | Easy — compiler |
| What counts as "executes correctly"? | Harder — test suite coverage |
| What counts as "solves domain problems"? | Political — stakeholders disagree |
| What counts as "handles ambiguity"? | Subjective — no clear metric |
| What counts as "production-ready"? | Contested — varies by organization |

As stages advance, objectivity decreases.

---

## The Risk

Without ruthless definition, gates **soften over time**.

```
Year 1: "Must pass 100% of test cases"
Year 2: "95% is basically good enough"
Year 3: "80% with manual workarounds"
Year 4: "We'll fix it in the next generation"
```

This is how all standards erode.

---

## Where Nasab is Strong

Domains with **hard reality anchors**:

| Domain | Reality Check |
|--------|---------------|
| Code | Compiles or doesn't. Runs or crashes. |
| Calculation | Correct or wrong. Verifiable. |
| Reconciliation | Balances or doesn't. |
| Unit tests | Pass or fail. |

In these domains, capability is objective.

---

## Where Nasab is Weak

Domains with **soft criteria**:

| Domain | Problem |
|--------|---------|
| Ethics | Who defines "correct" ethical judgment? |
| Policy | Political disagreement is inherent |
| Creative work | Quality is subjective |
| Strategy | Success only visible in hindsight |
| Persuasion | Effectiveness varies by audience |

In these domains, gates become political.

---

## Possible Mitigations

### 1. Restrict Scope
Only claim Nasab for domains with verifiable ground truth.

Honest limitation: "This framework works for code, calculation, and verifiable domains. For ethics and policy, different approaches needed."

### 2. Multi-stakeholder Gates
Multiple independent evaluators must agree.

Risk: Lowest common denominator.

### 3. Adversarial Testing
Red team actively tries to break the model.

If it survives adversarial conditions, it's more likely robust.

### 4. Time-delayed Evaluation
Don't evaluate immediately. Wait and see if the solution holds up.

Slower, but catches false positives.

### 5. Explicit Criteria Documentation
Write down exact criteria before evaluation.

Prevents post-hoc rationalization.

---

## The Honest Answer

Nasab doesn't fully solve this.

**Capability definition is a governance problem, not a technical problem.**

The technical architecture can enforce gates. It cannot define what "passing" means in contested domains.

---

## Implication for Implementation

### For Nasab v1
Focus on domains where reality is verifiable:
- Financial code
- Data pipelines
- Calculations
- Reconciliations

### For Future Extensions
Acknowledge that expanding to soft domains requires solving governance, not just architecture.

---

*The gate is only as strong as its definition. And definitions are human choices.*
