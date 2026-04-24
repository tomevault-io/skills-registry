---
name: cellphonedb
description: Comprehensive skill for CellPhoneDB - Database of cell type markers and cell-cell communication analysis for single-cell data. Use for cell type annotation, ligand-receptor analysis, cell-cell interaction inference, and communication network visualization. Use when this capability is needed.
metadata:
  author: ketomihine
---

# Cellphonedb Skill

Comprehensive assistance with CellPhoneDB development, generated from official documentation.

## When to Use This Skill

This skill should be triggered when you need to:

**Data Preparation & Analysis:**
- Prepare meta and counts data files for CellPhoneDB analysis
- Validate and preprocess single-cell RNA-seq data for interaction analysis
- Subsample counts data for computational efficiency
- Set up proper cell type annotations and metadata formatting

**Cell-Cell Communication Analysis:**
- Identify significant ligand-receptor interactions between cell types
- Perform statistical analysis of cell-type specific communication
- Analyze spatial microenvironments and neighborhood interactions
- Query and filter interaction results based on expression thresholds

**Advanced Applications:**
- Integrate transcription factor activity with receptor signaling (CellSign module)
- Perform differential expression analysis for interaction-specific genes
- Visualize communication networks and interaction scores
- Analyze complex multi-subunit interactions and heteromeric complexes

**Database Management:**
- Work with CellPhoneDB database files and versions
- Extract protein and complex data for web applications
- Handle gene synonym mappings and database updates
- Manage custom CellPhoneDB database creation

## Quick Reference

### Data Preparation and Validation
```python
import pandas as pd
import numpy as np
from cellphonedb.src.core.exceptions.ParseCountsException import ParseCountsException

# Validate meta DataFrame - ensure correct columns and indexes
def validate_meta(meta_raw):
    """Re-formats meta_raw if need be to ensure correct columns and indexes are present"""
    meta = meta_raw.copy()
    # Ensure proper indexing and column structure
    return meta

# Validate counts DataFrame - ensure float32 type and cell consistency
def validate_counts(counts, meta):
    """Ensure that counts values are of type float32, and that all cells in meta exist in counts"""
    if not len(counts.columns):
        raise ParseCountsException('Counts values are not decimal values', 'Incorrect file format')

    try:
        if np.any(counts.dtypes.values != np.dtype('float32')):
            counts = counts.astype(np.float32)
    except Exception:
        raise ParseCountsException

    meta.index = meta.index.astype(str)

    if np.any(~meta.index.isin(counts.columns)):
        raise ParseCountsException("Some cells in meta did not exist in counts",
                                   "Maybe incorrect file format")

    if np.any(~counts.columns.isin(meta.index)):
        counts = counts.loc[:, counts.columns.isin(meta.index)]

    return counts
```

### Database Operations and Data Extraction
```python
from typing import Tuple
import pandas as pd
import zipfile
import io

# Extract interaction data from CellPhoneDB database
def get_interactions_genes_complex(cpdb_file_path) -> Tuple[pd.DataFrame, pd.DataFrame, pd.DataFrame, pd.DataFrame, dict, dict]:
    """Returns a tuple of four DataFrames containing data from CellPhoneDB database"""

    # Extract csv files from database zip file
    dbTableDFs = extract_dataframes_from_db(cpdb_file_path)

    # Process gene synonym mappings
    gene_synonym2gene_name = {}
    if 'gene_synonym_to_gene_name' in dbTableDFs:
        gs2gn = dbTableDFs['gene_synonym_to_gene_name']
        gene_synonym2gene_name = dict(zip(gs2gn['Gene Synonym'], gs2gn['Gene Name']))

    # Process multidata table and convert boolean columns
    mtTable = dbTableDFs['multidata_table']
    MULTIDATA_TABLE_BOOLEAN_COLS = ['receptor', 'other', 'secreted_highlight',
                                   'transmembrane', 'secreted', 'peripheral', 'integrin', 'is_complex']

    for col in MULTIDATA_TABLE_BOOLEAN_COLS:
        mtTable[col] = mtTable[col].astype(bool)

    # Build genes table by merging gene, protein, and multidata tables
    genes = pd.merge(dbTableDFs['gene_table'], dbTableDFs['protein_table'],
                    left_on='protein_id', right_on='id_protein')
    genes = pd.merge(genes, mtTable, left_on='protein_multidata_id', right_on='id_multidata')

    # Build interactions table with proper suffixes
    multidata_expanded = pd.concat([
        pd.merge(dbTableDFs['protein_table'], mtTable, left_on='protein_multidata_id', right_on='id_multidata'),
        pd.merge(mtTable, dbTableDFs['complex_table'], left_on='id_multidata', right_on='complex_multidata_id')
    ], ignore_index=True, sort=True)

    interactions = pd.merge(dbTableDFs['interaction_table'], multidata_expanded, how='left',
                           left_on=['multidata_1_id'], right_on=['id_multidata'])
    interactions = pd.merge(interactions, multidata_expanded, how='left',
                           left_on=['multidata_2_id'], right_on=['id_multidata'], suffixes=('_1', '_2'))

    # Set indices for final dataframes
    interactions.set_index('id_interaction', drop=True, inplace=True)

    return interactions, genes, complex_composition, complex_expanded, gene_synonym2gene_name, receptor2tfs
```

