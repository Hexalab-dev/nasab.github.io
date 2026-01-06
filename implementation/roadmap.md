# Technical Roadmap

*From Concept to Implementation*

---

## Hardware Baseline

| Component | Specification |
|-----------|---------------|
| GPU | NVIDIA RTX 4070 (12GB VRAM) |
| Environment | WSL2 on Windows |
| Feasible approach | QLoRA fine-tuning, not pre-training |
| Base model size | 7B parameters (quantized) or 3B full precision |

---

## Phase 0: Environment Setup (Week 1)

### WSL2 + CUDA Setup

```bash
# Update WSL
wsl --update

# Install Ubuntu 24.04
wsl --install -d Ubuntu-24.04

# Inside WSL, install CUDA toolkit
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-4
```

### Python Environment

```bash
# Create virtual environment
python3 -m venv nasab-env
source nasab-env/bin/activate

# Install core dependencies
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
pip install transformers
pip install peft  # For LoRA/QLoRA
pip install bitsandbytes  # For 4-bit quantization
pip install datasets
pip install accelerate
pip install pandas numpy
pip install pytest  # For gate testing
```

### Verify Setup

```python
import torch
print(f"CUDA available: {torch.cuda.is_available()}")
print(f"GPU: {torch.cuda.get_device_name(0)}")
print(f"Memory: {torch.cuda.get_device_properties(0).total_memory / 1e9:.1f} GB")
```

---

## Phase 1: Data Preparation (Weeks 2-3)

### Source Inventory

| Source | Content | Format |
|--------|---------|--------|
| Your codebases | Python, SQL, VBA | Files |
| Claude exports | Conversations | JSON/Markdown |
| Cursor history | Code sessions | Workspace files |
| Gemini exports | Conversations | Google Takeout |

### Export Procedures

**Claude:**
Settings → Export Data → Download

**Gemini:**
Google Takeout → Select Gemini → Export

**Cursor:**
Check `.cursor/` folder in project directories

### Processing Pipeline

```python
# Structure: raw → processed → training-ready

def process_codebase(path):
    """Extract code samples with context"""
    samples = []
    for file in walk_directory(path):
        if is_code_file(file):
            samples.append({
                "content": read_file(file),
                "language": detect_language(file),
                "path": file,
                "context": extract_context(file)
            })
    return samples

def process_chat_export(path):
    """Extract instruction-response pairs"""
    pairs = []
    conversations = load_conversations(path)
    for conv in conversations:
        for i, msg in enumerate(conv.messages):
            if msg.role == "user" and i+1 < len(conv.messages):
                pairs.append({
                    "instruction": msg.content,
                    "response": conv.messages[i+1].content,
                    "corrections": extract_corrections(conv, i)
                })
    return pairs
```

### Output Format

```
processed_data/
├── code_samples.jsonl
├── instruction_pairs.jsonl
├── refinements.jsonl      # Correction chains
└── metadata.json
```

---

## Phase 2: Base Model Setup (Week 3)

### Model Selection

| Model | Why | VRAM Usage |
|-------|-----|------------|
| **CodeQwen2-7B** | Strong code base, multilingual | ~6GB quantized |
| DeepSeek-Coder-7B | Excellent structured reasoning | ~6GB quantized |
| Mistral-7B | General but solid | ~6GB quantized |

**Recommendation:** Start with CodeQwen2-7B

### Download and Quantize

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import prepare_model_for_kbit_training

model_id = "Qwen/CodeQwen1.5-7B"

# Load in 4-bit
model = AutoModelForCausalLM.from_pretrained(
    model_id,
    load_in_4bit=True,
    device_map="auto"
)

tokenizer = AutoTokenizer.from_pretrained(model_id)

# Prepare for training
model = prepare_model_for_kbit_training(model)
```

---

## Phase 3: Generation 0 Training (Weeks 4-5)

### QLoRA Configuration

```python
from peft import LoraConfig, get_peft_model

