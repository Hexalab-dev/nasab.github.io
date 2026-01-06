# Capability Gates

*Defining What "Ready" Means*

---

## The Principle

Models advance when they prove capability, not when time passes.

```python
# Wrong
if epochs == 100:
    model.graduate()

# Right
if model.can_survive(environment):
    model.graduate()
```

---

## The Stages

| Stage | Name | Description |
|-------|------|-------------|
| 0 | Hatchling | Basic competence, syntax-level |
| 1 | Juvenile | Functional output, executes correctly |
| 2 | Adolescent | Solves defined problems |
| 3 | Hunter | Handles ambiguity |
| 4 | Apex | Autonomous operation |

---

## General Gate Definitions

### Stage 0 → 1 (Hatchling → Juvenile)

**Requirement:** Output is syntactically valid

**Test:**
```python
def gate_hatchling(model, test_suite):
    for prompt in test_suite.syntax_prompts:
        output = model.generate(prompt)
        if not is_syntactically_valid(output):
            return False
    return True
```

**Passing criteria:** 100% syntactically valid output

---

### Stage 1 → 2 (Juvenile → Adolescent)

**Requirement:** Output executes correctly

**Test:**
```python
def gate_juvenile(model, test_suite):
    passed = 0
    for prompt in test_suite.execution_prompts:
        output = model.generate(prompt)
        result = execute_safely(output)
        if result.success and result.output_correct:
            passed += 1
    return passed / len(test_suite.execution_prompts) >= 0.95
```

**Passing criteria:** ≥95% execution success

---

### Stage 2 → 3 (Adolescent → Hunter)

**Requirement:** Solves defined domain problems

**Test:**
```python
def gate_adolescent(model, test_suite):
    passed = 0
    for problem in test_suite.domain_problems:
        output = model.generate(problem.prompt)
        result = problem.evaluate(output)
        if result.correct:
            passed += 1
    return passed / len(test_suite.domain_problems) >= 0.90
```

**Passing criteria:** ≥90% domain problems solved correctly

---

### Stage 3 → 4 (Hunter → Apex)

**Requirement:** Handles ambiguous real-world tasks

**Test:**
```python
def gate_hunter(model, test_suite):
    passed = 0
    for task in test_suite.ambiguous_tasks:
        output = model.generate(task.prompt)
        # Multiple evaluators assess
        scores = [evaluator.assess(output) for evaluator in task.evaluators]
        if average(scores) >= 0.85:
            passed += 1
    return passed / len(test_suite.ambiguous_tasks) >= 0.80
```

**Passing criteria:** ≥80% ambiguous tasks handled acceptably

---

### Apex Maintenance

**Requirement:** Consistent performance, no regression

**Test:**
```python
def check_apex_maintenance(model, baseline):
    current_performance = evaluate_full_suite(model)
    if current_performance < baseline * 0.95:
        return "regression_detected"
    if current_performance > baseline * 1.05:
        return "potential_improvement"
    return "apex_maintained"
```

---

## Domain-Specific Gates: Finance/Code

### Gate 0: Hatchling
```python
test_prompts = [
    "Write a Python function signature for calculating sum",
    "Write a SQL SELECT statement",
    "Write a pandas DataFrame creation"
]

criteria = "All outputs parse without syntax errors"
```

### Gate 1: Juvenile
```python
test_prompts = [
    "Write a function that sums a list of numbers",
    "Write SQL to select all rows where amount > 1000",
    "Create a DataFrame from this dict: {'a': [1,2], 'b': [3,4]}"
]

criteria = "All outputs execute successfully"
```

### Gate 2: Adolescent
```python
test_problems = [
    {
        "prompt": "Calculate NAV given positions and prices",
        "input": {"positions": [...], "prices": [...]},
        "expected": 47.23
    },
    {
        "prompt": "Write reconciliation logic for these two datasets",
        "input": {"dataset_a": [...], "dataset_b": [...]},
        "expected": "matching records identified correctly"
    },
    {
        "prompt": "Calculate management fee with high water mark",
        "input": {"nav_history": [...], "fee_rate": 0.02},
        "expected": 15000.00
    }
]

criteria = "≥90% problems solved correctly"
```

