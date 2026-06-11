# 📊 SalesPulse — Executive Sales Analytics Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-44%20Measures-blue?style=for-the-badge)
![Data](https://img.shields.io/badge/Data-Global%20Superstore-orange?style=for-the-badge)
![Records](https://img.shields.io/badge/Records-9%2C994%20rows-red?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

> A single-page executive dashboard built in Power BI Desktop using the Global Superstore dataset. Covers sales performance, profitability, customer analysis, regional breakdown, and time intelligence — all in one interactive view.

---

## 🖼️ Dashboard Preview

![SalesPulse Dashboard](https://raw.githubusercontent.com/venkatraman0400-blip/SalesPulse-PowerBI-Dashboard/main/assets/dashboard_preview.png)

---

## 📌 Key Metrics

| Metric | Value |
|--------|-------|
| 💰 Total Sales | $12.63M |
| 📈 Total Profit | $1.46M |
| 🎯 Profit Margin | 11.60% |
| 🛒 Total Orders | 25,035 |
| 👥 Total Customers | 4,873 |
| 🏷️ Avg Discount | 8.14% |
| 📅 Period | 2011 – 2014 |
| 🌍 Regions | 13 global regions |

---

## 📊 Visuals Included

| # | Visual | Type | Key Insight |
|---|--------|------|-------------|
| 1 | KPI Cards (×6) | Card | Headline metrics with YoY indicators |
| 2 | Sales Trend Over Time | Line Chart | Monthly Sales vs Sales PY |
| 3 | Sales by Region | Donut Chart | West leads at 32.7% |
| 4 | Sales by Category | Bar Chart | Technology leads at $4.74M |
| 5 | Sales by Segment | Donut Chart | Consumer = 51.4% of sales |
| 6 | Top 10 Sub-Category | Bar Chart | Phones #1 · **Tables = -$64K profit ⚠️** |
| 7 | Monthly Sales Heatmap | Matrix | Nov 2014 = $555K peak month |
| 8 | Sales vs Profit Scatter | Scatter | Category profitability analysis |
| 9 | Top 5 Customers | Table | Sales · Profit · Margin per customer |
| 10 | Sales by Year | Bar Chart | 90% growth from 2011 → 2014 |
| 11 | Interactive Slicers | Button Slicer | Year · Region · Category · Segment · Ship Mode |

---

## 🧠 DAX Measures (44 Total)

### Core KPIs
```dax
Total Sales       = SUM(fact_sales[sales])
Total Profit      = SUM(fact_sales[profit])
Profit Margin %   = DIVIDE([Total Profit], [Total Sales], 0)
Total Orders      = DISTINCTCOUNT(fact_sales[order_id])
Total Customers   = DISTINCTCOUNT(fact_sales[customer_id])
Avg Discount      = AVERAGE(fact_sales[discount])
Total Quantity    = SUM(fact_sales[quantity])
```

### Time Intelligence
```dax
Sales PY =
    CALCULATE([Total Sales], DATEADD(dim_date[Date], -1, YEAR))

Sales YoY % =
VAR CurrentSales = [Total Sales]
VAR PrevSales    = [Sales PY]
RETURN
    IF(
        ISBLANK(CurrentSales) || ISBLANK(PrevSales),
        BLANK(),
        DIVIDE(CurrentSales - PrevSales, PrevSales, BLANK())
    )

Sales YTD        = CALCULATE([Total Sales], DATESYTD(dim_date[Date]))
Sales Last 30 Days = CALCULATE([Total Sales], DATESINPERIOD(dim_date[Date], LASTDATE(dim_date[Date]), -30, DAY))
```

### Segment & Regional Breakdowns
```dax
Sales - Consumer     = CALCULATE([Total Sales], fact_sales[segment] = "Consumer")
Sales - Corporate    = CALCULATE([Total Sales], fact_sales[segment] = "Corporate")
Sales - Home Office  = CALCULATE([Total Sales], fact_sales[segment] = "Home Office")
Sales - West         = CALCULATE([Total Sales], fact_sales[region] = "West")
Profit - Central     = CALCULATE([Total Profit], fact_sales[region] = "Central")
Sales % of Total     = DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(fact_sales)), 0)
```

---

## 🗄️ Data Model — Star Schema

```
                    ┌─────────────┐
                    │  dim_date   │
                    │  Date (PK)  │
                    └──────┬──────┘
                           │ 1:M (Active)
        ┌──────────────────▼──────────────────────┐
        │               fact_sales                 │
        │  order_id · sales · profit · discount    │
        │  order_date · ship_date · quantity       │
        │  category · region · segment · ship_mode │
        └───────┬────────────┬──────────┬──────────┘
                │            │          │
             1:M│         1:M│       1:M│
        ┌───────▼──┐  ┌──────▼──┐  ┌───▼───────┐
        │dim_region│  │dim_categ│  │ kpi_yoy   │
        │region(PK)│  │ory (PK) │  │order_year │
        └──────────┘  └─────────┘  └───────────┘
```

**4 Active Relationships:**
- `fact_sales[order_date]` → `dim_date[Date]` *(Many-to-One)*
- `fact_sales[region]` → `dim_region[region]` *(Many-to-One)*
- `fact_sales[category]` → `dim_category[category]` *(Many-to-One)*
- `fact_sales[order_year]` → `kpi_yoy[order_year]` *(Many-to-One)*

---

## 🔑 Key Business Insights

> 1. 📈 **Sales grew 90.6%** in 4 years — from $2.26M (2011) to $4.30M (2014)
> 2. 🏆 **Technology** is the top revenue category — $4.74M (37.5% of total)
> 3. ⚠️ **Tables sub-category loses money** — $757K sales but **-$64K profit** (must investigate)
> 4. 🌍 **Central region** leads profit — $311K (21.3% of total profit)
> 5. 👤 **Consumer segment** dominates — 51.4% of all sales ($6.50M)
> 6. 📅 **Nov & Dec are always peak months** — clear Q4 seasonality pattern
> 7. 💸 **Southeast Asia** has the weakest margin — only 2% despite $883K in sales
> 8. 🧑‍💼 **Top customer Tom Ashbrook** — $40,489 in sales, 15.59% margin

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| **Power BI Desktop** | Dashboard development & visualisation |
| **DAX** | 44 custom measures for KPIs & time intelligence |
| **Power Query (M)** | Data transformation & cleaning |
| **Global Superstore** | Dataset source (9,994 rows · 21 columns) |
| **Star Schema** | Data model architecture |

---

## 📁 Project Structure

```
SalesPulse-PowerBI-Dashboard/
│
├── 📊 superstore_analytics_2026.pbix   ← Main Power BI file (open with Power BI Desktop)
├── 📄 README.md                         ← Project documentation
└── 🔧 .gitignore
```

---

## 🚀 How to Open

1. **Download** `superstore_analytics_2026.pbix`
2. Open with **Power BI Desktop** — free download at [powerbi.microsoft.com/desktop](https://powerbi.microsoft.com/desktop)
3. All data is embedded — **no external connections required**
4. All 44 measures and relationships are pre-built and ready

---

## 👨‍💻 About

**Venkatraman R** — Data Science & AI Engineer
📍 Chennai, India | Boston Institute of Analytics, 2026

[![Portfolio](https://img.shields.io/badge/Portfolio-Visit-blue?style=flat-square&logo=github)](https://venkatraman0400-blip.github.io/venkatraman-portfolio)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat-square&logo=linkedin)](https://linkedin.com/in/venkatraman0400)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat-square&logo=github)](https://github.com/venkatraman0400-blip)

---

*Part of my Data Science & AI portfolio — built from scratch using Power BI Desktop, DAX, and the Global Superstore dataset.*