lora_config = LoraConfig(
    r=16,                      # Rank
    lora_alpha=32,             # Alpha
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
```

### Training Script

```python
from transformers import TrainingArguments, Trainer

training_args = TrainingArguments(
    output_dir="./generations/gen_0",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    learning_rate=2e-4,
    fp16=True,
    save_strategy="epoch",
    logging_steps=10,
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    tokenizer=tokenizer,
)

trainer.train()
```

### Gate Testing

```bash
python scripts/run_gate_tests.py --generation 0 --gate 0
# Must pass syntax gate before proceeding
```

---

## Phase 4: Generational Loop (Weeks 6+)

### The Loop

```python
def generational_training_loop():
    current_gen = 0
    current_stage = 0  # Hatchling
    
    while current_stage < 4:  # Not yet Apex
        # Train this generation
        train_generation(current_gen)
        
        # Test capability gate
        if passes_gate(current_gen, current_stage):
            print(f"Gen {current_gen} passed stage {current_stage}")
            current_stage += 1
        else:
            print(f"Gen {current_gen} failed stage {current_stage}")
        
        # Check for regression
        if current_gen > 0:
            if regression_detected(current_gen, current_gen - 1):
                print("Regression! Rolling back.")
                rollback(current_gen)
                continue
        
        # Merge LoRA into base for next generation
        merge_lora(current_gen)
        current_gen += 1
        
        # Collect new training data (validated corrections)
        new_data = collect_validated_corrections()
        prepare_training_data(new_data, current_gen)
    
    print(f"Apex reached at generation {current_gen}")
```

---

## Phase 5: Verification Systems (Parallel)

### Calculator Module

```python
# verification/calculator.py
import ast
import operator

SAFE_OPERATORS = {
    ast.Add: operator.add,
    ast.Sub: operator.sub,
    ast.Mult: operator.mul,
    ast.Div: operator.truediv,
}

def safe_calculate(expression: str) -> float:
    """Execute calculation safely, no eval()"""
    tree = ast.parse(expression, mode='eval')
    return _eval_node(tree.body)

def _eval_node(node):
    if isinstance(node, ast.Num):
        return node.n
    elif isinstance(node, ast.BinOp):
        left = _eval_node(node.left)
        right = _eval_node(node.right)
        return SAFE_OPERATORS[type(node.op)](left, right)
    raise ValueError(f"Unsupported operation: {node}")
```

### Code Executor

```python
# verification/code_executor.py
import subprocess
import tempfile

def execute_code_safely(code: str, language: str, timeout: int = 30):
    """Execute code in sandbox, capture output"""
    with tempfile.NamedTemporaryFile(mode='w', suffix=get_suffix(language)) as f:
        f.write(code)
        f.flush()
        
        try:
            result = subprocess.run(
                get_interpreter(language) + [f.name],
                capture_output=True,
                timeout=timeout,
                text=True
            )
            return {
                "success": result.returncode == 0,
                "stdout": result.stdout,
                "stderr": result.stderr
            }
        except subprocess.TimeoutExpired:
            return {"success": False, "error": "timeout"}
```

### Checklist Enforcer

```python
# verification/checklist_enforcer.py

VAT_CHECKLIST = [
    "parties_identified",
    "jurisdictions_identified", 
    "b2b_or_b2c_determined",
    "goods_services_digital_classified",
    "exemptions_checked",
    "thresholds_verified",
    "reverse_charge_assessed",
    "place_of_supply_determined"
]

def enforce_checklist(query_type: str, context: dict) -> dict:
    """Ensure all checklist items addressed before response"""
    checklist = get_checklist(query_type)
    missing = []
    
    for item in checklist:
        if item not in context or context[item] is None:
            missing.append(item)
    
    if missing:
        return {
            "complete": False,
            "missing": missing,
            "action": "gather_missing_info"
        }
    
    return {"complete": True}
```

---

## Phase 6: State & Knowledge Systems (Parallel)

### State Database

```python
# Using SQLite for simplicity
import sqlite3

def init_state_db():
    conn = sqlite3.connect('state/nasab_state.db')
    conn.execute('''
        CREATE TABLE IF NOT EXISTS actions (
            id INTEGER PRIMARY KEY,
            timestamp TEXT,
            action_type TEXT,
            content TEXT,
            author TEXT,
            session_id TEXT
        )
    ''')
    conn.execute('''
        CREATE TABLE IF NOT EXISTS knowledge (
            id INTEGER PRIMARY KEY,
            content TEXT,
            retrieval_weight REAL,
            validation_count INTEGER,
            created_at TEXT,
            last_validated TEXT,
            lineage TEXT
        )
    ''')
    return conn
```

### Knowledge Retrieval

```python
def retrieve_knowledge(query: str, mode: str = "normal"):
    """Retrieve relevant knowledge based on mode"""
    if mode == "normal":
        threshold = 0.5
    elif mode == "deep":
        threshold = 0.0  # Access everything
    
    # Simple keyword matching for MVP
    # Replace with vector similarity for production
    results = db.execute('''
        SELECT * FROM knowledge 
        WHERE retrieval_weight >= ?
        ORDER BY retrieval_weight DESC
        LIMIT 10
    ''', (threshold,))
    
    return results.fetchall()
```

---

## Timeline Summary

| Phase | Duration | Outcome |
|-------|----------|---------|
| 0: Environment | Week 1 | CUDA + Python ready |
| 1: Data | Weeks 2-3 | Training data prepared |
| 2: Base Model | Week 3 | CodeQwen2 quantized and ready |
| 3: Gen 0 | Weeks 4-5 | First generation trained, Gate 0 passed |
| 4: Loop | Weeks 6+ | Generational improvement |
| 5: Verification | Parallel | Calculator, executor, checklists |
| 6: State | Parallel | Persistence, knowledge store |

---

## Milestones

| Milestone | Definition |
|-----------|------------|
| **M1** | Environment working, base model loads |
| **M2** | Gen 0 passes Gate 0 (syntax) |
| **M3** | Gen N passes Gate 1 (execution) |
| **M4** | Gen N passes Gate 2 (domain problems) |
| **M5** | Gen N passes Gate 3 (ambiguity) |
| **M6** | Apex: Stable, autonomous operation |

---

## The Crocodile Reminder

```
Year 1: Code generation for YOUR workflows
        Perfect it. Test it. Validate it.
        
Year 2: Add finance layer
        Perfect it. Test it. Validate it.
        
Year 3: Maybe nothing new
        Just refinement
        Apex maintenance
```

**Resist the urge to rush.**

---

*The roadmap is a guide, not a prison. Adjust based on what you learn.*
