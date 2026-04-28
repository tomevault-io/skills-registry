---
name: python-analytics
description: Python data analysis for healthcare analytics and medical imaging informatics Use when this capability is needed.
metadata:
  author: seqis
---

# Python Analytics Skill

## Overview

Comprehensive Python data analysis patterns for healthcare analytics, medical imaging informatics, and operational intelligence. Covers pandas/numpy workflows, statistical testing, visualization, Jupyter best practices, and automated reporting.

## Type

domain-expertise

## When to Use

**Trigger this skill when:**
- Data manipulation with pandas/numpy
- Statistical analysis or hypothesis testing
- Data visualization (matplotlib, seaborn, plotly)
- Jupyter notebook development
- Healthcare/PACS analytics
- Automated report generation
- Data cleaning workflows

**Keywords:** pandas, numpy, scipy, matplotlib, seaborn, plotly, jupyter, notebook, EDA, statistics, visualization, PACS, DICOM, healthcare analytics

---

## 1. Data Manipulation & Cleaning (Pandas/NumPy)

### Core Patterns

```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

# Load with date parsing
df = pd.read_csv('data.csv', parse_dates=['StudyDate', 'SeriesDate'])

# Clean and standardize
df['Modality'] = df['Modality'].str.upper().str.strip()
df['Description'] = df['Description'].str.strip().fillna('UNKNOWN')

# Handle missing values
df['PatientAge'] = df['PatientAge'].fillna(df['PatientAge'].median())
df['PatientAge'] = df.groupby('Modality')['PatientAge'].transform(
    lambda x: x.fillna(x.median())
)

# Create derived features
df['StudyHour'] = df['StudyDate'].dt.hour
df['DayOfWeek'] = df['StudyDate'].dt.day_name()

# Remove duplicates
df = df.drop_duplicates(subset=['StudyInstanceUID'], keep='first')

# Aggregations
summary = df.groupby(['Modality', df['StudyDate'].dt.year]).agg({
    'StudyCount': 'sum',
    'SizeGB': ['sum', 'mean']
}).reset_index()
```

### DICOM Metadata Cleaning

```python
def clean_dicom_metadata(df):
    """Comprehensive DICOM metadata cleaning."""
    df = df.copy()

    # Standardize dates
    date_cols = ['StudyDate', 'SeriesDate', 'AcquisitionDate']
    for col in date_cols:
        df[col] = pd.to_datetime(df[col], errors='coerce', infer_datetime_format=True)

    # Standardize modality codes
    modality_mapping = {
        'CR': 'CR', 'DX': 'CR',
        'CT': 'CT', 'MR': 'MR', 'MRI': 'MR',
        'US': 'US', 'ULTRASOUND': 'US',
        'NM': 'NM', 'PT': 'PT',
        'MG': 'MG', 'MAMMO': 'MG'
    }
    df['Modality'] = df['Modality'].str.upper().map(modality_mapping).fillna(df['Modality'])

    # Parse age (handles '052Y', '52', etc.)
    def parse_age(age_str):
        if pd.isna(age_str):
            return np.nan
        age_str = str(age_str).strip()
        if 'Y' in age_str:
            return int(age_str.replace('Y', ''))
        try:
            return int(age_str)
        except:
            return np.nan

    df['PatientAge'] = df['PatientAge'].apply(parse_age)

    # Flag suspicious data
    df['QualityFlag'] = 'OK'
    df.loc[df['PatientAge'] > 120, 'QualityFlag'] = 'SUSPICIOUS_AGE'
    df.loc[df['StudyDate'] < '1990-01-01', 'QualityFlag'] = 'SUSPICIOUS_DATE'

    return df
```

### Healthcare Workflow Metrics

```python
# Turnaround time analysis
workflow['TAT_hours'] = (
    workflow['ReportSignedTime'] - workflow['ExamCompleteTime']
).dt.total_seconds() / 3600

# Remove outliers (>99th percentile)
p99 = workflow['TAT_hours'].quantile(0.99)
workflow_clean = workflow[workflow['TAT_hours'] <= p99]

# Stratified summary
tat_summary = workflow_clean.groupby(['Priority', 'Modality']).agg({
    'TAT_hours': ['mean', 'median', 'std', lambda x: x.quantile(0.95)]
}).round(2)
```

