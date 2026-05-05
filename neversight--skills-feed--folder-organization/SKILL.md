---
name: folder-organization
description: Best practices for organizing project folders, file naming conventions, and directory structure standards for research and development projects Use when this capability is needed.
metadata:
  author: neversight
---

# Folder Organization Best Practices

Expert guidance for organizing project directories, establishing file naming conventions, and maintaining clean, navigable project structures for research and development work.

## When to Use This Skill

- Setting up new projects
- Reorganizing existing projects
- Establishing team conventions
- Creating reproducible research structures
- Managing data-intensive projects

## Core Principles

1. **Predictability** - Standard locations for common file types
2. **Scalability** - Structure grows gracefully with project
3. **Discoverability** - Easy for others (and future you) to navigate
4. **Separation of Concerns** - Code, data, documentation, outputs separated
5. **Version Control Friendly** - Large/generated files excluded appropriately

## Standard Project Structure

### Research/Analysis Projects

```
project-name/
├── README.md                 # Project overview and getting started
├── .gitignore               # Exclude data, outputs, env files
├── environment.yml          # Conda environment (or requirements.txt)
├── data/                    # Input data (often gitignored)
│   ├── raw/                # Original, immutable data
│   ├── processed/          # Cleaned, transformed data
│   └── external/           # Third-party data
├── notebooks/               # Jupyter notebooks for exploration
│   ├── 01-exploration.ipynb
│   ├── 02-analysis.ipynb
│   └── figures/            # Notebook-generated figures
├── src/                     # Source code (reusable modules)
│   ├── __init__.py
│   ├── data_processing.py
│   ├── analysis.py
│   └── visualization.py
├── scripts/                 # Standalone scripts and workflows
│   ├── download_data.sh
│   └── run_pipeline.py
├── tests/                   # Unit tests
│   └── test_analysis.py
├── docs/                    # Documentation
│   ├── methods.md
│   └── references.md
├── results/                 # Analysis outputs (gitignored)
│   ├── figures/
│   ├── tables/
│   └── models/
└── config/                  # Configuration files
    └── analysis_config.yaml
```

### Development Projects

```
project-name/
├── README.md
├── .gitignore
├── setup.py                 # Package configuration
├── requirements.txt         # or pyproject.toml
├── src/
│   └── package_name/
│       ├── __init__.py
│       ├── core.py
│       └── utils.py
├── tests/
│   ├── test_core.py
│   └── test_utils.py
├── docs/
│   ├── api.md
│   └── usage.md
├── examples/                # Example usage
│   └── example_workflow.py
└── .github/                 # CI/CD workflows
    └── workflows/
        └── tests.yml
```

### Bioinformatics/Workflow Projects

```
project-name/
├── README.md
├── data/
│   ├── raw/                # Raw sequencing data
│   ├── reference/          # Reference genomes, annotations
│   └── processed/          # Workflow outputs
├── workflows/               # Galaxy .ga or Snakemake files
│   ├── preprocessing.ga
│   └── assembly.ga
├── config/
│   ├── workflow_params.yaml
│   └── sample_sheet.tsv
├── scripts/                # Helper scripts
│   ├── submit_workflow.py
│   └── quality_check.py
├── results/                # Final outputs
│   ├── figures/
│   ├── tables/
│   └── reports/
└── logs/                   # Workflow execution logs
```

## File Naming Conventions

### General Rules

1. **Use lowercase** with hyphens or underscores
   - ✅ `data-analysis.py` or `data_analysis.py`
   - ❌ `DataAnalysis.py` or `data analysis.py`

2. **Be descriptive but concise**
   - ✅ `process-telomere-data.py`
   - ❌ `script.py` or `process_all_the_telomere_sequencing_data_from_experiments.py`

3. **Use consistent separators**
   - Choose either hyphens or underscores and stick with it
   - Convention: hyphens for file names, underscores for Python modules

4. **Include version/date for important outputs**
   - ✅ `report-2026-01-23.pdf` or `model-v2.pkl`
   - ❌ `report-final-final-v3.pdf`

### Numbered Sequences

For sequential files (notebooks, scripts), use zero-padded numbers:

