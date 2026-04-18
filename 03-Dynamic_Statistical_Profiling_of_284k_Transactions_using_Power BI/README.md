# 🏦 Dynamic Statistical Profiling using Power BI + Python
 
> **Stack:** Power BI Desktop · Power Query (M + Python) · DAX · Field Parameters · Custom Visuals  
> **Dataset:** 284,807 European credit card transactions (Kaggle, 2013) — PCA-transformed features V1–V28  
> **Goal:** Perform univariate & bivariate statistical exploration of a highly imbalanced fraud detection dataset

---

## 📌 Project Overview

This project goes beyond a standard dashboard — it is a **statistical analysis platform built entirely in Power BI**, designed to explore a real-world fraud detection dataset with the rigor of a data scientist and the interactivity of a BI tool.

Power BI link: https://app.powerbi.com/groups/me/reports/20522af1-5547-4792-a0a7-04edef1f8d70/0da768d79943273340ac?ctid=fd1df4e5-2eb0-410e-a611-12513030b133&experience=power-bi 

Drive Link: https://mega.nz/file/RV1whaCL#9xJhuEK4Vw-WmEmWOyumchNjkdko-xVZgAcJEd4omx8

Three analytical layers were built:

| Page | Purpose |
|------|---------|
| **Overview** | Global dataset profile — descriptive stats for all 31 columns, class distribution |
| **Univariate Analysis** | Dynamic per-variable exploration — distribution curve, descriptive stats, normality insights |
| **Bivariate Analysis** | Variable vs. `Class` (fraud/normal) — statistical test results, group comparison visuals |

> The entire analysis is **dynamic**: variable selection drives all visuals and metrics simultaneously via **Field Parameters** — no page reload, no filter panel.

---

## 🧰 Stack

| Layer | Tools / Concepts |
|-------|-----------------|
| **Data Ingestion** | Power Query — CSV load, type validation, duplicate removal |
| **Custom Profiling** | M language `Table.Profile()` + custom M script for full-dataset stats |
| **Python in Power Query** | `pandas` + `numpy` for scalable descriptive statistics across all columns |
| **DAX — Dynamic Measures** | `SWITCH`, `MAX`, `SUM`, `COUNTROWS`, `SELECTEDVALUE` driven by Field Parameters |
| **Field Parameters** | Dynamic column selector — one slicer controls all visuals and DAX measures |
| **Custom Visuals** | Histogram (3rd-party) — evaluated and integrated |
| **Navigation** | Button-based page navigation with active-state visual indicators |
| **Data Modeling** | Reference queries (no duplicate load), index column, helper `All` column |

---

## 🧠 Skills Demonstrated

| 💼 Skill | 📌 Concrete Application |
|----------|------------------------|
| 🧹 **Data Quality Audit** | `Column Quality` + `Column Distribution` + `Column Profile` — validated 284,807 rows across 31 columns, zero nulls confirmed |
| 🗑️ **Deduplication at Scale** | `Home → Remove Duplicates` on full column selection — removed 1,081 duplicate rows, confirmed via `Count Rows` |
| 🔗 **Reference Queries** | Used Power Query **Reference** (not Duplicate) to build the profiling table — avoids loading source data twice into the model |
| 🧪 **Custom M Profiling Script** | Wrote a full M `let...in` script computing `Column`, `Type`, `Total`, `Nulls`, `Distinct`, `Mode`, `Min`, `Max`, `Mean`, `Median` for every column dynamically |
| 🐍 **Python in Power Query** | Integrated `pandas`/`numpy` script inside Power Query to compute `mean`, `median`, `std`, `Q1`, `Q3`, `IQR`, `mode` per column — handling both numeric and categorical types |
| 📐 **DAX — Field Parameter driven** | Created a `selected_numeric_field` Field Parameter — all DAX measures (`histogram_count`, descriptive stats) route through `SWITCH(MAX(...))` to respond dynamically |
| 🔀 **DAX — SWITCH dispatch** | `histogram_count` measure uses a 30-branch `SWITCH` to map the selected field name to the correct `SUM(creditcard[Vn])` — enables one chart to display any variable |
| 🧩 **Dynamic Visuals** | Single line chart behaves as a distribution curve for any selected variable — axis updates, values update, title updates — all from one slicer |
| 📑 **Multi-page Report Design** | Three-page report with button-based navigation and active-page visual indicator (rounded rectangle sent to background) |
| 📈 **Univariate Statistics** | Distribution shape analysis (normal, bimodal, trimodal, multimodal) across 28 PCA components + `Time` + `Amount` |

---

## 📁 Dataset

**Source:** [Kaggle — Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)  
**Context:** European cardholders, September 2013 — 2 days of transactions

| Column | Description |
|--------|-------------|
| `V1`–`V28` | PCA-derived principal components (anonymized) |
| `Time` | Seconds elapsed since first transaction — bimodal (2-day recording) |
| `Amount` | Transaction amount — right-skewed, range [0, 25,000] |
| `Class` | Target — `0` = normal, `1` = fraud |

