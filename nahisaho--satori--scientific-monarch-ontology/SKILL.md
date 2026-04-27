---
name: monarch-initiative
description: 疾患-表現型-遺伝子オントロジー統合 API Use when this capability is needed.
metadata:
  author: nahisaho
---

# Scientific Monarch Initiative Ontology

Monarch Initiative API を活用した疾患-遺伝子-表現型
アソシエーション取得・HPO ベースフェノタイピング・
セマンティックオントロジー検索パイプラインを提供する。

## When to Use

- 疾患の関連遺伝子・表現型 (HPO) を検索するとき
- 遺伝子から関連疾患・表現型を逆引きするとき
- HPO 用語でフェノタイプマッチングするとき
- オントロジー用語間の意味的類似度を計算するとき
- 疾患-表現型-遺伝子の三者間アソシエーションを統合するとき

---

## Quick Start

## 1. 疾患-遺伝子-表現型アソシエーション

```python
import requests
import pandas as pd

MONARCH_API = "https://api.monarchinitiative.org/v3/api"


def monarch_disease_genes(disease_id, limit=50):
    """
    Monarch — 疾患→関連遺伝子取得。

    Parameters:
        disease_id: str — 疾患 ID
            (例: "MONDO:0007254" = breast cancer)
        limit: int — 最大結果数
    """
    url = f"{MONARCH_API}/association"
    params = {
        "subject": disease_id,
        "category": "biolink:GeneToDiseaseAssociation",
        "limit": limit,
    }
    resp = requests.get(url, params=params, timeout=30)
    resp.raise_for_status()
    data = resp.json()

    rows = []
    for item in data.get("items", []):
        obj = item.get("object", {})
        rows.append({
            "disease_id": disease_id,
            "gene_id": obj.get("id", ""),
            "gene_label": obj.get("label", ""),
            "relation": item.get("predicate", ""),
            "source": "; ".join(
                item.get("provided_by", [])),
        })

    df = pd.DataFrame(rows)
    print(f"Monarch disease→genes: {disease_id} "
          f"→ {len(df)} genes")
    return df


def monarch_disease_phenotypes(disease_id, limit=100):
    """
    Monarch — 疾患→表現型 (HPO) 取得。

    Parameters:
        disease_id: str — 疾患 ID
        limit: int — 最大結果数
    """
    url = f"{MONARCH_API}/association"
    params = {
        "subject": disease_id,
        "category":
            "biolink:DiseaseToPhenotypicFeatureAssociation",
        "limit": limit,
    }
    resp = requests.get(url, params=params, timeout=30)
    resp.raise_for_status()
    data = resp.json()

    rows = []
    for item in data.get("items", []):
        obj = item.get("object", {})
        rows.append({
            "disease_id": disease_id,
            "phenotype_id": obj.get("id", ""),
            "phenotype_label": obj.get("label", ""),
            "frequency": item.get("frequency_qualifier",
                                  ""),
            "onset": item.get("onset_qualifier", ""),
        })

    df = pd.DataFrame(rows)
    print(f"Monarch disease→phenotypes: {disease_id} "
          f"→ {len(df)} HPO terms")
    return df
```

## 2. 遺伝子→疾患逆引き

```python
def monarch_gene_diseases(gene_id, limit=50):
    """
    Monarch — 遺伝子→関連疾患取得。

    Parameters:
        gene_id: str — 遺伝子 ID
            (例: "HGNC:1100" = BRCA1)
        limit: int — 最大結果数
    """
    url = f"{MONARCH_API}/association"
    params = {
        "subject": gene_id,
        "category": "biolink:GeneToDiseaseAssociation",
        "limit": limit,
    }
    resp = requests.get(url, params=params, timeout=30)
    resp.raise_for_status()
    data = resp.json()

    rows = []
    for item in data.get("items", []):
        obj = item.get("object", {})
        rows.append({
            "gene_id": gene_id,
            "disease_id": obj.get("id", ""),
            "disease_label": obj.get("label", ""),
            "relation": item.get("predicate", ""),
        })

    df = pd.DataFrame(rows)
    print(f"Monarch gene→diseases: {gene_id} "
          f"→ {len(df)} diseases")
    return df
```

## 3. エンティティ検索・オントロジー用語

```python
def monarch_search(query, category=None, limit=25):
    """
    Monarch — エンティティテキスト検索。

    Parameters:
        query: str — 検索クエリ
        category: str — カテゴリフィルタ
            (例: "biolink:Disease", "biolink:Gene",
             "biolink:PhenotypicFeature")
        limit: int — 最大結果数
    """
    url = f"{MONARCH_API}/search"
    params = {"q": query, "limit": limit}
    if category:
        params["category"] = category

    resp = requests.get(url, params=params, timeout=30)
    resp.raise_for_status()
    data = resp.json()

    rows = []
    for item in data.get("items", []):
        rows.append({
            "id": item.get("id", ""),
            "label": item.get("name", ""),
            "category": item.get("category", ""),
            "description": (item.get("description", "")
                            or "")[:200],
        })

    df = pd.DataFrame(rows)
    print(f"Monarch search: '{query}' → {len(df)}")
    return df
```

## 4. Monarch 統合パイプライン

```python
def monarch_pipeline(disease_query,
                        output_dir="results"):
    """
    Monarch 統合パイプライン。

    Parameters:
        disease_query: str — 疾患名 or ID
        output_dir: str — 出力ディレクトリ
    """
    from pathlib import Path
    output_dir = Path(output_dir)
    output_dir.mkdir(parents=True, exist_ok=True)

    # 1) 疾患検索
    diseases = monarch_search(disease_query,
                              category="biolink:Disease")
    diseases.to_csv(output_dir / "monarch_diseases.csv",
                    index=False)

    if diseases.empty:
        print(f"Monarch: '{disease_query}' not found")
        return {"diseases": diseases}

    disease_id = diseases.iloc[0]["id"]

    # 2) 関連遺伝子
    genes = monarch_disease_genes(disease_id)
    genes.to_csv(output_dir / "monarch_genes.csv",
                 index=False)

    # 3) 表現型 (HPO)
    phenotypes = monarch_disease_phenotypes(disease_id)
    phenotypes.to_csv(
        output_dir / "monarch_phenotypes.csv",
        index=False)

    print(f"Monarch pipeline: {disease_query} "
          f"→ {output_dir}")
    return {"genes": genes, "phenotypes": phenotypes}
```

---

## ToolUniverse 連携

| TU Key | ツール名 | 連携内容 |
|--------|---------|---------|
| `monarch` | Monarch Initiative | 疾患-表現型-遺伝子オントロジー統合 |

## パイプライン統合

```
disease-research → monarch-ontology → rare-disease-genetics
  (GWAS/DisGeNET)   (Monarch API)     (OMIM/Orphanet)
        │                  │                 ↓
variant-interpretation ───┘       ontology-enrichment
  (ClinVar/ACMG)                 (EFO/OLS/Enrichr)
```

## パイプライン出力

| ファイル | 説明 | 次スキル |
|---------|------|---------|
| `results/monarch_diseases.csv` | 疾患検索結果 | → disease-research |
| `results/monarch_genes.csv` | 関連遺伝子 | → variant-interpretation |
| `results/monarch_phenotypes.csv` | HPO 表現型 | → rare-disease-genetics |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nahisaho) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
