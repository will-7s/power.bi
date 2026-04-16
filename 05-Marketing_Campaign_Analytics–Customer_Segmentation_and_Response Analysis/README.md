# Marketing Campaign Analytics – Customer Segmentation & Response Analysis

**Stack:** Power BI Desktop · Power Query (M) · DAX · Field Parameters · Python (EDA App)  
**Dataset:** 2,240 customer records · 29 features · 8 marketing campaigns  (5 created)
**Scope:** End-to-end pipeline — from raw, inconsistent customer data to a segmented, interactive campaign performance dashboard

**EDA App used:** https://huggingface.co/spaces/will-7s/eda_app

Power BI Link: https://app.powerbi.com/groups/me/reports/a2d04256-0fba-4077-97df-473edb293332/cdee724ed3dabc266a91?ctid=fd1df4e5-2eb0-410e-a611-12513030b133&experience=power-bi&bookmarkGuid=c81a2a3b-41ce-461a-ba77-325cf8eb014d

---

## 📌 Project Overview

Raw marketing data contained:
- Absurd/inconsistent categorical values (`Marital_Status`: `"YOLO"`, `"Absurd"`, `"Alone"`)
- 24 missing values in `Income` column
- Irrelevant columns (`ID`, `Complain`, `Z_CostContact`, `Z_Revenue`)
- No business-ready aggregated variables (total spend, total purchases, campaign score)
- Raw numeric variables with no segmentation (age, recency, income, tenure)

**Result:** A fully engineered dataset with cleaned categories, imputed values, 7 computed business features, 8 customer segmentation dimensions (5 created), and a dynamic Power BI dashboard enabling cross-segment campaign performance analysis.

---

## 🧰 Tools & Techniques

| Layer | Tools / Techniques |
|-------|--------------------|
| **Data Profiling** | EDA App (Column Quality, Distribution, "Outlier" detection) |
| **ETL & Cleansing** | Remove Columns, Replace Values, Median imputation |
| **Feature Engineering** | Custom Columns (Total Response, Nb Kids, Total Purchases, Expenses, Age) |
| **Customer Segmentation** | DAX Calculated Columns — 8 segmentation dimensions (5 created) |
| **Dynamic Visuals** | Field Parameters → one slicer drives all visuals |
| **DAX Measures** | SUMX, SUM, DIVIDE — KPIs + Response Rate |
| **Interactivity** | Slicers, Bookmarks (reset all filters), Conditional Formatting |
| **Statistical Analysis** | Chi-square test, Fisher's exact test, Mann-Whitney U (via EDA App) and so one... |

---

## ⚙️ Power Query Pipeline

### Step 1 — Data Quality Assessment

Used the **EDA App** for fast profiling before any transformation:

- **Outliers detected:** `Year_Birth` min = 1893 (0.1% of rows), `Income` max = 666,666 (0.4%)
- **Missing values:** 24 nulls in `Income` (~1.1%) → imputed with **median** (≈ mean, distribution not skewed)
- **Absurd categories:** `Marital_Status` contained `"YOLO"`, `"Absurd"`, `"Alone"` → standardized

### Step 2 — Column Removal

| Column removed | Reason |
|----------------|--------|
| `ID` | Not relevant for analysis |
| `Complain` | Does not explain campaign acceptance |
| `Z_CostContact` | Constant value — no variance |
| `Z_Revenue` | Constant value — no variance |

### Step 3 — Categorical Cleaning

**Problem:** `Marital_Status` contained non-standard values

**Solution — Replace Values:**

| Original value | Replaced by |
|----------------|-------------|
| `Alone` | `Single` |
| `YOLO` | `Married` |
| `Absurd` | `Together` |

### Step 4 — Feature Engineering (5 new business variables)

| New Column | Formula / Logic | Business Purpose |
|------------|----------------|-----------------|
| `Total_Response` | Sum of all `AcceptedCmpX` + `Response` | Total campaigns accepted per customer |
| `Nb_Kids` | `Kidhome + Teenhome` | Total children in household |
| `Total_Purchases` | Sum of all `NumXxxPurchases` channels | Overall purchase activity |
| `Expenses` | Sum of all `MntXxx` product categories | Total spend across all categories |
| `Age` | `Current_Year − Year_Birth` | Customer age |

Original component columns removed after aggregation to keep the model clean.

---

## 🧩 Customer Segmentation (DAX Calculated Columns)

