# Architecture Overview

*System Design for Nasab*

---

## Core Philosophy

```
Current Industry:  Model is the intelligence, tools are helpers
Nasab:             System is the intelligence, model is the interface
```

The model becomes the **mouth**, not the brain.

It translates between human language and system operations.
It doesn't decide. It doesn't calculate. It doesn't verify.

**It speaks.**

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      USER INTERFACE                         │
│                  (Natural Language I/O)                     │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    LANGUAGE MODEL                           │
│              (Interface Layer — Speaks)                     │
│         Translates between human and system                 │
└─────────────────────────────┬───────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   VERIFICATION  │ │     STATE       │ │    KNOWLEDGE    │
│     SYSTEMS     │ │   MANAGEMENT    │ │      STORE      │
│                 │ │                 │ │                 │
│ • Calculator    │ │ • Actions done  │ │ • Validated     │
│ • Code executor │ │ • Decisions     │ │ • Dormant       │
│ • Checklist     │ │ • Error trace   │ │ • Deep storage  │
│ • Math prover   │ │ • Authorship    │ │ • Full lineage  │
└─────────────────┘ └─────────────────┘ └─────────────────┘
          │                   │                   │
          └───────────────────┼───────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  VALIDATION PIPELINE                        │
│                                                             │
│  Layer 1: Human Consensus    (Did users confirm?)           │
│  Layer 2: Internal Consistency (Contradictions?)            │
│  Layer 3: Reality Check      (Execution, math, facts)       │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│               GENERATIONAL TRAINING                         │
│                                                             │
│  Incorporate validated knowledge into next generation       │
│  Gate advancement by capability, not time                   │
│  Preserve full lineage                                      │
└─────────────────────────────────────────────────────────────┘
```

---

## Component Details

### 1. Language Model (Interface Layer)

**Role:** Translation only

| It Does | It Doesn't |
|---------|------------|
| Parse user intent | Make decisions |
| Generate natural language | Calculate |
| Route to appropriate systems | Verify its own output |
| Format responses | Store state |

**Implementation:**
- Base: CodeQwen2-7B or Mistral-7B (4-bit quantized)
- Method: QLoRA fine-tuning
- Advancement: Generational, gated by capability

---

### 2. Verification Systems

**Calculator**
```python
# Model NEVER calculates directly
def handle_calculation(expression):
    # Parse expression
    # Execute in sandboxed calculator
    # Return verified result
    # Log: what was calculated, which method, timestamp
```

**Code Executor**
```python
# Model's code is always executed to verify
def verify_code(code):
    # Syntax check
    # Execute in sandbox
    # Capture output/errors
    # Return: success/failure + output
```

**Domain Checklist**
```python
# Forced completion before responding
def vat_checklist(query):
    checklist = [
        "parties_identified",
        "jurisdictions_identified",
        "b2b_or_b2c",
        "goods_or_services",
        "exemptions_checked",
        "thresholds_checked",
        "reverse_charge_checked",
        "place_of_supply_checked"
    ]
    # Block response until all checked
```

**Math Epistemology**
```python
# Track proof status of all mathematical claims
def get_formula_status(formula):
    return {
        "type": "theorem|conjecture|model|disproven",
        "status": "proven|unproven|refuted",
        "assumptions": [...],
        "limitations": [...],
        "lineage": [...]
    }