```
notebooks/
├── 01-data-exploration.ipynb
├── 02-quality-control.ipynb
├── 03-statistical-analysis.ipynb
└── 04-visualization.ipynb
```

### Data Files

Include metadata in filename when possible:

```
data/raw/
├── sample-A_hifi_reads_2026-01-15.fastq.gz
├── sample-B_hifi_reads_2026-01-15.fastq.gz
└── reference_genome_v3.fasta
```

## Directory Management Best Practices

### What to Version Control

**DO commit:**
- Source code
- Documentation
- Configuration files
- Small test datasets (<1MB)
- Requirements/environment files
- README files

**DON'T commit:**
- Large data files (use `.gitignore`)
- Generated outputs
- Environment directories (`venv/`, `conda-env/`)
- Logs
- Temporary files
- API keys/secrets

### .gitignore Template

```gitignore
# Python
__pycache__/
*.py[cod]
*$py.class
.venv/
venv/
*.egg-info/

# Jupyter
.ipynb_checkpoints/
*.ipynb_checkpoints

# Data
data/raw/
data/processed/
*.fastq.gz
*.bam
*.vcf.gz

# Outputs
results/
outputs/
*.png
*.pdf
*.html

# Logs
logs/
*.log

# Environment
.env
environment.local.yml

# OS
.DS_Store
Thumbs.db
```

## Data Organization

### Raw Data is Sacred

- **Never modify raw data** - Always keep originals untouched
- Store in `data/raw/` and make it read-only if possible
- Document data provenance (where it came from, when downloaded)

### Processed Data Hierarchy

```
data/
├── raw/                    # Original, immutable
├── interim/                # Intermediate processing steps
├── processed/              # Final, analysis-ready data
└── external/               # Third-party data
```

## Documentation Standards

### README.md Essentials

Every project should have a README with:

```markdown
# Project Name

Brief description

## Installation

How to set up the environment

## Usage

How to run the analysis/code

## Project Structure

Brief overview of directories

## Data

Where data lives and how to access it

## Results

Where to find outputs
```

### Code Documentation

- **Docstrings** for all functions/classes
- **Comments** for complex logic
- **CHANGELOG.md** for tracking changes
- **TODO.md** for tracking work (gitignored or removed before merge)

## Common Anti-Patterns to Avoid

❌ **Flat structure with everything in root**
```
project/
├── script1.py
├── script2.py
├── data.csv
├── output1.png
├── output2.png
└── final_really_final_v3.xlsx
```

❌ **Ambiguous naming**
```
notebooks/
├── notebook1.ipynb
├── test.ipynb
├── analysis.ipynb
└── analysis_new.ipynb
```

❌ **Mixed concerns**
```
project/
├── src/
│   ├── analysis.py
│   ├── data.csv          # Data in source code directory
│   └── figure1.png       # Output in source code directory
```

## Cleanup and Maintenance

### Regular Maintenance Tasks

1. **Archive old branches** - Delete merged feature branches
2. **Clean temp files** - Remove `TODO.md`, `NOTES.md` from completed work
3. **Update documentation** - Keep README current with changes
4. **Review .gitignore** - Ensure large files aren't tracked
5. **Organize notebooks** - Rename/renumber as project evolves

### End-of-Project Checklist

- [ ] README complete and accurate
- [ ] Code documented
- [ ] Tests passing
- [ ] Large files gitignored
- [ ] Working files removed (TODO.md, scratch notebooks)
- [ ] Final outputs in `results/`
- [ ] Environment files current
- [ ] License added (if applicable)

## Integration with Other Skills

This skill works well with:
- **python-environment** - Environment setup and management
- **claude-collaboration** - Team workflow best practices
- **jupyter-notebook-analysis** - Notebook organization standards

## Templates and Tools

### Quick Project Setup

```bash
# Create standard research project structure
mkdir -p data/{raw,processed,external} notebooks scripts src tests docs results config
touch README.md .gitignore environment.yml
```

### Cookiecutter Templates

Consider using cookiecutter for standardized project templates:
- `cookiecutter-data-science` - Data science projects
- `cookiecutter-research` - Research projects
- Custom team templates

## References and Resources

- [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science/)
- [A Quick Guide to Organizing Computational Biology Projects](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1000424)
- [Good Enough Practices in Scientific Computing](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005510)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