Five segmentation dimensions built directly in the Power BI model using `IF` logic for custom column:

| Dimension | Segments | Basis |
|-----------|----------|-------|
| **Income** | Low / Medium / High / Very High | Univariate threshold analysis |
| **Recency** | New/Active · Engaged · Cooling · Churned | Days since last purchase |
| **Nb of Monthly Web Visits** | Low · Medium · High · Very High | `NumWebVisitsMonth` |
| **Age** | Young · Adult · Middle-aged · Senior | Computed `Age` column |
| **Customer Tenure** | New · Growing · Established · Loyal · Veteran | `Dt_Customer` enrollment date |

> Segment boundaries defined from **univariate distribution analysis** — not arbitrary — ensuring meaningful, data-driven groupings.

---

## 📐 Statistical Analysis (Python — Jupyter Notebook)

Before building the dashboard, a rigorous statistical analysis was conducted to identify which features actually explain campaign acceptance.

### Features significantly associated with `Response` (last campaign)

**Significant** (Chi-square / Fisher's exact test):
`NumCatalogPurchases`, `NumWebPurchases`, `MntGoldProds`, `MntSweetProducts`, `MntFishProducts`, `MntMeatProducts`, `MntFruits`, `MntWines`, `Recency`, `Teenhome`, `Kidhome`, `Income`, `Marital_Status`, `Education`

**Not significant:**
`Complain`, `NumWebVisitsMonth`, `NumStorePurchases`, `NumDealsPurchases`, `Year_Birth`

### Campaign acceptance rate overview

| Campaign | Acceptance rate |
|----------|----------------|
| Campaign 1 | Moderate |
| Campaign 2 | **Lowest** — worst performing |
| Campaign 3 | Moderate |
| Campaign 4 | Moderate |
| Campaign 5 | Moderate |
| **Last campaign (Response)** | **14.9%** (334 / 2,240) |

### Features engineered — validated by statistical tests

All 5 engineered features (`Year_Birth`, `Income`, `Dt_Customer`, `Recency`, `Nb_Kids`, `Expenses`, `Nb_Purchases`) show **statistically significant differences** between campaign acceptance groups.

---


## 📈 Dashboard Design

### Field Parameter — Dynamic Segmentation

A **Field Parameter** was created over all 8 segmentation dimensions (5 created). One slicer controls which dimension is active — all visuals update simultaneously without page duplication.

```
Selected segment → [Income band | Recency | Web Visits | Age group | Tenure]
         ↓
All charts and KPI cards update dynamically
```

### Visuals Built

| Visual | Purpose |
|--------|---------|
| KPI Cards | Response Rate, Avg Spent, Avg Purchases, Total Accepted |
| Grouped bar chart | Campaign performance by selected customer segment |
| Distribution chart | Customer count per segment band |
| Slicers | Filter by Education, Marital Status, segment dimension |
| Bookmark | Reset all filters to default state |


---

## 💡 Key Skills Demonstrated

| Skill | Application |
|-------|-------------|
| **Statistical rigor** | Chi-square, Fisher's exact, Mann-Whitney U — per campaign, per feature |
| **Data-driven segmentation** | Segment thresholds derived from univariate distributions, not arbitrary |
| **Feature engineering** | 5 business variables synthesized from raw columns — meaningful for marketing |
| **Missing value strategy** | Median imputation justified by distribution shape (mean ≈ median) |
| **Dirty data handling** | `Marital_Status` absurd values identified, documented, and standardized |
| **Field Parameters** | One slicer drives all visuals across 8 segmentation dimensions |
| **DAX measures** | `CALCULATE`, `DIVIDE` for safe rate computation, `SUM` for row-level aggregation |
| **Tool integration** | EDA App → Python notebook → Power BI — each tool used where it excels |
| **End-to-end ownership** | Raw CSV → statistical analysis → engineered features → deployed dashboard |

---

## 📁 Project Artifacts

| File | Purpose |
|------|---------|
| `marketing_campaign.csv / .xlsx` | Raw data — 2,240 customers, 29 features, 5 (+1) campaigns |
| `marketing.ipynb` | EDA notebook — univariate, bivariate, feature engineering |
| Power BI report (`.pbix`) | Final dashboard with all transformations and segments embedded |

---

*This project demonstrates that effective marketing analytics requires both statistical validation (which features matter?) and business translation (how do we segment and act on them?) — Power BI handles the latter only after Python answers the former.*
