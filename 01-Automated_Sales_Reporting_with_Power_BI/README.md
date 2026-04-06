# Power BI Sales Dashboard – ABC Company

**Power BI link : ** https://app.powerbi.com/groups/me/reports/89551e25-4cc5-45bd-aa67-6d6e28ddba02/fec13063d30d57fe7847?ctid=fd1df4e5-2eb0-410e-a611-12513030b133&experience=power-bi&bookmarkGuid=e24f28a1-4a49-4258-9b1c-ec85b75f125a

Automated sales dashboard built with Power BI and Power Query. No manual exports, no repetitive Excel work.

---

## 🎯 Objective

Transform raw, disconnected files into an interactive dashboard that answers:
- Turnover trends (month by month)
- Year-over-year comparison (2024 vs 2023)
- Analysis by products, brands, clients, region... and other filters.

---

## 🛠️ Tools used

| Tool | Purpose |
|------|---------|
| Power Query | Load and clean data (no code) |
| Power BI | Dashboard and visuals |
| DAX | Calculations (turnover, YoY) |

---

## 📁 Data sources

| File | Content |
|------|---------|
| `Produits.csv` | 100 products |
| `Clients.xlsx` | 1,000 clients |
| `Ventes/` folder | 96,000+ sales (2023 + 2024) | (in other to add files for the next years).


---

## ⚙️ What I did

1. Loaded all files with Power Query
2. Cleaned data (removed empty rows, fixed headers)
3. Added a `Turnover` column (Qty × Price)
4. Built a star schema model (linked tables that were unlinked)
5. Created a calendar table for date filtering
6. Wrote DAX measures for turnover and YoY comparison
7. Designed an interactive dashboard

---

## 📊 Dashboard features

- Treemap of turnovers for regions
- Monthly trend line (2024 vs 2023)
- Year filter (dropdown)
- Revenue table by brand / client
- Cross-filtering (click → updates all visuals)

---

## 💡 Key skills

> Power Query · Star Schema · DAX · Time Intelligence · Dashboard Design 

---

## 📎 Status

✅ Complete – ready for refresh with new data