---

## 2. Statistical Analysis & Hypothesis Testing

### A/B Testing for Workflow Changes

```python
from scipy import stats
import numpy as np

def compare_interventions(baseline_df, intervention_df, metric='TAT_hours'):
    """
    Compare workflow metrics before/after intervention.
    H0: No difference | H1: Intervention reduced metric
    """
    baseline = baseline_df[metric].dropna()
    intervention = intervention_df[metric].dropna()

    # Descriptive statistics
    print(f"Baseline: mean={baseline.mean():.2f}, median={baseline.median():.2f}, n={len(baseline)}")
    print(f"Intervention: mean={intervention.mean():.2f}, median={intervention.median():.2f}, n={len(intervention)}")

    # Normality test
    _, p_base = stats.shapiro(baseline.sample(min(5000, len(baseline))))
    _, p_int = stats.shapiro(intervention.sample(min(5000, len(intervention))))

    # Choose appropriate test
    if p_base < 0.05 or p_int < 0.05:
        stat, p = stats.mannwhitneyu(baseline, intervention, alternative='greater')
        test_name = "Mann-Whitney U (non-parametric)"
    else:
        stat, p = stats.ttest_ind(baseline, intervention, alternative='greater')
        test_name = "Independent t-test"

    # Effect size (Cohen's d)
    pooled_std = np.sqrt((baseline.std()**2 + intervention.std()**2) / 2)
    cohens_d = (baseline.mean() - intervention.mean()) / pooled_std

    print(f"\n{test_name}: stat={stat:.4f}, p={p:.4f}, Cohen's d={cohens_d:.3f}")

    if p < 0.05:
        improvement = baseline.mean() - intervention.mean()
        pct = (improvement / baseline.mean()) * 100
        print(f"Significant: {improvement:.2f} ({pct:.1f}%) improvement")

    return {'p_value': p, 'cohens_d': cohens_d, 'significant': p < 0.05}
```

### Trend Analysis

```python
from scipy.stats import linregress

def analyze_trends(df, date_col='StudyDate', value_col='StudyCount'):
    """Analyze volume trends over time."""
    monthly = df.groupby(pd.Grouper(key=date_col, freq='M'))[value_col].sum().reset_index()
    monthly['MonthNum'] = range(len(monthly))

    slope, intercept, r, p, _ = linregress(monthly['MonthNum'], monthly[value_col])

    annual_growth = slope * 12
    pct_growth = (annual_growth / monthly[value_col].mean()) * 100

    print(f"Slope: {slope:.2f}/month, R2: {r**2:.3f}, p: {p:.4f}")
    print(f"Annual: {annual_growth:.1f} ({pct_growth:+.1f}%)")

    return monthly
```

### Outlier Detection

```python
def detect_outliers(df, columns, method='iqr', threshold=1.5):
    """Detect outliers using IQR or Z-score."""
    outliers = pd.DataFrame()

    for col in columns:
        if method == 'iqr':
            Q1, Q3 = df[col].quantile(0.25), df[col].quantile(0.75)
            IQR = Q3 - Q1
            mask = (df[col] < Q1 - threshold * IQR) | (df[col] > Q3 + threshold * IQR)
        elif method == 'zscore':
            z = np.abs(stats.zscore(df[col].dropna()))
            mask = z > threshold

        outliers[f'{col}_outlier'] = mask
        print(f"{col}: {mask.sum()} outliers ({mask.sum()/len(df)*100:.2f}%)")

    return outliers
```

---

## 3. Data Visualization

### Matplotlib/Seaborn Patterns

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Set publication defaults
sns.set_style("whitegrid")
plt.rcParams['figure.figsize'] = (12, 6)
plt.rcParams['font.size'] = 11

