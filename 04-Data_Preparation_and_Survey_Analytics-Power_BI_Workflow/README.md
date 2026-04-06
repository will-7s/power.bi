# Survey Data Preparation & Analytics – Power BI Workflow

**Stack:** Power BI Desktop · Power Query (M) · DAX · Lookup Tables · Data Modeling  
**Dataset:** 600–700 survey responses from data professionals  
**Scope:** Complete data preparation pipeline — from raw, messy survey data to clean, modeled, analysis-ready dataset

**Power BI link:** https://app.powerbi.com/groups/me/reports/e1e4629d-7954-4dd9-95b3-ef0c44fadd11/bb05d68066991cab67a8?ctid=fd1df4e5-2eb0-410e-a611-12513030b133&experience=power-bi&bookmarkGuid=5353a805-940e-4588-9e4b-a2ed4173ad25 

---

## 📌 Project Overview

Raw survey data contained:
- Empty columns (Browser, OS, City, Country, Referrer)
- Free-text "Other" fields requiring standardization
- Salary ranges stored as text (e.g., "50k-126k")
- Inconsistent categorical values across multiple questions

**Result:** A fully prepared dataset with standardized categories, numeric salary bands, and a star schema ready for dashboarding.

---

## 🧰 Tools & Techniques

| Layer | Tools / Techniques |
|-------|-------------------|
| Data Profiling | Column Quality, Column Distribution, Column Profile |
| ETL & Cleansing | Remove Columns, Filter Rows, Replace Values, Split Column |
| Categorization | Lookup tables + Merge Queries (Left Outer Join) + Conditional Column |
| Salary Parsing | Split Column (Digit to Non-Digit) + Replace Values + Custom Column |
| Data Modeling | Star schema, 1:*, *:* relationships, unidirectional/bidirectional |
| DAX | Calculated columns (WEEKDAY, IF), Measures (SUMX), conditional formatting |

---

## ⚙️ Power Query Pipeline

### Step 1 — Data Quality Assessment
- **Column Quality** → Identified 5 completely empty columns → removed
- **Column Distribution** → Analyzed distinct values per column
- **Column Profile** → Assessed value completeness before transformation

### Step 2 — Column Removal & Row Filtering
- Removed empty columns: Browser, OS, City, Country, Referrer
- Filtered rows: e.g., excluded "Milk" from Product column if we don't want to consider it.

### Step 3 — Categorical Standardization (6 columns)

**Problem:** Free-text "Other" fields (Q1, Q4, Q5, Q8, Q11, Q13)

**Solution — Lookup table pattern:**

Excel Pivot Table → Count occurrences → Threshold (≥3 kept) → Lookup table → Power Query Merge → Replace

**Power Query implementation:**
```m
// Merge with lookup table
Table.NestedJoin(Source, {"Q1"}, LookupTable, {"Original"}, "Lookup", JoinKind.LeftOuter)
// Expand and apply conditional column
Table.AddColumn(PreviousStep, "Q1_Cleaned", each if [NewValue] = null then [Q1] else [NewValue])
// Remove original columns
```

**Categories standardized:** Roles, Industry, Programming Language, Job Priority, Country, Ethnicity

### Step 4 — Salary Range Parsing (Text → Numeric)

**Challenge:** Q3 contained salary ranges as text (e.g., "50k-126k", "0-50k")

**Solution:**
| Step | Action |
|------|--------|
| 1 | Duplicate column (preserve original) |
| 2 | Split Column → Digit to Non-Digit |
| 3 | Delete third column (contains only "k") |
| 4 | Replace Values: remove `-` and `k` from second column |
| 5 | Change type to Whole Number |
| 6 | Add Custom Column: `(Lower + Upper) / 2` → Mean salary per band |

---

## 📐 Data Modeling Concepts Explored

| Concept | Application |
|---------|-------------|
| 1-to-many cardinality | Dimension → Fact relationships |
| Unidirectional vs. Bidirectional | Cross-filtering behavior control |
| Many-to-many | Complex relationships |
| Star schema preparation | Fact vs. dimension identification before loading |

---

## 📊 DAX — Calculated Columns vs. Measures

| Type | Example | Use Case |
|------|---------|----------|
| **Calculated Column** | `WEEKDAY([Date])` | Row-by-row classification |
| **Calculated Column** | `IF([Quantity] > threshold, "Big", "Small")` | Categorization |
| **Calculated Column** | Age bands (18-25, 26-35, etc.) | Demographic grouping |
| **Measure** | `SUMX(Sales, [Quantity] * [Price])` | Row-by-row aggregation |
| **Measure** | Dynamic KPI | User-interactive metrics |
| **Conditional Formatting** | Color scales, rule-based | Table visual enhancement |

---

## 📈 Dashboard Visuals Created

| Visual Type | Purpose |
|-------------|---------|
| Histogram | Distribution of continuous variables (salary, age, time spent) |
| Grouped bar chart | Compare categories across dimensions |
| Cards | Key KPIs (response count, average salary) |
| Slicers | Interactive filtering by role, industry, salary band |
| Table | Detailed response view with conditional formatting |

---

## 🔄 Complete Pipeline Summary

```
Raw Survey (Excel)
        ↓
[Power Query - Profiling] → Column Quality / Distribution / Profile
        ↓
[Power Query - Cleansing] → Remove empty columns (5) + Filter rows
        ↓
[Power Query - Categorization] → Merge with 6 lookup tables + Conditional column
        ↓
[Power Query - Salary Parsing] → Split Column + Replace Values + Custom Column (mean)
        ↓
[Load to Power BI Model]
        ↓
[DAX] → Calculated columns (WEEKDAY, IF, age bands) + Measures (SUMX)
        ↓
[Report] → Histograms, bar charts, cards, slicers, conditional formatting
```

---

## 💡 Key Skills Demonstrated

| Skill | Application |
|-------|-------------|
| Data profiling | Column Quality/Distribution/Profile before any transformation |
| Lookup table pattern | 6 Excel-based mappings + Power Query Merge for standardization |
| Text parsing | Split Column (Digit to Non-Digit) + Replace Values for salary ranges |
| Pragmatic approximation | Mean-of-band for salaries when exact values unavailable |
| Relationship design | 1-to-many, bidirectional, many-to-many relationships |
| DAX calculated columns | WEEKDAY, IF, age bands, order categorization |
| DAX measures | SUMX vs. SUM — performance implications understood |
| Conditional formatting | Table visuals with color scales and rules |
| End-to-end ownership | Raw survey → deployed dashboard — complete pipeline |

---

## 📁 Project Artifacts

| File | Purpose |
|------|---------|
| Survey raw data | 600–700 responses, 20+ questions |
| Lookup tables (6) | Role, Industry, Language, Priority, Country, Ethnicity mappings |
| Power BI report (.pbix) | Final dashboard with all transformations embedded |

---

*This project demonstrates that effective Power BI work is 80% data preparation — and that Power Query, when mastered, can handle complex real-world survey data without external tools.*
```