### Installation and Setup
```bash
# Install Python and Jupyter Notebook
# Follow instructions at https://docs.conda.io/en/latest/miniconda.html
conda create -n cpdb python=3.8
conda activate cpdb
pip install notebook

# Clone CellPhoneDB repository
cd <your_working_directory>
git clone git@github.com:ventolab/CellphoneDB.git
cd CellphoneDB/cellphonedb/notebooks

# Start Jupyter notebook
jupyter notebook
# Navigate to http://localhost:8888/notebooks/notebooks/cellphonedb.ipynb
```

### Analysis Methods Selection
```python
# METHOD 1: Simple analysis - interaction means
# Use for quick exploration without statistical testing
cellphonedb method statistical_analysis meta.txt counts.txt --output-path results/

# METHOD 2: Statistical analysis - significance testing
# Use for identifying significant cell-type specific interactions
cellphonedb method statistical_analysis meta.txt counts.txt --output-path results/ --subsampling --threads 4

# METHOD 3: Differential expression analysis
# Use for custom comparisons with provided DEGs file
cellphonedb method degs_analysis meta.txt counts.txt degs.txt --output-path results/

# METHOD 4: Spatial microenvironments analysis
# Add spatial context to interaction analysis
cellphonedb method statistical_analysis meta.txt counts.txt --output-path results/ --microenvironments microenv.txt
```

### Data Format Requirements
```python
# Meta file format (tab-separated):
# cell_name    cell_type
# cell1        T_cell
# cell2        B_cell
# cell3        T_cell

# Counts file format (tab-separated, genes as rows, cells as columns):
# Gene    cell1    cell2    cell3
# EGFR    5.2      0.0      3.1
# CD3D    8.7      1.2      9.4

# DEGs file format for METHOD 3 (tab-separated):
# gene        cluster    pval    avg_log2FC
# IL2RA       T_cell     0.001   2.3
# MS4A1       B_cell     0.0005  3.1
```

### Microenvironments and Spatial Analysis
```python
# Microenvironments file format (tab-separated):
# cell_type    microenvironment
# T_cell       immune_compartment
# B_cell       immune_compartment
# epithelial   tissue_compartment

# Run analysis with spatial constraints
cellphonedb method statistical_analysis meta.txt counts.txt \
    --output-path results/ \
    --microenvironments microenv.txt \
    --threshold 0.1  # Minimum expression fraction
```

### CellSign Module Integration
```python
# Prepare transcription factor activity file
# Format: cell_type    TF1    TF2    TF3
#          T_cell       1.2    0.8    0.5
#          B_cell       0.3    1.1    0.9

# Run analysis with TF activity integration
cellphonedb method statistical_analysis meta.txt counts.txt \
    --output-path results/ \
    --active-tfs tf_activity.txt \
    --threshold 0.1
```

### Database Path Management
```python
import os

def get_db_path(user_dir_root, db_version):
    """Retrieves the path to the local database file corresponding to db_version"""
    return os.path.join(user_dir_root, "releases", db_version)

# Example usage:
user_dir = "/path/to/cellphonedb/data"
db_version = "v5.0"
db_path = get_db_path(user_dir, db_version)
# Returns: "/path/to/cellphonedb/data/releases/v5.0"
```

## Key Concepts

### Analysis Methods
- **METHOD 1 (Simple Analysis)**: Calculates mean interaction expression without statistical testing. Fast exploration tool.
- **METHOD 2 (Statistical Analysis)**: Permutation-based statistical testing for cell-type specific interactions using empirical shuffling.
- **METHOD 3 (DEGs Analysis)**: Custom differential expression-based approach using user-provided marker genes or DEGs.

### Statistical Testing Framework
- **Permutation approach**: Randomly shuffles cluster labels 1000+ times to create null distribution
- **P-value calculation**: Proportion of permuted means ≥ actual mean
- **Multiple testing correction**: Built-in methods for controlling false discovery rate
- **Expression thresholds**: Default 10% of cells (configurable) must express interacting partners

### Database Structure
- **Multidata table**: Central table containing proteins, complexes, and their properties
- **Interactions table**: Curated ligand-receptor pairs with directionality and classification
- **Complex composition**: Multi-subunit protein complexes and their components
- **Gene synonym mapping**: Alternate gene names for comprehensive coverage

### CellSign Integration
- **Receptor-TF relationships**: 211 curated high-specificity receptor-transcription factor pairs
- **Activity status**: Uses TF activity as downstream sensor for receptor activation
- **Enhanced confidence**: Adds extra evidence layer for cell-cell interaction predictions