def plot_modality_distribution(df, save_path=None):
    """Publication-quality modality distribution."""
    counts = df['Modality'].value_counts().head(10)
    colors = sns.color_palette("husl", len(counts))

    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(14, 6))

    # Bar plot
    counts.plot(kind='bar', ax=ax1, color=colors)
    ax1.set_title('Top 10 Modalities', fontsize=14, fontweight='bold')
    ax1.set_xlabel('Modality')
    ax1.set_ylabel('Study Count')
    ax1.tick_params(axis='x', rotation=45)

    # Add value labels
    for i, v in enumerate(counts):
        ax1.text(i, v + counts.max() * 0.01, f'{v:,}', ha='center', fontsize=10)

    # Pie chart
    ax2.pie(counts, labels=counts.index, autopct='%1.1f%%', colors=colors)
    ax2.set_title('Distribution (%)', fontsize=14, fontweight='bold')

    plt.tight_layout()
    if save_path:
        plt.savefig(save_path, dpi=300, bbox_inches='tight')
    plt.show()
```

### Plotly Interactive Dashboards

```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots

def create_volume_dashboard(df):
    """Interactive PACS volume dashboard."""
    daily = df.groupby([df['StudyDate'].dt.date, 'Modality']).size().reset_index(name='Count')

    fig = make_subplots(
        rows=2, cols=2,
        subplot_titles=('Daily Volumes', 'Modality Distribution', 'Hourly Pattern', 'Weekly Pattern'),
        specs=[[{"type": "scatter"}, {"type": "bar"}],
               [{"type": "bar"}, {"type": "bar"}]]
    )

    # Time series by modality
    for mod in daily['Modality'].unique():
        mod_data = daily[daily['Modality'] == mod]
        fig.add_trace(go.Scatter(x=mod_data.iloc[:, 0], y=mod_data['Count'], name=mod, mode='lines'), row=1, col=1)

    # Modality totals
    totals = df['Modality'].value_counts()
    fig.add_trace(go.Bar(x=totals.index, y=totals.values, marker_color='lightblue'), row=1, col=2)

    # Hourly pattern
    hourly = df.groupby(df['StudyDate'].dt.hour).size()
    fig.add_trace(go.Bar(x=hourly.index, y=hourly.values, marker_color='lightgreen'), row=2, col=1)

    # Day of week
    dow_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']
    dow = df.groupby(df['StudyDate'].dt.day_name()).size().reindex(dow_order)
    fig.add_trace(go.Bar(x=dow.index, y=dow.values, marker_color='coral'), row=2, col=2)

    fig.update_layout(height=800, title_text="PACS Volume Dashboard", showlegend=True)
    return fig
```

---

## 4. Jupyter Notebook Best Practices

### Standard Notebook Structure

```python
# Cell 1: Setup and Configuration
"""
Analysis Title - Date
Author: User
Purpose: [Clear objective]
"""
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore')

pd.set_option('display.max_columns', None)
pd.set_option('display.float_format', '{:.2f}'.format)
sns.set_style("whitegrid")
plt.rcParams['figure.figsize'] = (12, 6)

print(f"Analysis started: {datetime.now()}")

# Cell 2: Data Loading
DATA_PATH = '/path/to/data.csv'
df = pd.read_csv(DATA_PATH, parse_dates=['DateCol'])
print(f"Loaded {len(df):,} records")
df.head()

# Cell 3: Data Quality Check
print(f"Missing values:\n{df.isnull().sum()}")
print(f"Duplicates: {df.duplicated().sum()}")

# Cell 4: Analysis
# ... analysis code ...

# Cell 5: Visualization
# ... plots ...

# Cell 6: Summary
print("\nKEY FINDINGS:")
print("1. ...")
```

### Parameterized Notebooks (papermill)

```python
# Tag cell as 'parameters'
START_DATE = '2025-01-01'
END_DATE = '2025-12-31'
MODALITIES = ['CT', 'MR']
OUTPUT_PATH = '/path/to/reports/'

# Run: papermill notebook.ipynb output.ipynb -p START_DATE '2025-06-01'
```

### Notebook to Production Script

```python
#!/path/to/venv/bin/python
"""Production script converted from notebook."""
import argparse
import logging
import pandas as pd

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

