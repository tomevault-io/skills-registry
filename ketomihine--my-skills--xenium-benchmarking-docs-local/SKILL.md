---
name: xenium-benchmarking-docs-local
description: Xenium benchmarking 本地文档快照（全量 HTML）- 包含完整模块文档 Use when this capability is needed.
metadata:
  author: ketomihine
---

# Xenium-Benchmarking-Docs-Local Skill

Comprehensive assistance with Xenium spatial transcriptomics benchmarking, generated from official documentation.

## When to Use This Skill

This skill should be triggered when:
- Working with Xenium spatial transcriptomics data analysis
- Processing Xenium machine output files
- Performing spatial domain identification using Banksy, NBD, or RBD
- Analyzing neighborhood enrichment and spatially variable genes
- Benchmarking Xenium data against scRNA-seq references
- Implementing quality control and filtering for spatial transcriptomics
- Calculating cell density and spatial metrics
- Performing coexpression analysis in spatial data
- Using Baysor for alternative segmentation

## Quick Reference

### Common Patterns

#### Basic Data Processing
```python
# Import required modules
import os
import numpy as np
import pandas as pd
import scanpy as sc
from xb.formatting import *
from xb.plotting import *
from xb.preprocessing import *
from xb.Spage_main import *
from xb.calculating import *

# Format Xenium output to AnnData
files=['./data/output-XETG00047__0011146__1886C__20231102__180733',
       './data/output-XETG00047__0011146__1886P__20231102__180733']
output_path=r'../pipeline_output/'
max_nucleus_distance=10
min_quality=0

adata=format_to_adata(files=files,
                      output_path=output_path,
                      max_nucleus_distance=max_nucleus_distance,
                      min_quality=min_quality,
                      save=True)
```

#### Preprocessing and Clustering
```python
# Define clustering parameters
clustering_params={
    'normalization_target_sum':100,
    'min_counts_x_cell':40,
    'min_genes_x_cell':15,
    'scale':False,
    'clustering_alg':'louvain',
    'resolutions':[0.2,0.5,1.1],
    'n_neighbors':15,
    'umap_min_dist':0.1,
    'n_pcs':0
}

# Read and preprocess data
adata=sc.read(output_path+'combined_filtered.h5ad')
adata=preprocess_adata(adata, save=True,
                       clustering_params=clustering_params,
                       output_path=output_path)
```

#### Domain Identification with Banksy
```python
# Banksy parameters for spatial domain identification
banksy_params={
    'resolutions':[.9],
    'pca_dims':[20],
    'lambda_list':[.8],
    'k_geom':15,
    'max_m':1,
    'nbr_weight_decay':"scaled_gaussian",
    'cluster_algorithm':'leiden'
}

# Run Banksy domain identification
adata, adata_banksy = domains_by_banksy(adata,
                                       plot_path=plot_path,
                                       banksy_params=banksy_params)
```

#### Alternative Segmentation with Baysor
```python
# Prepare Xenium data for Baysor
prep_xenium_data_for_baysor(files[0], output_path,
                           CROP=True,
                           COORDS=[1000, 5000, 1000, 5000])

# Run Baysor using Docker (command line)
!docker pull louisk92/txsim_baysor:v0.6.2bin
!sudo docker run -it --rm -v /path/to/baysor/input:/data \
   -v /path/to/xb/module:/module louisk92/txsim_baysor:v0.6.2bin
!cd /path/to/xb && bash run_baysor.sh "/data"

# Format Baysor output to AnnData
path='/path/to/baysor/output'
adata=format_baysor_output_to_adata(path, output_path)
```

#### Spatial Analysis
```python
# Calculate neighborhood enrichment
adata1 = neighborhood_enrichment(adata,
                                sample_key='sample',
                                radius=50,
                                cluster_key='cell_type')

# Identify spatially variable genes
adata1 = spatially_variable_features(adata1,
                                    sample_key='sample',
                                    radius=50)

# Generate spatial plots
spatial_plot(adata, key='cell_type', clusters='all',
           size=10, background='white',
           figuresize=(10, 8), save=True)
```

#### Quality Metrics
```python
# Calculate cell density
cell_density_value = cell_density(adata_sp, pipeline_output=True)

# Calculate negative marker purity
purity_score = negative_marker_purity(adata_sp, adata_sc,
                                    key='cell_type',
                                    pipeline_output=True)

# Compute clustering comparisons
fmi = fowlkes_mallows_index(ground_truth, predicted)
nmi = normalized_mutual_info_score(ground_truth, predicted)
vi = variation_of_information(ground_truth, predicted)
```

