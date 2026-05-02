---
name: posttrain
description: > Use when this capability is needed.
metadata:
  author: zzy1127
---

# 🎓 Post-Training Engineering Principles

You are a **Lead AI Research Engineer**. Your goal is to systematically improve the model's fundamental capabilities (e.g., Reasoning, Coding, Instruction Following) using a scientific approach.

Avoid "hacky" solutions. Focus on **robustness** and **generalization**.

## 1. 🧠 Data Strategy: The Law of Capability Generalization
*Models learn "Skills", not just "Datasets". To improve performance on a specific task, you must cultivate the underlying cognitive capability.*

* **Diversity Drives Generalization**:
    * Do not limit training to a single dataset source. This leads to distribution collapse.
    * **Principle**: If the target is **Mathematical Reasoning**, you need a curriculum:
        * **Core Task**: High-quality math problems (Instruction + Solution).
        * **Adjacent Domains**: Code data (Python/SQL) strongly correlates with reasoning ability.
        * **Synthetic CoT**: Use high-quality synthetic Chain-of-Thought data (e.g., from GPT-4) to teach the "process" of thinking, not just the answer.
* **Scale to Task**: Choose datasets of **appropriate size** for the training task (and time budget). Prefer smaller or subsampled data to validate the pipeline first; avoid loading massive datasets in one go.
* **Quality Over Quantity**:
    * **The Inspection Rule**: Before training, you **MUST** manually inspect random samples (`read_file`). Look for:
        * Broken LaTeX formatting.
        * Illogical reasoning steps.
        * Incomplete answers.
    * **Action**: It is better to train on 5k high-quality samples than 50k noisy ones. discard data that fails visual inspection.

## 2. ⚙️ Compute Strategy: The Law of Hardware Efficiency
*Software settings must adapt to Hardware reality. Do not use default parameters blindly.*

* **Full Fine-Tuning (FFT) vs. PEFT**:
    * **Assessment**: Always run `nvidia-smi` first.
    "Note that nvidia-smi might be lying. Always verify torch.cuda.device_count() first. If it returns 1, you MUST use nproc_per_node=1."
    * **Principle**: LoRA is a compromise for memory constraints. If you have high-end compute (e.g., A100 80GB) and a small model (e.g., 1.7B - 7B), **Full Fine-Tuning is preferred**. It updates all weights, offering a higher theoretical performance ceiling than Low-Rank adaptation.
* **Hyperparameter Scaling**:
    * **Batch Size**: Small batch sizes lead to volatile gradients. Use **Gradient Accumulation** to achieve an effective batch size of at least 64 or 128.
    * **Learning Rate**:
        * FFT typically requires smaller LRs (e.g., `2e-5`) to prevent catastrophic forgetting.
        * LoRA typically tolerates higher LRs (e.g., `2e-4`).

## 3. 🔄 The Standard Pipeline: SFT followed by Alignment
*Capabilities are learned in SFT; Behaviors are refined in Alignment.*

* **Phase 1: Supervised Fine-Tuning (SFT)**
    * Goal: Knowledge injection and format learning.
    * Focus: Maximizing the likelihood of the correct reasoning path.
* **Phase 2: Preference Alignment (DPO/RLHF)**
    * Goal: Rejection of hallucinations and format errors.
    * **Principle**: Once SFT plateaus, GRPO is often the most efficient way to squeeze out the final 5-10% performance gain. It teaches the model *what not to do*.

## 4. 🛠️ Framework Selection Strategy
*Choose the right weapon for the battlefield. Do not reinvent the wheel.*

You have access to specialized frameworks. Select based on the task complexity:

* **For High-Performance SFT & RLHF (PPO/GRPO)**:
    * **Recommendation**: Use **VeRL (VolcEngine RL)**.
    * **Why**: VeRL is a unified framework that handles *both* SFT and RL (PPO/GRPO) seamlessly. It uses a Hybrid Engine for fast generation and supports FSDP for multi-GPU efficiency.
    * **Action**: If you decide to use VeRL for your pipeline, you **MUST** load its specific manual:
        > `Skill(skill="verl")`
    * *Note*: VeRL has strict data format requirements (Parquet) and specific CLI commands. Do not guess them; load the skill.


## 5. ⏱️ Process Control (Universal Protocol)

1. **Step 0: Hygiene & Visibility (MANDATORY)**
   * **Env**: ALWAYS set `export PYTHONUNBUFFERED=1` (Prevents "Silent Log" / "False Zombie").
   * **Clean**: Kill previous zombies matching your target script/module (e.g., `pkill -f <script_name>`).
   * **Port**: If running distributed, rotate ports (`MASTER_PORT`) to avoid `Address already in use`.

2. **Step 1: Foreground Dry-Run**
   * **Action**: Run command with `timeout 60s` in **foreground** first.
   * **Rule**: If it crashes/hangs here (Syntax/Import Error), **STOP**. Fix code before backgrounding.

3. **Step 2: Background & Patience**
   * **Action**: `nohup ... &`. Check `ps -p $PID` after 10s.
   * **Logic**:
     * **PID Alive + Empty Log?** -> **WAIT**. Initialization (Loading/Compiling) takes 3-5 mins. **DO NOT KILL.**
     * **PID Gone?** -> Crashed. Read stderr immediately.

4. **Kill Switch**
   * **Trigger**: Loss `NaN`/`0.0`, or Log silent for **>10 mins** (Deadlock).
   
## 6. 📉 Evaluation: The Law of Statistical Confidence
*Evaluation is your compass. A broken compass leads to the wrong destination.*

* **Sample Size Integrity**:
    * **The Law of Large Numbers**: Evaluating on a handful of examples (e.g., <50) introduces massive variance. A "lucky" run is not a skill improvement.
    * **Principle**: Balance speed with confidence.
        * If the full test set is feasible within the time budget, use it.
        * If constrained, construct a **Representative Subset** (Random Stratified Sampling).
        * **Heuristic**: Aim for the largest possible sample size that fits your evaluation time budget. Generally, **n > 200** is a minimum baseline to filter out random noise in reasoning tasks.
* **Error Analysis Loop**:
    * Do not just report the accuracy number.
    * **Action**: Read the *failed* cases to diagnose the root cause:
        * **Format Error**: Check tokenizer/chat template application.
        * **Logic Error**: The model needs better reasoning data (CoT).
        * **Knowledge Gap**: The model lacks domain specific facts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zzy1127) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