```

---

### 3. State Management

**Persistent State Database**
```
state_db/
├── actions/          # What has been done
├── decisions/        # What was decided and why
├── errors/           # Errors and their causes
├── authorship/       # Who wrote what
└── context/          # Current session context
```

**Authorship Tagging**
```json
{
  "content": "def calculate_nav(): ...",
  "author": "nasab_gen_4",
  "timestamp": "2024-01-15T14:32:00Z",
  "context": "user requested NAV function",
  "confidence": 0.87,
  "verification_status": "executed_successfully",
  "lineage": ["gen_3_refinement", "gen_2_base", "gen_1_template"]
}
```

---

### 4. Knowledge Store

**Layered Architecture**
```
┌─────────────────────────────────────┐
│          Active Layer               │
│   retrieval_weight > 0.8            │
│   Recent, validated, frequently used│
├─────────────────────────────────────┤
│          Dormant Layer              │
│   retrieval_weight 0.3 - 0.8        │
│   Older, less used, still valid     │
├─────────────────────────────────────┤
│          Deep Storage               │
│   retrieval_weight < 0.3            │
│   Historical, superseded            │
├─────────────────────────────────────┤
│          Full Lineage               │
│   All generations, all versions     │
│   Never deleted                     │
└─────────────────────────────────────┘
```

**Retrieval Weight Function**
```python
def calculate_retrieval_weight(knowledge):
    return weighted_average(
        validation_count,      # More validation = higher
        recency,               # More recent = higher
        usage_frequency,       # More used = higher
        reality_anchor_strength # Verified = higher
    )
```

---

### 5. Validation Pipeline

**Three Layers**
```python
def validate_knowledge(correction):
    # Layer 1: Human Consensus
    consensus = get_user_confirmations(correction)
    if consensus.count < threshold:
        return "insufficient_consensus"
    
    # Layer 2: Internal Consistency
    conflicts = check_contradictions(correction)
    if conflicts:
        return "conflicts_detected", conflicts
    
    # Layer 3: Reality Check
    if correction.type == "code":
        result = execute_code(correction.content)
        if not result.success:
            return "reality_check_failed"
    elif correction.type == "calculation":
        result = verify_calculation(correction.content)
        if not result.correct:
            return "reality_check_failed"
    
    # All layers passed
    return "validated"
```

---

### 6. Generational Training

**Training Loop**
```python
def train_generation(current_gen):
    # Load previous generation
    model = load_generation(current_gen - 1)
    
    # Get validated corrections since last generation
    new_knowledge = get_validated_knowledge(since=last_training)
    
    # Fine-tune with QLoRA
    model = train_qlora(model, new_knowledge)
    
    # Test capability gate
    if passes_gate(model, current_stage):
        promote(model)
        save_generation(model, current_gen)
        return "promoted"
    else:
        return "gate_failed"
```

---

## Data Flow Example

**User asks:** "Calculate the NAV for this fund"

```
1. USER INPUT
   "Calculate the NAV for this fund with these positions..."
   
2. LANGUAGE MODEL (parses intent)
   Intent: NAV_calculation
   Required: positions, prices, shares
   
3. CHECKLIST ENFORCEMENT
   □ Positions extracted
   □ Prices current
   □ Shares outstanding confirmed
   □ Fee accruals checked
   □ Pending trades handled
   
4. EXTERNAL CALCULATION
   Calculator executes: sum(positions * prices) / shares
   Result: $47.23
   
5. VERIFICATION
   Sanity check against previous NAV
   Within expected range: ✓
   
6. KNOWLEDGE CHECK
   Formula used: Standard NAV
   Status: Proven, standard practice
   Assumptions: Mark-to-market, no pending settlements
   
7. RESPONSE GENERATION
   Model formats response with result + caveats
   
8. STATE UPDATE
   Log: calculation performed, method, result, timestamp
   
9. OUTPUT
   "The NAV is $47.23 per share, calculated using 
    mark-to-market positions as of [date]. Note: This 
    assumes no pending settlements affecting positions."
```

---

## File Structure

```
nasab/
├── models/
│   └── generations/
│       ├── gen_0/
│       ├── gen_1/
│       └── current/
├── verification/
│   ├── calculator.py
│   ├── code_executor.py
│   ├── checklist_enforcer.py
│   └── math_epistemology.py
├── state/
│   ├── state_db/
│   └── state_manager.py
├── knowledge/
│   ├── store/
│   ├── retrieval.py
│   └── validation.py
├── training/
│   ├── train_generation.py
│   ├── capability_gates.py
│   └── lineage_tracker.py
├── interface/
│   └── language_interface.py
└── config/
    └── generation_config.yaml
```

---

*The model speaks. The system thinks.*
