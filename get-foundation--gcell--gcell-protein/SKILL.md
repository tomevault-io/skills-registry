---
name: gcell-protein
description: | Use when this capability is needed.
metadata:
  author: get-foundation
---

# Protein Structure Analysis

## Get Protein Sequences

```python
from gcell.protein.data import (
    get_seq_from_gene_name,
    get_uniprot_from_gene_name
)

# Get protein sequence from gene name
seq = get_seq_from_gene_name('TP53')
seq = get_seq_from_gene_name('EGFR')
seq = get_seq_from_gene_name('BRCA1')

# Get UniProt accession
uniprot_id = get_uniprot_from_gene_name('TP53')
```

## AlphaFold2 Confidence Scores

```python
from gcell.protein.data import get_lddt_from_gene_name

# Get pLDDT (predicted local distance difference test) scores
# Higher scores = higher confidence in structure prediction
plddt = get_lddt_from_gene_name('TP53')
plddt = get_lddt_from_gene_name('EGFR')

# pLDDT interpretation:
# > 90: Very high confidence
# 70-90: Confident
# 50-70: Low confidence
# < 50: Very low confidence (likely disordered)
```

## Full Protein Analysis

```python
from gcell.protein.protein import Protein

# Load protein from gene name
protein = Protein.from_gene_name('EGFR')
protein = Protein.from_gene_name('TP53')

# Access protein data
print(protein.sequence)
print(protein.length)
print(protein.plddt)  # AlphaFold confidence

# 3D structure visualization
protein.plot_structure()  # Interactive 3D view
```

## Protein-Protein Interactions

```python
from gcell.protein.string import get_string_interactions

# Get interactions from STRING database
interactions = get_string_interactions('TP53')

# Filter by confidence score
high_conf = interactions[interactions['score'] > 0.7]
```

## Key Classes and Functions

| Name | Purpose |
|------|---------|
| `Protein` | Full protein analysis class |
| `get_seq_from_gene_name()` | Get amino acid sequence |
| `get_uniprot_from_gene_name()` | Get UniProt ID |
| `get_lddt_from_gene_name()` | Get AlphaFold pLDDT scores |
| `get_string_interactions()` | Get protein interactions |

## Data Sources

- Protein sequences: UniProt
- Structures: AlphaFold Database
- Interactions: STRING Database
- Data cached in: `~/.gcell_data/cache/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get-foundation) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
