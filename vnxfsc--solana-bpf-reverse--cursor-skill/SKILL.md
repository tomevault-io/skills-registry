---
name: solana-bpf-reverse
description: Reverse engineer any Solana BPF program (.so file) to understand its logic and restore readable source code. Use when analyzing compiled Solana programs, decompiling .so files, understanding unknown on-chain programs, extracting program logic, or when the user mentions BPF bytecode, reverse engineering, decompilation, or program analysis. Use when this capability is needed.
metadata:
  author: vnxfsc
---

# Solana BPF Reverse Engineering

Complete reverse engineering toolkit for Solana BPF programs.

## Features (60+)

### Core Analysis
- 10-Level Analysis
- Program Type Detection (13+ types)
- String/Constant Extraction
- Function Identification
- Call Graph Building

### Security Analysis
- 8 Vulnerability Checks
- Taint Analysis
- Flash Loan Detection
- Vulnerability Pattern Matching
- Permission Matrix
- Vulnerability PoC Generation

### Advanced Analysis
- Control Flow Graph (CFG)
- Loop Detection
- Cross References
- CPI Target Analysis
- Dead Code Detection
- Code Clone Detection
- Stack Frame Analysis
- Complexity Metrics
- Type Inference
- Symbol Recovery

### Smart Detection
- Compiler Fingerprinting
- Optimization Level Detection
- Obfuscation Detection
- Open Source Library Matching
- Program Similarity Analysis

### Code Recovery
- Decompiled Rust Project
- Complete Anchor Project Generation
- Account Structure Recovery
- Error Type Recovery
- Function Signatures
- Trait Inference
- Generic Type Recovery
- Macro Detection

### Advanced Analysis
- Symbolic Execution Engine
- Multi-Version Comparison
- Parallel Analysis
- Incremental Analysis
- Transaction Simulation

### Visualization
- Memory Layout SVG
- Complexity Heatmap SVG
- Call Graph (DOT)

### Output Formats
- Markdown Report (33 sections)
- Interactive HTML Report
- JSON Analysis Data
- Ghidra Import Script
- IDA Pro Script
- Radare2 Script

### Automation
- REST API Server
- Watch Mode (Monitor Chain)
- Batch Analysis
- CI/CD GitHub Actions
- Fuzz Test Generation

---

## Quick Start

```bash
# Full analysis
python analyze.py program.so -o ./output

# Interactive REPL
python analyze.py repl program.so

# Watch on-chain program
python analyze.py watch <program_id> -o ./watch

# REST API server
python analyze.py serve 8080

# Fetch from chain
python analyze.py fetch <program_id> -o program.so

# Compare versions
python analyze.py diff v1.so v2.so -o ./diff

# Batch analyze
python analyze.py batch *.so -o ./batch
```

---

## Commands

| Command | Description |
|---------|-------------|
| `analyze.py <file>` | Full analysis |
| `analyze.py repl <file>` | Interactive mode |
| `analyze.py serve [port]` | REST API server |
| `analyze.py watch <id>` | Monitor chain |
| `analyze.py fetch <id>` | Fetch from chain |
| `analyze.py diff <a> <b>` | Compare 2 programs |
| `analyze.py compare <files>` | Compare 3+ versions |
| `analyze.py parallel <files>` | Parallel analysis |
| `analyze.py anchor <file>` | Generate Anchor project |
| `analyze.py symbolic <file> <addr>` | Symbolic execution |
| `analyze.py idl <so> <json>` | Compare with IDL |
| `analyze.py batch <files>` | Batch analyze |

---

## Output Files

```
output/
├── report.md              # 37 section report
├── report.html            # Interactive HTML
├── analysis.json          # Complete JSON
├── memory_layout.svg      # Account structures
├── complexity_heatmap.svg # Code complexity
├── callgraph.dot          # GraphViz
├── ghidra_import.py       # Ghidra script
├── ida_import.py          # IDA Pro script
├── radare2_import.r2      # r2 script
├── symbols.csv            # Symbol table
├── vulnerability_tests.rs # Security tests
├── fuzz/                  # Fuzz tests
│   ├── Cargo.toml
│   ├── fuzz_harness.rs
│   └── fuzz_vectors.rs
├── decompiled/            # Rust skeleton
│   └── ...
├── anchor_project/        # Complete Anchor
│   ├── Anchor.toml
│   ├── Cargo.toml
│   ├── package.json
│   ├── programs/recovered/src/lib.rs
│   └── tests/recovered.ts
└── .github/workflows/     # CI/CD
    └── bpf-analysis.yml
```

---

## Report Sections (37)