## Reference Files

This skill includes comprehensive documentation in `references/`:

### **api_reference.md** - Technical Implementation
Essential for developers and advanced users:
- **Data preprocessing functions**: Complete implementations for meta and counts validation
- **Database utilities**: Source code for data extraction and processing
- **Counts preprocessing**: Float32 conversion, cell consistency checking, error handling
- **Protein and complex data extraction**: Functions for web application integration

### **user_guide.md** - Complete Analysis Workflow
Comprehensive guide for all analysis methods:
- **Installation instructions**: Python environment setup, Jupyter configuration
- **Three analysis methods**: Detailed explanations, use cases, and interpretation
- **Statistical framework**: Permutation testing, p-value calculation, significance thresholds
- **Advanced features**: Spatial microenvironments, CellSign integration, scoring methodology
- **Output interpretation**: Understanding means, pvalues, significant_means, and deconvoluted files

### **other.md** - Getting Started Resources
Quick start and setup information:
- **Installation procedures**: Conda/miniconda setup, Jupyter notebook configuration
- **Quick start workflow**: From data upload to analysis completion
- **Example notebooks**: Step-by-step guided analysis with sample datasets

Use `view` to read specific reference files when detailed information is needed.

## Working with This Skill

### For Beginners
1. **Start with installation**: Follow the user_guide.md setup instructions for Python and Jupyter
2. **Prepare your data**: Use the interactive notebook format at http://localhost:8888/notebooks/cellphonedb.ipynb
3. **Try METHOD 1 first**: Simple analysis without statistical testing to understand data structure
4. **Review output formats**: Understand means.csv and deconvoluted.csv structure

### For Intermediate Users
1. **Master statistical analysis**: Use METHOD 2 for rigorous significance testing of interactions
2. **Optimize thresholds**: Adjust expression thresholds based on your dataset characteristics
3. **Implement subsampling**: Use geometric sketching for large datasets (>100k cells)
4. **Add spatial context**: Incorporate microenvironment information for tissue-specific interactions

### For Advanced Users
1. **Custom DEG analysis**: Use METHOD 3 for complex experimental designs and hierarchical comparisons
2. **CellSign integration**: Incorporate transcription factor activity for enhanced confidence
3. **Database customization**: Create custom CellPhoneDB databases with organism-specific interactions
4. **Batch processing**: Implement automated pipelines for multiple datasets or conditions

### Navigation Tips
- **Data format first**: Always ensure meta.txt and counts.txt follow exact format requirements
- **Method selection flow**: METHOD 1 (exploration) → METHOD 2 (standard analysis) → METHOD 3 (custom comparisons)
- **Threshold tuning**: Adjust expression thresholds (default 0.1) based on sequencing depth and biological context
- **Result validation**: Cross-reference significant interactions with known biology and literature

## Resources

### references/
Organized documentation extracted from official sources:
- **Complete API documentation** with function implementations and error handling
- **Step-by-step analysis workflows** for all three methods
- **Statistical framework explanations** with permutation testing details
- **Advanced integration guides** for spatial and transcription factor analysis
- **Real code examples** from the official CellPhoneDB codebase

### scripts/
Add your automation scripts here:
- Data preprocessing pipelines for multiple datasets
- Batch analysis workflows for systematic studies
- Result visualization and network analysis tools
- Custom statistical testing frameworks

### assets/
Store templates and reference materials:
- Input file templates (meta.txt, counts.txt, DEGs formats)
- Output interpretation guides and examples
- Network visualization templates and scripts
- Analysis workflow checklists

## Notes

### Data Requirements
- **Counts data**: Raw counts (not normalized) required for statistical methods
- **Meta information**: Cell barcodes and corresponding cell type annotations
- **Expression threshold**: Default 10% of cells must express gene to consider interaction
- **Cell type consistency**: Minimum cell numbers per type recommended for statistical power

### Performance Considerations
- **Large datasets**: Use subsampling for datasets >100k cells to improve runtime
- **Memory usage**: Consider sparse matrix representations for large count matrices
- **Parallel processing**: Use --threads parameter for multi-core acceleration
- **Database caching**: Local database storage speeds up repeated analyses

### Common Pitfalls
- **Normalized data**: Using normalized counts with statistical methods (requires raw counts)
- **Format mismatch**: Incorrect tab-separated format or header inconsistencies
- **Low-expressed genes**: Setting expression thresholds too low leading to spurious interactions
- **Cell type naming**: Inconsistent cell type labels between meta and analysis files

## Updating

To refresh this skill with updated documentation:
1. Check the official CellPhoneDB documentation at https://cellphonedb.readthedocs.io/en/latest/
2. Re-run the scraper with updated source URLs if available
3. The skill will preserve existing structure while incorporating new methods and features
4. Database updates and new interaction curation will be automatically integrated

For the most current information, always cross-reference with the official CellPhoneDB documentation and GitHub repository.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ketomihine) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
