---
name: huggingface-skills
description: Hugging Face Hub operations - models, datasets, Spaces, training with TRL, evaluation, and CLI operations. Use when working with ML models, datasets, or Hugging Face infrastructure. Use when this capability is needed.
metadata:
  author: allanninal
---

# Hugging Face Skills

## When to Use This Skill

- Uploading/downloading models and datasets
- Creating or managing Spaces
- Training models with TRL (SFT, DPO, GRPO)
- Running model evaluations
- Using the Hugging Face CLI
- Converting models to GGUF format
- Tracking ML experiments

## CLI Operations

### Setup

```bash
# Install
pip install huggingface_hub[cli]

# Login
huggingface-cli login

# Check identity
huggingface-cli whoami
```

### Repository Operations

```bash
# Create repo
huggingface-cli repo create my-model --type model
huggingface-cli repo create my-dataset --type dataset
huggingface-cli repo create my-space --type space

# Clone
git clone https://huggingface.co/username/my-model

# Upload
huggingface-cli upload username/my-model ./local_dir
huggingface-cli upload username/my-model ./file.safetensors

# Download
huggingface-cli download username/my-model
huggingface-cli download username/my-model --include "*.safetensors"
```

## Python API

### Upload Models

```python
from huggingface_hub import HfApi, upload_folder

api = HfApi()

# Create repo
api.create_repo("my-model", repo_type="model", private=False)

# Upload entire folder
upload_folder(
    folder_path="./model_output",
    repo_id="username/my-model",
    repo_type="model",
)

# Upload single file
api.upload_file(
    path_or_fileobj="./model.safetensors",
    path_in_repo="model.safetensors",
    repo_id="username/my-model",
)
```

### Download Models

```python
from huggingface_hub import snapshot_download, hf_hub_download

# Download entire repo
snapshot_download(repo_id="meta-llama/Llama-3.2-3B")

# Download specific file
hf_hub_download(
    repo_id="meta-llama/Llama-3.2-3B",
    filename="config.json",
)

# With cache
from transformers import AutoModelForCausalLM
model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.2-3B")
```

## Datasets

### Create Dataset

```python
from datasets import Dataset, DatasetDict

# From dict
data = {
    "text": ["Hello world", "How are you?"],
    "label": [0, 1],
}
dataset = Dataset.from_dict(data)

# From pandas
import pandas as pd
df = pd.read_csv("data.csv")
dataset = Dataset.from_pandas(df)

# Create train/test split
dataset = dataset.train_test_split(test_size=0.1)

# Upload
dataset.push_to_hub("username/my-dataset")
```

### Load Dataset

```python
from datasets import load_dataset

# From Hub
dataset = load_dataset("username/my-dataset")

# Specific split
train = load_dataset("username/my-dataset", split="train")

# Streaming for large datasets
dataset = load_dataset("username/my-dataset", streaming=True)

# With SQL query
dataset = load_dataset("username/my-dataset", split="train[:1000]")
```

## Training with TRL

### Supervised Fine-Tuning (SFT)

```python
from trl import SFTTrainer
from transformers import AutoModelForCausalLM, AutoTokenizer
from datasets import load_dataset

model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.2-3B")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.2-3B")

dataset = load_dataset("your-dataset", split="train")

trainer = SFTTrainer(
    model=model,
    train_dataset=dataset,
    tokenizer=tokenizer,
    max_seq_length=2048,
    dataset_text_field="text",
    args=TrainingArguments(
        output_dir="./sft_output",
        per_device_train_batch_size=4,
        gradient_accumulation_steps=4,
        learning_rate=2e-5,
        num_train_epochs=3,
        logging_steps=10,
        save_steps=100,
        push_to_hub=True,
    ),
)

trainer.train()
```

### DPO Training

```python
from trl import DPOTrainer, DPOConfig

trainer = DPOTrainer(
    model=model,
    ref_model=None,  # Uses implicit reference
    args=DPOConfig(
        output_dir="./dpo_output",
        beta=0.1,
        per_device_train_batch_size=4,
    ),
    train_dataset=dataset,  # Must have "chosen" and "rejected" columns
    tokenizer=tokenizer,
)

trainer.train()
```

### GRPO Training

```python
from trl import GRPOTrainer, GRPOConfig

config = GRPOConfig(
    output_dir="./grpo_output",
    per_device_train_batch_size=8,
    learning_rate=1e-6,
)

trainer = GRPOTrainer(
    model=model,
    config=config,
    train_dataset=dataset,
    reward_model=reward_model,
)

trainer.train()
```

## Model Evaluation

```python
from lighteval.tasks import Task
from lighteval.metrics import Accuracy

# Define evaluation task
task = Task(
    name="my_eval",
    prompt_template="Question: {question}\nAnswer:",
    gold_key="answer",
    metric=Accuracy(),
)

# Run evaluation
from lighteval import evaluate

results = evaluate(
    model="username/my-model",
    tasks=[task],
    output_dir="./eval_results",
)
```

## GGUF Conversion

```bash
# Install llama.cpp
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp && make

# Convert to GGUF
python convert_hf_to_gguf.py /path/to/model --outtype f16

# Quantize
./llama-quantize model.gguf model-q4_k_m.gguf Q4_K_M
```

## Spaces

### Create Space

```bash
# Create gradio space
huggingface-cli repo create my-app --type space --space_sdk gradio

# Or streamlit
huggingface-cli repo create my-app --type space --space_sdk streamlit
```

### Gradio App Example

```python
# app.py
import gradio as gr
from transformers import pipeline

pipe = pipeline("text-generation", model="gpt2")

def generate(prompt):
    return pipe(prompt, max_length=100)[0]["generated_text"]

demo = gr.Interface(fn=generate, inputs="text", outputs="text")
demo.launch()
```

## Experiment Tracking

```python
from huggingface_hub import create_repo, upload_file
import json

# Log metrics
metrics = {"loss": 0.5, "accuracy": 0.92}
with open("metrics.json", "w") as f:
    json.dump(metrics, f)

upload_file(
    path_or_fileobj="metrics.json",
    path_in_repo="runs/exp_001/metrics.json",
    repo_id="username/my-model",
)
```

## Best Practices

- [ ] Use .safetensors format (safer than pickle)
- [ ] Include model card (README.md) with usage examples
- [ ] Tag models with appropriate metadata
- [ ] Use Git LFS for large files
- [ ] Set appropriate licenses
- [ ] Include requirements.txt for Spaces
- [ ] Use streaming for large datasets

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
