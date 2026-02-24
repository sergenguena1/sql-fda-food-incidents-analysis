# 🔍 FDA Food Incidents Analysis — SQL Project

> Exploratory SQL analysis of **231,897 food-related adverse event reports** from the U.S. Food & Drug Administration (FDA) — identifying industry risk patterns, consumer profiles, and year-over-year trends using Google BigQuery.

---

## 🧠 Business Context

In the FMCG industry, monitoring product safety incidents is critical for commercial and supply chain teams. This project simulates the kind of analytical work done in commercial performance roles — identifying where incidents concentrate, who is affected, and how trends evolve over time.

**Dataset:** `bigquery-public-data.fda_food.food_events`
**Tool:** Google BigQuery (SQL)
**Skills demonstrated:** Aggregation, filtering, multi-column grouping, date extraction, CTEs, INNER JOIN, YoY analysis

---

## 📊 Key Findings

| Metric | Result |
|---|---|
| Total incidents analyzed | 231,897 |
| Distinct brands involved | 84,086 |
| Product categories covered | 45 |
| #1 industry by incidents | Vitamins/Minerals/Proteins (95,043) |
| #2 industry by incidents | Cosmetics (83,526) |
| Peak year for Cosmetics | 2021 (21,247 incidents) |
| Most affected consumer profile | Females aged 53–60 |
| Fastest growing category 2022→2023 | Baby Food Products (+92 incidents, +109%) |
| Largest decline 2022→2023 | Nuts/Edible Seeds (–1,710 incidents) |

---

## 🗂️ Analysis Structure — 5 Queries

### Query 1 — Global Overview
**Question:** What is the overall scale of the dataset?

```sql
SELECT
  COUNT(report_number) AS nombre_incidents,
  COUNT(DISTINCT products_brand_name) AS nombre_marques,
  COUNT(DISTINCT products_industry_name) AS nombre_categories
FROM `bigquery-public-data.fda_food.food_events`
```

**Result:** 231,897 incidents · 84,086 distinct brands · 45 product categories

---

### Query 2 — Top 10 Industries by Incident Volume
**Question:** Which food industries concentrate the most incidents?

```sql
SELECT
  products_industry_name,
  COUNT(report_number) AS nombre_incidents
FROM `bigquery-public-data.fda_food.food_events`
GROUP BY products_industry_name
ORDER BY nombre_incidents DESC
LIMIT 10
```

**Top 3 results:**
1. Vitamins/Minerals/Proteins — 95,043 incidents
2. Cosmetics — 83,526 incidents
3. Nuts/Edible Seeds — 5,760 incidents

---

### Query 3 — Temporal Evolution by Industry
**Question:** How do incidents evolve year by year per industry?

```sql
SELECT
  products_industry_name,
  EXTRACT(YEAR FROM date_created) AS annee,
  COUNT(report_number) AS nombre_incidents
FROM `bigquery-public-data.fda_food.food_events`
GROUP BY products_industry_name, annee
ORDER BY nombre_incidents DESC
```

**Key insight:** Cosmetics peaked in 2021 with 21,247 incidents — nearly 3x higher than any other year, suggesting a specific market event or reporting surge.

---

### Query 4 — Consumer Profile Analysis
**Question:** Who are the most affected consumers — by gender and age?

```sql
SELECT
  consumer_gender,
  consumer_age,
  COUNT(report_number) AS nombre_incidents
FROM `bigquery-public-data.fda_food.food_events`
WHERE consumer_gender IS NOT NULL
  AND consumer_age IS NOT NULL
GROUP BY consumer_gender, consumer_age
ORDER BY nombre_incidents DESC
LIMIT 5
```

**Key insight:** The top 5 most affected profiles are all **Female, aged 53–60** — suggesting strong concentration of incidents in cosmetics and dietary supplements targeting this demographic.

---

### Query 5 — Year-over-Year Comparison 2022 vs 2023 (CTE + JOIN)
**Question:** Which industries are growing or declining in incident volume?

```sql
WITH incidents_2022 AS (
  SELECT
    products_industry_name,
    COUNT(report_number) AS incidents_2022
  FROM `bigquery-public-data.fda_food.food_events`
  WHERE EXTRACT(YEAR FROM date_created) = 2022
  GROUP BY products_industry_name
),
incidents_2023 AS (
  SELECT
    products_industry_name,
    COUNT(report_number) AS incidents_2023
  FROM `bigquery-public-data.fda_food.food_events`
  WHERE EXTRACT(YEAR FROM date_created) = 2023
  GROUP BY products_industry_name
)
SELECT
  a.products_industry_name,
  a.incidents_2022,
  b.incidents_2023,
  b.incidents_2023 - a.incidents_2022 AS evolution
FROM incidents_2022 a
INNER JOIN incidents_2023 b
  ON a.products_industry_name = b.products_industry_name
ORDER BY evolution DESC
```

**Key insights:**
- 📈 **Baby Food Products** saw the sharpest increase: +92 incidents (+109%) — a critical signal for FMCG players in infant nutrition
- 📉 **Nuts/Edible Seeds** saw the largest decline: –1,710 incidents, suggesting improved safety controls or market contraction
- 📉 **Cereals/Breakfast Food** dropped by –617 incidents — relevant for brands like Danone operating in this category

---

## ⚙️ Technical Skills Demonstrated

| Skill | Used in |
|---|---|
| `SELECT`, `FROM`, `WHERE` | All queries |
| `COUNT()`, `COUNT(DISTINCT)` | Queries 1, 2, 3, 4, 5 |
| `GROUP BY` (single & multi-column) | Queries 2, 3, 4, 5 |
| `ORDER BY`, `LIMIT` | Queries 2, 3, 4 |
| `EXTRACT(YEAR FROM date)` | Queries 3, 5 |
| `IS NOT NULL` filtering | Query 4 |
| `CTE (WITH ... AS)` | Query 5 |
| `INNER JOIN` | Query 5 |
| YoY delta calculation | Query 5 |

---

## 🔗 Related Projects

- **[Power BI Commercial Dashboard](https://github.com/sergenguena1/powerbi-commercial-dashboard)** — Multi-page interactive BI report · €120M scope · Danone SSA
- **[Excel Commercial BI Dashboard](https://github.com/sergenguena1/excel-commercial-bi-dashboard)** — 8-tab Excel dashboard · 8 brands · 70+ distributors
- **[VBA ETL Multi-File Compiler](https://github.com/sergenguena1/excel-vba-etl-multifile-compiler)** — Automated ETL processing 70+ files in under 4 minutes

---

## 👤 Author

**Serge NGUENA** — Senior Commercial Performance & BI Analyst
[LinkedIn](https://linkedin.com/in/serge-nguena) · [GitHub Portfolio](https://github.com/sergenguena1) · Laval, QC

[![Microsoft Certified: Data Analyst Associate](https://img.shields.io/badge/Microsoft%20Certified-Data%20Analyst%20Associate-0078D4?style=flat&logo=microsoft&logoColor=white)](https://learn.microsoft.com/en-us/users/sergenguenadada-3442/credentials/ee8b077cb47b0eed)