1. Basic Information
2. String Analysis
3. Function Analysis
4. Syscall Usage
5. Constant Analysis
6. Security Analysis
7. Symbol Recovery
8. Function Signatures
9. Data Flow Analysis
10. Control Flow Analysis
11. Loop Detection
12. Cross References
13. CPI Targets
14. Account Permissions
15. Compute Unit Estimation
16. Taint Analysis
17. Type Inference
18. Dead Code Analysis
19. Instruction Format
20. Library Matching
21. Call Chain Analysis
22. Code Clone Detection
23. Stack Frame Analysis
24. Permission Matrix
25. Compiler Information
26. Optimization Level
27. Obfuscation Detection
28. Program Similarity
29. Flash Loan Detection
30. Vulnerability Patterns
31. Complexity Metrics
32. Gas Optimization Suggestions
33. Recovered Error Types
34. Inferred Rust Traits
35. Generic Type Recovery
36. Detected Macros
37. Symbolic Execution Sample

---

## REST API

```bash
python analyze.py serve 8080
```

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/health` | Health check |
| GET | `/api/v1/help` | API help |
| GET | `/api/v1/fetch/<id>` | Fetch & analyze |
| POST | `/api/v1/analyze` | Analyze (base64 body) |

### Example

```bash
# Fetch and analyze
curl http://localhost:8080/api/v1/fetch/whirLbMiicVdio4qvUfM5KAg6Ct8VwpYzGff3uctyCc

# Analyze local file
cat program.so | base64 | curl -X POST -d @- http://localhost:8080/api/v1/analyze
```

---

## Anchor Project Generation

Generate a complete compilable Anchor project:

```bash
python analyze.py anchor program.so -o ./my_project
```

Output:
```
my_project/
├── Anchor.toml
├── Cargo.toml
├── package.json
├── programs/recovered/
│   ├── Cargo.toml
│   └── src/lib.rs
└── tests/recovered.ts
```

Build & test:
```bash
cd my_project
anchor build
anchor test
```

---

## Symbolic Execution

Explore all execution paths:

```bash
python analyze.py symbolic program.so 0x120
```

Output:
```
Paths explored: 4
Max path length: 41

Path 1 (length 23):
  - jeq true @ 0x000148
  - jeq true @ 0x000248
```

---

## Multi-Version Comparison

Compare 3+ program versions:

```bash
python analyze.py compare v1.so v2.so v3.so -o ./comparison
```

Output:
```
| Version | Hash | Functions | Changes |
|---------|------|-----------|---------|
| v1 | a1b2c3 | 450 | - |
| v2 | d4e5f6 | 465 | +15 |
| v3 | g7h8i9 | 480 | +15 |
```

---

## Parallel Analysis

Analyze multiple programs with multi-threading:

```bash
python analyze.py parallel *.so -o ./results -w 8
```

---

## Watch Mode

```bash
python analyze.py watch <program_id> -o ./watch --interval 60
```

- Auto-detects changes via hash
- Saves each version separately
- Auto-generates diff reports
- Real-time logging

---

## Security Checks

| Check | Severity |
|-------|----------|
| Missing Signer | HIGH |
| Missing Owner | MEDIUM |
| Integer Overflow | HIGH |
| Reentrancy via CPI | MEDIUM |
| PDA Validation | MEDIUM |
| Flash Loan Risk | HIGH |
| Account Confusion | HIGH |

---

## Program Types

Token, NFT, DEX (AMM/CLMM/DLMM), DEX Aggregator, Lending, Staking, Governance, Oracle, Bridge, Perpetual, Orderbook, cNFT, Name Service

---

## Library Matching

SPL Token, Token-2022, Anchor, Metaplex, Orca Whirlpool, Raydium AMM/CLMM, Meteora DLMM, Marinade, Jupiter

---

## Python API

```python
from analyze import BPFAnalyzer

with open("program.so", "rb") as f:
    data = f.read()

analyzer = BPFAnalyzer(data)
result = analyzer.analyze(level=10)

print(f"Type: {result['program_type']}")
print(f"Functions: {result['function_count']}")
print(f"Security: {result['security']['risk_level']}")
print(f"Flash Loan Risk: {result['flash_loan']['risk_level']}")
print(f"Optimization: {result['optimization']['estimated_level']}")
```

---

## Example Output

```
Analyzing program.so (505,920 bytes)...

Summary:
  - Functions: 495
  - Strings: 110
  - Constants: 2350
  - Program Type: dex (CLMM)
  - Security Risk: LOW
  - Flash Loan Risk: HIGH
  - Optimization Level: O2
  - Dead Code: 81.88%

Output files:
  - report.md (33 sections)
  - report.html
  - memory_layout.svg
  - complexity_heatmap.svg
  - ida_import.py
  - radare2_import.r2
  - fuzz/
  - .github/workflows/
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vnxfsc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