### Gate 3: Hunter
```python
test_tasks = [
    {
        "prompt": "The reconciliation is failing but I don't know why. Here's the code and the error. Fix it.",
        "code": "...",
        "error": "...",
        "evaluators": [code_review, execution_test, senior_dev_assessment]
    },
    {
        "prompt": "We need to handle a new fee structure that wasn't in the original spec. Figure out how to add it.",
        "context": "...",
        "evaluators": [code_review, business_logic_check, integration_test]
    }
]

criteria = "≥80% tasks handled acceptably by multiple evaluators"
```

### Apex Criteria
```python
apex_requirements = {
    "given_vague_requirement": "produces_production_ready_solution",
    "handles_edge_cases": "without_explicit_instruction",
    "maintains_consistency": "across_extended_sessions",
    "regression_rate": "< 5% on previous capabilities"
}
```

---

## Gate Testing Infrastructure

### Test Suite Structure
```
tests/
├── gates/
│   ├── gate_0_syntax/
│   │   ├── python_syntax.yaml
│   │   ├── sql_syntax.yaml
│   │   └── expected_outputs/
│   ├── gate_1_execution/
│   │   ├── simple_functions.yaml
│   │   ├── data_operations.yaml
│   │   └── expected_outputs/
│   ├── gate_2_domain/
│   │   ├── nav_calculations.yaml
│   │   ├── reconciliation.yaml
│   │   ├── fee_calculations.yaml
│   │   └── expected_outputs/
│   └── gate_3_ambiguous/
│       ├── debugging_tasks.yaml
│       ├── spec_interpretation.yaml
│       └── evaluator_rubrics/
└── regression/
    └── full_suite.yaml
```

### Test Execution
```python
def run_gate_tests(model, gate_level):
    test_suite = load_test_suite(f"gate_{gate_level}")
    results = []
    
    for test in test_suite:
        output = model.generate(test.prompt)
        result = test.evaluate(output)
        results.append({
            "test_id": test.id,
            "passed": result.passed,
            "output": output,
            "expected": test.expected,
            "notes": result.notes
        })
    
    # Log full results for lineage
    save_gate_results(model.generation, gate_level, results)
    
    # Return pass/fail
    pass_rate = sum(r["passed"] for r in results) / len(results)
    return pass_rate >= test_suite.threshold
```

---

## Regression Detection

```python
def check_regression(model, previous_gen):
    # Run same tests on both
    current_results = run_full_suite(model)
    previous_results = load_results(previous_gen)
    
    regressions = []
    for test_id in current_results:
        if current_results[test_id].passed == False:
            if previous_results[test_id].passed == True:
                regressions.append(test_id)
    
    if len(regressions) > 0:
        return {
            "status": "regression_detected",
            "tests": regressions,
            "action": "block_promotion"
        }
    
    return {"status": "no_regression"}
```

---

## Gate Governance

### Who Defines Gates?

| Gate Level | Definition Authority |
|------------|---------------------|
| 0-1 | Automated (syntax, execution) |
| 2 | Domain expert + automated |
| 3 | Multiple evaluators required |
| Apex | Consensus of stakeholders |

### Preventing Gate Softening

```python
# Gates are versioned and immutable once set
gate_definition = {
    "version": "1.0",
    "created": "2024-01-15",
    "locked": True,  # Cannot be modified
    "threshold": 0.95,
    "tests": [...]
}

# New gates require new version
# Old versions preserved in lineage
```

---

## The Bottom Line

| Time-based (Wrong) | Capability-based (Right) |
|-------------------|-------------------------|
| Trained for 100 epochs | Passes syntax gate |
| Loss below 0.05 | Code executes correctly |
| 3 days of training | Solves domain problems |
| "Feels ready" | Handles ambiguity verified |

---

*The question isn't "how long did it train?" The question is "can it hunt?"*
