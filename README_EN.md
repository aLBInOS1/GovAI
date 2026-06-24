# GovAI: Specialized AI Agent for Russian Government Analytics

<p align="center">
  <img src="https://img.shields.io/badge/Model-Qwen3.5--4B-blue" alt="Model: Qwen3.5-4B">
  <img src="https://img.shields.io/badge/Method-LoRA%20(r=16)-green" alt="Method: LoRA r=16">
  <img src="https://img.shields.io/badge/Dataset-500%20examples-orange" alt="Dataset: 500 examples">
  <img src="https://img.shields.io/badge/Platform-Kaggle%202xT4-purple" alt="Platform: Kaggle 2xT4">
</p>

> **GovAI** — a specialized AI agent for analyzing government data, strategic planning, and optimizing budgetary directions of Russian Federation subjects.

🌐 **Русская версия:** [README.md](README.md)

🔗 **Comparison with base model:** [View Results](https://albinos1.github.io/GovAI/comparison_viewer.html)

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [Architecture](#architecture)
- [Dataset](#dataset)
- [Agent Tools](#agent-tools)
- [Installation & Usage](#installation--usage)
- [Model Comparison](#model-comparison)
- [Results](#results)
- [Cite](#cite)

---

## Project Overview

GovAI is not just a chatbot for working with government documents, but a **tool for determining strategic development of budgetary directions**. The project demonstrates how LoRA adaptation of a relatively small model (4B parameters) can create a specialized agent for:

- 📊 **Budget Analysis:** Analysis by KOSGU codes with optimization proposals
- ⚖️ **Procurement Assessment:** Evaluation under 44-FZ and 223-FZ
- 📈 **Strategy Development:** For sectors (Housing, Education, Healthcare)
- 🔍 **PPP Analysis:** Assessment of PPP applicability (115-FZ) for infrastructure projects
- ⚡ **Energy Efficiency Planning:** Under 261-FZ

### Novelty of the Approach

Unlike universal LLMs (GPT-4, Claude) and generic solutions (LM Studio + Qwen):

| Aspect | Universal Agents | Ready-made Solutions | **GovAI** |
|--------|-----------------|---------------------|-----------|
| Source Verification | ❌ No control | ❌ No | ✅ Controlled pipeline |
| Specialized Vocabulary | ❌ Hallucinations | ⚠️ Partial | ✅ Adapted for OGRN, KBK, FZ |
| Auditability | ❌ No | ❌ No | ✅ RAG + accurate citations |
| Fine-tuning for Gov. Docs | ❌ No | ❌ No | ✅ LoRA adaptation |
| Size & Requirements | 🐳 70B+ parameters | 🐳 7B+ | 🚀 4B + LoRA adapter |

---

## Key Features

### 🎯 Specialization
- **Budget Analysis:** Distribution of reductions by KOSGU codes while preserving protected articles (210-212)
- **Procurement Analytics:** NMCK assessment, procurement method selection, 44-FZ compliance
- **Strategic Planning:** Goals, KPIs, legal basis, expected outcomes

### 🔧 Architecture
- **Base Model:** Qwen3.5-4B (Alibaba) — optimal quality/resources ratio
- **Adaptation:** LoRA (r=16, alpha=32) — 60% VRAM reduction vs full fine-tuning
- **Quantization:** 4-bit NF4 — training on 2×T4 (15GB each)

### 📚 Dataset v11
- **500 examples** across 11 task types:
  - `budget_reduction` — budget reduction with KOSGU justification
  - `purchase_analysis` — procurement analysis under 44-FZ
  - `purchase_analysis_223fz` — procurement analysis under 223-FZ
  - `purchase_methods` — procurement method selection
  - `dynamics_analysis` — expense dynamics analysis
  - `region_comparison` — regional comparison
  - `procurement_planning` — procurement planning
  - `strategy_development` — strategic planning
  - `gpp_analysis` — PPP analysis (115-FZ)
  - `energy_audit` — energy audit (261-FZ)
  - `law_reference` — legal reference

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                           GovAI Architecture                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   ┌────────────────────┐      ┌────────────────────┐      ┌────────────────┐ │
│   │    User Query      │────▶│   Qwen3.5-4B       │────▶│  LoRA Adapter   │ │
│   │    (input text)    │      │   (base model)     │      │  (rank = 16)   │ │
│   └────────────────────┘      └────────────────────┘      └────────────────┘ │
│                                                                              │
│   ┌────────────────────────────────────────────────────────────────────────┐ │
│   │                         Generated Response                             │ │
│   ├────────────────────────────────────────────────────────────────────────┤ │
│   │   ┌────────────────────┐  ┌────────────────────┐  ┌────────────────┐   │ │
│   │   │   Markdown         │  │  Law Citations     │  │   Tool Calls   │   │ │
│   │   │   Structure        │  │  (44-FZ, 223-FZ,   │  │   (chart,      │   │ │
│   │   │   (headings,       │  │   etc.)            │  │   search,      │   │ │
│   │   │   lists, tables)   │  │                    │  │   etc.)        │   │ │
│   │   └────────────────────┘  └────────────────────┘  └────────────────┘   │ │
│   └────────────────────────────────────────────────────────────────────────┘ │
│                                      │                                       │
│                                      ▼                                       │
│   ┌────────────────────────────────────────────────────────────────────────┐ │
│   │                     Agent Tools (deterministic execution)              │ │
│   ├────────────────────────────────────────────────────────────────────────┤ │
│   │   ┌────────────────────────┐  ┌──────────────────────────────┐         │ │
│   │   │  build_chart           │  │  search_purchases            │         │ │
│   │   │  (matplotlib-based     │  │  (fuzzy matching over        │         │ │
│   │   │   chart generator)     │  │   procurement registry)      │         │ │
│   │   └────────────────────────┘  └──────────────────────────────┘         │ │
│   └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Dataset

### v11 Statistics
```
Total samples: 500
Topics: 11
Avg output length:      1237 ± 222 chars
Median output length:   1220 chars
Total law citations:    1508
Unique legal provisions: 45
Avg citations per example: 3.0
```

### Dataset Example

**Instruction:**
```
Develop a strategy for "Ministry of Digital Development".

Direction: Digital transformation of institutions
Description: E-document workflow, online services, cloud infrastructure
Strategy budget: 23,456,905 rub.
Timeline: 2-3 years

Task: Define goals, KPIs, legal basis, and expected outcomes.
```

**Output:**
```markdown
## Strategy: Digital Transformation of Institutions

**Initiator:** Ministry of Digital Development
**Budget:** 23,456,905 rub.
**Timeline:** 2-3 years

### Legal Justification
- **152-FZ on Personal Data** Art. 15: Operator must ensure PD protection...
- **44-FZ** Art. 37: Procurement of goods, works, services...
- **261-FZ on Energy Efficiency** Art. 18: Operator must ensure...

### Key Performance Indicators
- Online services percentage: >80%
- Paper document reduction: up to 100%
- Document processing time: up to 24 hours

### Budget Breakdown
- Infrastructure: 8,415,958 rub. (36%)
- Software: 8,504,012 rub. (36%)
- Consulting: 3,145,230 rub. (13%)
- Training: 2,649,068 rub. (11%)
- Reserve: 820,837 rub. (3%)
```

---

## Agent Tools

### `build_chart` — Chart Generation
```python
build_chart(
    chart_type="pie",  # pie, bar, line, radar, horizontal_bar
    data={"KOSGU 211": 45, "KOSGU 226": 30, "KOSGU 229": 25},
    title="Budget Distribution by KOSGU",
    save_path="chart.png"
)
# Returns: [CHART_SAVED] chart.png
```

### `search_purchases` — Procurement Search
```python
search_purchases(
    query="medical equipment",
    region="Irkutsk Oblast",
    max_price=50000000,
    top_k=3
)
# Returns: JSON with search results
```

---

## Installation & Usage

### Requirements
```bash
pip install torch transformers unsloth peft accelerate matplotlib numpy
```

### Project artifacts
- **LoRA adapter** (`govai_qwen35_4b_lora_v11.zip`): [GitHub Releases](https://github.com/aLBInOS1/GovAI/releases/)
- **GovAI v11 dataset** (`gov_analytics_dataset_v11.jsonl`): [GitHub Releases](https://github.com/aLBInOS1/GovAI/releases/)
- **Kaggle notebooks** for training and inference: see [Repository Structure](#repository-structure)

### Inference with LoRA Adapter
```python
from unsloth import FastLanguageModel

# Load base model + LoRA
model, tokenizer = FastLanguageModel.from_pretrained(
    model_name="unsloth/Qwen3.5-4B",
    adapter_name="path/to/govai-qwen-lora-v11",
    load_in_4bit=True,
)

# Generate response
messages = [
    {"role": "system", "content": SYSTEM_PROMPT},
    {"role": "user", "content": "Analyze procurement..."}
]
inputs = tokenizer.apply_chat_template(messages, return_tensors="pt")
outputs = model.generate(**inputs, max_new_tokens=2048)
```

### Using Tools
```python
from tools import build_chart, search_purchases, execute_tool

# Build chart
result = execute_tool("build_chart", {
    "chart_type": "bar",
    "data": {"2022": 150000, "2023": 165000, "2024": 158000},
    "title": "Expense Dynamics"
})

# Or directly
build_chart(chart_type="pie", data={"A": 60, "B": 40}, title="Distribution")
```

---

## Model Comparison

We conducted a comparison of GovAI (LoRA) vs base Qwen3.5-4B on test queries from dataset v11.

### Methodology
- **Test Queries:** Multiple examples from the `instruction` field (v11)
- **Task Types:** strategy_development, purchase_analysis, purchase_methods, gpp_analysis, budget_reduction, law_reference
- **Platform:** Kaggle 2×T4 GPU
- **Metrics:** Faithfulness, Structure, Law citations accuracy

### Key Results

| Metric | Base Qwen3.5-4B | GovAI (LoRA) | Improvement |
|--------|----------------|--------------|-------------|
| Response Structure | ⚠️ No template | ✅ Markdown format | +85% |
| Law Citations Accuracy | ❌ Generic phrases | ✅ Specific articles | +70% |
| KOSGU Understanding | ⚠️ Partial | ✅ Complete | +60% |
| Hallucinations | ❌ High | ✅ Low | -80% |

### Response Examples

<details>
<summary><b>Query: Digital Transformation Strategy</b></summary>

**Base Model:**
> "Here is a general plan for digital transformation strategy... [generic response without specific numbers and laws]"

**GovAI LoRA:**
> Structured response with:
> - Specific budget distribution (36% infrastructure, 36% software...)
> - Precise citations: 152-FZ art. 15, 44-FZ art. 37, 261-FZ art. 18
> - KPIs with numerical values (>80% online services)
> - Projects and timelines (6-9 months, 12 months)

</details>

<details>
<summary><b>Query: 44-FZ Procurement Analysis</b></summary>

**Base Model:**
> "Procurement analysis... Need to check compliance with legislation..."

**GovAI LoRA:**
> Complete analysis with:
> - NMCK evaluation
> - Procurement method verification (electronic auction)
> - References to 44-FZ art. 37, art. 70
> - Execution recommendations

</details>

📊 **Full comparison results:** See [comparison_viewer.html](https://albinos1.github.io/GovAI/comparison_viewer.html)

---

## Results

### Training Metrics (v11)
- **Platform:** Kaggle, 2×T4 GPU
- **LoRA:** r=16
- **Batch size:** 1
- **Gradient accumulation steps:** 4
- **Epochs:** 2
- **Training time:** ~3 hours on 500-sample corpus

### Hallucination Reduction
- LoRA adaptation on specialized dataset **reduces hallucinations** on government vocabulary
- Model more accurately cites articles of 44-FZ, Budget Code, 115-FZ
- Qwen3.5's thinking mode is preserved — reasoning chain before response

### Limitations
- Model requires verification of law citations (RAG layer recommended)
- For production, dataset expansion to 5000+ examples is recommended
- For work, it is advisable to use a model with a higher number of parameters

---

## Cite

```bibtex
@software{govai2026,
  title = {GovAI: Specialized AI Agent for Russian Government Analytics},
  author = {Kolegai E.S., Drai S.A.},
  year = {2026},
  url = {https://github.com/aLBInOS1/GovAI},
  note = {LoRA-adapted Qwen3.5-4B for budget analysis and strategic planning}
}
```

---

## 📁 Repository Structure

```
GovAI/
├── kaggle_lora_train (2GPU unsloth).ipynb   # Kaggle notebook for LoRA training
├── kaggle_inference_stream.ipynb            # Kaggle notebook for inference with LoRA + tools
├── kaggle_inference_advanced.ipynb          # Advanced inference with RAG and tool calling
├── comparison_viewer.html                   # Interactive model comparison viewer
├── comparison_analysis_without_tools.json   # LoRA vs Base comparison results
├── LICENSE                                  # MIT License
├── README_EN.md                             # English version (this file)
└── README.md                                # Russian version
```

---

## 🤝 Contributing

Project is open for contributions:
- Dataset expansion with real procurement data
- Agent tools improvement
- RAG module implementation

---

<p align="center">
  Made with ❤️ for advancing AI in the government sector
</p>