## Key Concepts

### Xenium Spatial Transcriptomics
- **Xenium Platform**: In situ sequencing technology for spatial gene expression profiling
- **Spatial Resolution**: Subcellular localization of transcript molecules
- **Quality Metrics**: Transcript quality scores and distance to nucleus filtering

### Analysis Modules
- **xb.formatting**: Raw data processing and AnnData conversion
- **xb.preprocessing**: Normalization, clustering, and spatial preprocessing
- **xb.domain_identification**: Spatial domain identification (Banksy, NBD, RBD)
- **xb.plotting**: Spatial visualization and plotting functions
- **xb.calculating**: Metrics, distances, and quality assessments
- **xb.neighborhood**: Cell-cell neighborhood analysis
- **xb.comparing**: Benchmarking and comparison metrics

### Domain Identification Methods
- **Banksy**: Incorporates neighboring cell information into clustering (preferred)
- **NBD (Neighbors-based domains)**: Uses neighboring cell type composition
- **RBD (Read-based domains)**: Collapses expression from neighboring cells

## Reference Files

This skill includes comprehensive documentation in `references/`:

- **main_documentation.md** - Core module API documentation
  - xb package structure (12 modules)
  - Function parameters and returns
  - Usage examples for each function

- **pipeline.md** - End-to-end workflow documentation
  - Complete 7-step analysis pipeline
  - Parameter configuration examples
  - Docker setup for Baysor integration
  - Best practices and troubleshooting tips

## Working with This Skill

### For Beginners
1. **Start with Data Formatting**: Use `format_to_adata()` to process Xenium outputs
2. **Quality Control**: Set appropriate `max_nucleus_distance` and `min_quality` filters
3. **Basic Clustering**: Follow the preprocessing pipeline with default parameters
4. **Visualization**: Use `spatial_plot()` to explore cell type distributions

### For Intermediate Users
1. **Domain Identification**: Implement Banksy for spatial domain discovery
2. **Parameter Tuning**: Adjust Banksy lambda_list and geometric parameters
3. **Quality Metrics**: Calculate cell density and negative marker purity
4. **Comparative Analysis**: Fowlkes-Mallows index for cluster comparison

### For Advanced Users
1. **Alternative Segmentation**: Integrate Baysor for refined cell boundaries
2. **Spatially Variable Genes**: Moran's I analysis for spatial gene patterns
3. **Neighborhood Enrichment**: Cell-type interaction analysis
4. **Benchmarking**: Compare with scRNA-seq reference datasets
5. **Custom Workflows**: Combine modules for specialized analyses

### Navigation Tips
- Use the pipeline.md as a step-by-step guide for complete analyses
- Refer to main_documentation.md for detailed function parameters
- Start with small datasets to optimize parameters before scaling
- Check computational requirements when processing multiple samples
- Verify Docker is installed for Baysor integration

## Resources

### references/
- **main_documentation.md**: Complete API reference for all xb modules
- **pipeline.md**: Full workflow from raw Xenium data to final results

### scripts/
Add helper scripts for:
- Batch processing of multiple samples
- Automated parameter optimization
- Quality control report generation
- Custom spatial analysis workflows

### assets/
Add example data for:
- Test datasets to validate workflows
- Configuration templates
- Visualization examples
- Troubleshooting test cases

## Performance Tips

### Memory Management
- Use parquet files with `use_parquet=True` for faster loading
- Apply strict quality filters for large datasets
- Consider cropping regions of interest (ROI) for debugging

### Computational Efficiency
- Adjust `rate_limit` parameters based on system resources
- Use clustering parameters appropriate for data size
- Test with subset of cells before full analysis

### Scaling to Multiple Samples
- Use batch functions (`batch_prep_xenium_data_for_baysor`) for multiple samples
- Standardize coordinate systems with `modify_coords_for_banksy`
- Implement consistent naming conventions across samples

## Troubleshooting

### Common Issues
- **Memory Errors**: Reduce dataset size or use subset of genes
- **Coordinate Problems**: Verify spatial coordinates are in micrometers
- **Import Errors**: Ensure all dependencies are installed (`pip install xb`)
- **Docker Issues**: Check Docker permissions and memory allocation

### Quality Control
- Monitor transcript quality distributions
- Validate spatial coordinate ranges
- Check cell-type annotation consistency
- Verify neighbor graph connectivity

## Updating

To refresh this skill with updated documentation:
1. Re-run the local documentation scraper
2. Update xenium-benchmarking-docs-local module if API changes occurred
3. Test with new Xenium software versions
4. Update example datasets and configurations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ketomihine) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
