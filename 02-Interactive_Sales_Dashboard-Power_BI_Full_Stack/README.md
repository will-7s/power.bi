# Interactive Sales Dashboard – Power BI

End-to-end sales dashboard built with Power BI Desktop and deployed to Power BI Service.

Power BI link: https://app.powerbi.com/groups/me/reports/12024c38-4a28-4ae8-af5f-73ae8b0c9c5c/f15e7cdacf4b172fad25?ctid=fd1df4e5-2eb0-410e-a611-12513030b133&experience=power-bi&bookmarkGuid=ae973b7c-1d54-4b2c-b494-2537d5d8730a 

Drive Link: https://mega.nz/file/lBsg3bLC#XuXtPzMRfregLTgVQq7nCupzROSUB7d3kQcjX7PBtpQ
---

## 📌 Project overview

Two-page interactive report analyzing B2B tech hardware sales:

- **Sales Overview** – revenue, orders, average basket, trends, regional breakdown, product mix
- **Cancelled Orders** – cancellation count, revenue lost, rate over time

---

## 🛠️ Tools

| Tool | Use |
|------|-----|
| Power Query | ETL (data loading, normalization) |
| DAX | All KPIs and business logic |
| Power BI Service | Cloud deployment |

---

## 📁 Data source

Single CSV file (`sales.csv`) containing:
- Orders, clients, products, regions
- Quantities, prices, order status, dates

**Volume:** B2B tech hardware dataset (multiple years)

---

## ⚙️ ETL (Power Query)

1. **Data quality check** – column quality, profiling, distribution
2. **Normalization** – flat CSV split into 4 tables:
   - `sales` (fact)
   - `clients`, `products`, `regions` (dimensions)
3. **Reference table** disabled from loading (feeds dimensions without duplication)

---

## 🧩 Data model

Star schema:
- Fact: `sales`
- Dimensions: `clients`, `products`, `regions`
- All relationships: 1-to-many toward fact

---

## 📐 DAX measures

| Measure | Logic |
|---------|-------|
| `Total Sales` | SUM(sales[TotalPrice]) |
| `Total of Orders Cancelled` | CALCULATE(SUM(sales[TotalPrice]), sales[OrderStatus] = "Cancelled") |
| `Quantity Sold` | SUM(sales[Quantity]) |
| `Number of Orders Cancelled` | CALCULATE(DISTINCTCOUNT(sales[OrderID]), sales[OrderStatus] = "Cancelled") |
| `Number of Orders` | DISTINCTCOUNT(sales[OrderID]) |
| `% Amount Orders Cancelled` | DIVIDE([Total of Orders Cancelled], CALCULATE([Total Sales], ALL(sales)), 0) |
| `% Average Order` | DIVIDE([Number of Orders], CALCULATE([Number of Orders], ALL(Sales)), 0) |
 | `% Orders` | DIVIDE([Number of Orders], CALCULATE([Number of Orders], ALL(sales)),0) |
| `% Orders Cancelled` | DIVIDE([Number of Orders Cancelled], CALCULATE([Number of Orders], ALL(sales)), 0) |
| `% Orders Cancelled DAX` | DIVIDE([Number of Orders Cancelled], CALCULATE([Number of Orders], ALL(sales)), 0) |
| `% Quantity Sold` | DIVIDE([Quantity Sold], CALCULATE([Quantity Sold], ALL(sales)), 0) |
| `% Total Sales` | DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(sales)), 0) |


All measures stored in a dedicated `Measures (2)` table.

---

## 📊 Visuals

**Page 1 – Sales Monitoring**
- 4 KPI cards (turnover, orders, quantity, avg basket)... I added gauges (so others measures to be defined)
- Line chart: turonver + quantity ordered over time
- Bar chart: turnover by region
- Donut: turnover by category
- Tree map + 3 slicers (region, status, date)

**Page 2 – Cancelled Orders**
- 3 KPI cards (count, revenue lost, rate)
- Line chart: cancellation trend
- Category breakdown

---

## ⭐ Advanced features

| Feature | Implementation |
|---------|----------------|
| Custom tooltips | Hidden pages linked to visuals |
| Custom menu | To click on the icons to navigate between pages |
| Bookmarks | Mobile, Desktop, Reset (nav icons) |
| JSON theme | Custom "Loomy Lime" styling |

---