def load_data(path):
    logging.info(f"Loading {path}")
    return pd.read_csv(path, parse_dates=['StudyDate'])

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--input', required=True)
    parser.add_argument('--output', required=True)
    args = parser.parse_args()

    df = load_data(args.input)
    # Process...

if __name__ == '__main__':
    main()
```

---

## 5. Automated Reporting

### HTML Report Generation

```python
def generate_weekly_report(df, week_start, week_end):
    """Generate HTML weekly report."""
    week_df = df[(df['StudyDate'] >= week_start) & (df['StudyDate'] <= week_end)]

    total = len(week_df)
    by_modality = week_df['Modality'].value_counts()
    daily_avg = total / 7

    # Compare to previous week
    prev_start = week_start - timedelta(days=7)
    prev_df = df[(df['StudyDate'] >= prev_start) & (df['StudyDate'] < week_start)]
    prev_total = len(prev_df)
    pct_change = ((total - prev_total) / prev_total * 100) if prev_total > 0 else 0

    html = f"""
    <html>
    <head><style>
        body {{ font-family: Arial, sans-serif; }}
        table {{ border-collapse: collapse; width: 100%; }}
        th, td {{ border: 1px solid #ddd; padding: 8px; }}
        th {{ background-color: #3498db; color: white; }}
    </style></head>
    <body>
        <h1>Weekly Volume Report</h1>
        <p>Week: {week_start:%Y-%m-%d} to {week_end:%Y-%m-%d}</p>
        <h2>Summary</h2>
        <p>Total Studies: {total:,} ({pct_change:+.1f}% vs prior week)</p>
        <p>Daily Average: {daily_avg:.0f}</p>
        <h2>By Modality</h2>
        <table><tr><th>Modality</th><th>Count</th><th>%</th></tr>
    """

    for mod, count in by_modality.items():
        html += f"<tr><td>{mod}</td><td>{count:,}</td><td>{count/total*100:.1f}%</td></tr>"

    html += f"</table><p><em>Generated: {datetime.now()}</em></p></body></html>"
    return html
```

---

## 6. Performance Optimization

### Best Practices

```python
# Vectorization over loops
df['result'] = df['col1'] * df['col2']  # Good
# for i in range(len(df)): df.loc[i, 'result'] = ...  # Bad

# Chunked reading for large files
for chunk in pd.read_csv('large.csv', chunksize=100000):
    process(chunk)

# Efficient dtypes
df['category_col'] = df['category_col'].astype('category')
df['int_col'] = df['int_col'].astype('int32')

# Query over boolean indexing for complex filters
df.query('age > 30 and status == "active"')

# Memory cleanup
del large_df
import gc; gc.collect()
```

---

## 7. Medical Imaging Specifics

### DICOM Extraction

```python
import pydicom
import os

def extract_dicom_metadata(dicom_dir):
    """Extract metadata from DICOM directory."""
    metadata = []

    for root, _, files in os.walk(dicom_dir):
        for f in files:
            if f.endswith('.dcm'):
                try:
                    ds = pydicom.dcmread(os.path.join(root, f))
                    metadata.append({
                        'StudyInstanceUID': str(ds.StudyInstanceUID),
                        'Modality': str(ds.Modality),
                        'StudyDate': str(ds.StudyDate),
                        'PatientID': str(ds.PatientID),
                        'StudyDescription': str(getattr(ds, 'StudyDescription', ''))
                    })
                except Exception as e:
                    print(f"Error: {e}")

    return pd.DataFrame(metadata)
```

---

## Best Practices Summary

| Category | Practice |
|----------|----------|
| **Code Quality** | Type hints, docstrings, try/except, logging |
| **Performance** | Vectorize, chunk large files, efficient dtypes |
| **Reproducibility** | Random seeds, version tracking, parameterization |
| **Healthcare** | Validate assumptions, handle missing data, communicate uncertainty |

---

## Communication Style

- Provide working code with actual data patterns
- Explain statistical methodology and assumptions
- Flag data quality issues proactively
- Suggest appropriate visualizations
- Include interpretation, not just code
- Follow "Actually Works" protocol

---

*Last updated: 2024-12-23*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seqis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
