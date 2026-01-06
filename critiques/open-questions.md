# Open Questions

*What Remains Unsolved*

---

## Honest frameworks acknowledge their limitations.

These questions don't have answers yet.

---

## 1. Grounding Without Reality

**The Problem:**
A child learns "hot" by touching something hot.
An LLM learns "hot" by seeing it near words like "fire," "burn," "temperature."

The LLM has no grounding. Words point to other words, never to reality.

**For Nasab:**
Code execution provides grounding — code touches reality.
But for non-code knowledge, this remains unsolved.

**Question:** Should Nasab refuse to learn things it cannot ground/verify?

---

## 2. Scale of Collective Validation

**The Problem:**
The validation pipeline assumes:
- Users provide corrections
- Corrections are compared across users
- Statistical thresholds determine truth

**Questions:**
- Can this scale to thousands or millions of users?
- How do you prevent gaming/manipulation at scale?
- Does quality of validation degrade as quantity increases?

---

## 3. Novelty and Local Optima

**The Problem:**
Nasab emphasizes refinement over exploration.

Generational learning compounds what works.
Crocodile strategy perfects the niche.

But pure refinement leads to local optima.

**Questions:**
- Can a refinement-based system produce genuine novelty?
- How much exploration (mutation) is enough?
- When does "apex" become "stuck"?

---

## 4. Governance Decay

**The Problem:**
Capability gates require clear definitions.
Clear definitions require governance.
Governance erodes over time.

```
Year 1: Strict standards
Year 5: "Pragmatic" exceptions
Year 10: Standards exist on paper only
```

**Questions:**
- How do you prevent gate softening?
- Who watches the gatekeepers?
- Can governance be encoded in architecture, not policy?

---

## 5. Adversarial Corruption

**The Problem:**
If users can correct the model, users can poison it.

```
Malicious users submit false corrections
       ↓
Coordinate "confirmation"
       ↓
Bad knowledge incorporated
       ↓
Model corrupted
```

**Questions:**
- How do you distinguish coordinated attack from genuine consensus?
- Can reputation systems be gamed?
- What's the recovery path after successful corruption?

---

## 6. Cold Start Problem

**The Problem:**
Collective validation requires users.
Users require a useful system.
A useful system requires validated knowledge.

Circular dependency.

**Questions:**
- How do you bootstrap validation with no initial users?
- Can synthetic validation substitute for real users initially?
- When does the system become self-sustaining?

---

## 7. Cross-Domain Transfer

**The Problem:**
Nasab advocates narrow niche mastery (crocodile strategy).

But knowledge often transfers across domains.

**Questions:**
- Can an "apex" model in one domain contribute to another?
- How do you balance focus with cross-pollination?
- When does narrow focus become counterproductive?

---

## 8. Temporal Validity

**The Problem:**
Knowledge that was true can become false.

```
"Pluto is a planet" (true until 2006)
"The UK is in the EU" (true until 2020)
"Interest rates are near zero" (true until 2022)
```

**Questions:**
- How does the system handle knowledge that expires?
- Should there be automatic validity decay for time-sensitive facts?
- How do you distinguish "temporarily true" from "fundamentally true"?

---

## 9. Computational Cost

**The Problem:**
Permanent memory means storage grows forever.
Full lineage means audit trail grows forever.
Multiple validation layers mean computation per decision increases.

**Questions:**
- Is this sustainable at scale?
- What's the cost/benefit of complete memory vs. selective pruning?
- Can compression preserve lineage while reducing storage?

---

## 10. Human Dependency

**The Problem:**
Collective validation depends on humans.
Humans are:
- Slow
- Inconsistent
- Sometimes wrong
- Sometimes malicious
- Not always available

**Questions:**
- Can the system reduce human dependency over time?
- Is full automation of validation desirable?
- What human role is irreducible?

---

## 11. What Would Prove Nasab Wrong?

**The Intellectual Honesty Question:**

For any framework to be credible, it should specify what would falsify it.

**Possible Falsifiers:**
- If a patch-culture system consistently outperforms on reliability
- If generational training shows no improvement over single-run training
- If collective validation produces worse outcomes than single-expert judgment
- If "apex" models consistently get disrupted by faster-iterating competitors
- If mathematical epistemology adds cost but no measurable benefit

**The Commitment:**
If evidence accumulates against these pillars, the framework should update.

That's what intellectual honesty requires.

---

## The Meta-Question

Is it possible to build an AI system that is:
- Grounded in reality
- Validated by consensus
- Preserved across generations
- Patient enough to master
- Humble about its own limitations

**We don't know.**

Nasab is an attempt. Not a guarantee.

---

*Honest frameworks state what they don't know. This is that list.*
