---
name: detecting-supply-chain-attacks-in-ci-cd
description: > Use when this capability is needed.
metadata:
  author: killvxk
---

# 检测 CI/CD 中的供应链攻击

## 使用说明

通过解析 GitHub Actions YAML 文件，检查未固定的依赖、脚本注入向量和密钥泄露，扫描 CI/CD 工作流文件中的供应链风险。

```python
import yaml
from pathlib import Path

for wf in Path(".github/workflows").glob("*.yml"):
    with open(wf) as f:
        workflow = yaml.safe_load(f)
    for job_name, job in workflow.get("jobs", {}).items():
        for step in job.get("steps", []):
            uses = step.get("uses", "")
            if uses and "@" in uses and not uses.split("@")[1].startswith("sha"):
                print(f"Unpinned action: {uses} in {wf.name}")
```

关键供应链风险：
1. 未固定到 SHA 的 GitHub Actions（使用 @main 而非提交哈希）
2. 通过 `${{ github.event }}` 表达式的脚本注入
3. GITHUB_TOKEN 权限过于宽松
4. 对仓库有写入权限的第三方 Action
5. 通过公有/私有包名冲突的依赖混淆

## 示例

```python
# 检查 run 步骤中的脚本注入
for step in job.get("steps", []):
    run_cmd = step.get("run", "")
    if "${{" in run_cmd and "github.event" in run_cmd:
        print(f"Script injection risk: {run_cmd[:80]}")
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/killvxk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