**Post-cleaning:** 283,726 rows — 1,081 duplicates removed  
**Class balance:** 99.83% normal / 0.17% fraud — extreme imbalance

---

## ⚙️ Power Query Pipeline

### Step 1 — Load & Type Validation
```
Home → New Source → Text/CSV → creditcard.csv
Transform → Verify column types (31 columns)
Transform → Count Rows → 284,807 confirmed
```

### Step 2 — Deduplication
```
Home → Reduce Rows → Remove Duplicates (all columns selected)
Transform → Count Rows → 283,726 remaining (−1,081 rows)
```

### Step 3 — Full-Dataset Statistical Profile

Native `Table.Profile()` was too slow on 283k rows with custom parameters. Two approaches explored:

**Approach A — Custom M Script** (Reference query, not duplicate):
```m
let
    Source       = #"Changed Type",
    Colonnes     = Table.ColumnNames(Source),
    EstNumerique = (colonne) => ...
    CalculerStats = (colonne) => [
        Column   = colonne,
        Type     = if numerique then "Number" else "Text",
        Total    = List.Count(valeurs),
        Nulls    = List.Count(List.Select(valeurs, each _ = null)),
        Distinct = List.Count(List.Distinct(valeurs)),
        Mode     = List.Mode(valeurs),
        Min      = if numerique then List.Min(valeursSansNull) else null,
        Max      = if numerique then List.Max(valeursSansNull) else null,
        Mean     = if numerique then List.Average(valeursSansNull) else null,
        Median   = if numerique then List.Median(valeursSansNull) else null
    ],
    Resultat = Table.FromRecords(List.Transform(Colonnes, CalculerStats))
in  Resultat
```

**Approach B — Python in Power Query** (faster on full dataset):
```python
import pandas as pd
import numpy as np

df = dataset.copy()
# Computes: mode, mean, median, std_dev, Q1, Q3, IQR
# Handles both numeric and categorical columns
# Returns a flat DataFrame — one row per (variable, category)
```

> ⚠️ Lesson documented: `Table.Profile()` with custom aggregators loses Power Query's vectorized execution — Python via Power Query is significantly faster at this scale.


---

## 📐 DAX — Dynamic Measures via Field Parameters

### Field Parameter Setup
```
Modeling → New Parameter → Fields → select all 30 numeric columns
→ named: selected_numeric_field
```

This generates a disconnected table. Every DAX measure reads the selected field name via `MAX('selected_numeric_field'[selected_numeric_field])`.

### Core Dynamic Measure
```dax
histogram_count =
VAR field_name = MAX('selected_numeric_field'[selected_numeric_field])
VAR current_value =
    SWITCH(
        field_name,
        "time",   SUM('creditcard'[Time]),
        "amount", SUM('creditcard'[Amount]),
        "v1",     SUM('creditcard'[V1]),
        "v2",     SUM('creditcard'[V2]),
        -- ... up to v28
    )
RETURN COUNTROWS('creditcard')
```

The same `SWITCH` pattern was applied to all descriptive statistics measures — **mean, median, std dev, min, max, Q1, Q3** — each dynamically routing to the correct column based on slicer selection.

> This architecture means **one set of visuals serves all 30 variables** — the slicer drives everything. No page duplication, no static reports.

---

## 📊 Dashboard Pages

### Page 1 — Overview
- Full statistical profile table (all 31 columns): Type, Count, Nulls, Distinct, Min, Max, Mean, Median
- Donut chart: `Class` distribution (Normal vs. Fraud)
- Key dataset stats: 283,726 rows, 31 features, 0 nulls, 0.17% fraud rate

### Page 2 — Univariate Analysis
- **Field Parameter slicer** → selects any of the 30 numeric columns
- Distribution curve (line chart acting as histogram via `COUNTROWS` per value)
- Dynamic stat cards: Mean, Median, Std Dev, Min, Max, Q1, Q3
- Distribution shape annotation: histogram, as variables are numeric


### Page 3 — Bivariate Analysis (vs. `Class`)
- Stack Column Charts / group comparison: Fraud vs. Normal distributions per variable
- For each variable, dynamically choosen, we compare means of the two groups, as well as median, Q1, Q3... DAX measures and field parameters are still used.

---

## 💡 Technical Challenges & Solutions

| Challenge | Root Cause | Solution |
|-----------|-----------|----------|
| `Table.Profile()` extremely slow | Custom aggregators disable vectorized execution | Switched to Python script in Power Query |
| Native histogram not dynamic | Can't bind Field Parameter to bucket axis | Used line chart + `COUNTROWS` per value as histogram proxy |
| Descriptive stats not dynamic natively | DAX aggregations don't auto-switch columns | Built 30-branch `SWITCH(MAX(...))` pattern — one measure per statistic |


---

*This project demonstrates that Power BI can serve as a serious statistical analysis environment — not just a reporting tool — when DAX, Field Parameters, M scripting, and Python integration are combined deliberately.*
