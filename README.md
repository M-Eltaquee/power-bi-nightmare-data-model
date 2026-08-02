# 🧩 Power BI Data Modeling: From Nightmare Dataset to Star Schema

### 🔗 [My LinkedIn](https://www.linkedin.com/in/saleh2023/)
### 📥 [Download the PBIX File](https://github.com/M-Eltaquee/power-bi-nightmare-data-model/blob/main/Dashboard.pbix?raw=true)

<br>
<div align="center">
  <img src="assets/final-schema.png" alt="Final Star Schema" width="1000">
</div>

---

## 📝 Introduction

<details>
  <summary><strong>📌 Overview (click)</strong></summary>

### **Overview**
> Most Power BI projects start with clean data. This one didn't, on purpose. The dataset arrived as a scattered collection of duplicate tables, split-year fact tables, unlabeled columns, and no defined keys, deliberately built to simulate what a real, unmanaged export from a growing distribution business looks like before anyone touches it.
>
> The focus of this project is not the dashboard. It is the discipline of turning that mess into a working star schema, table by table, relationship by relationship, with every decision traceable back to a reason.

</details>

<details>
  <summary><strong>📂 Data Sources (click)</strong></summary>

### **Data Sources**
> Simulated multi-source retail distribution dataset (Excel workbook, multiple sheets), structured to mimic real operational exports: order tables split by year, separate shipment and payment logs, a customer master file joined across three supporting tables, unpivoted monthly inventory snapshots, and a campaign log with no relational structure applied.

</details>

---

## 🎯 The Problem

<div align="center">
  <img src="assets/initial-schema.png" alt="Initial Messy Schema" width="1000">
</div>

The raw model, before any structural work, had no usable relationships and several structural issues typical of an unmanaged data export:

- **Duplicate tables covering the same entity** (`Sheet1` and `shipments` held identical structures)
- **Fact tables split by year** (`ORDERS_2025`, `ORDERS_2026`) instead of one table filtered by date
- **No defined keys** between related tables, Power BI could not infer how `CUST_MASTER`, `Address`, `customer_contacts`, and `cities` connected to one another
- **Unlabeled columns** in `inventory` (`Column1` through `Column13`), monthly stock figures with no readable structure
- **Raw numeric codes with no lookup**, order channel, status, and priority stored only as codes

None of this is queryable or reportable as-is. Step one was understanding the grain of every single table before writing a single transformation.

---

## 🔧 The Fix

<div align="center">
  <img src="assets/final-schema.png" alt="Final Star Schema" width="1000">
</div>

Rebuilt from the ground up as a clean star schema, following the same phased approach used in real BI implementations:

**1. Prepare**
Explored every raw table individually, stated the grain out loud before touching it, and classified each one as a dimension candidate, a fact candidate, or noise to be dropped.

**2. Build Dimensions**
Merged related tables into single, clean dimensions: customer records consolidated from `CUST_MASTER` + `Address` + `customer_contacts` + `cities` into one `dim_customers` table, product data merged with subcategory/category lookups into `dim_products`, and a manually built `channels` lookup table used to convert raw order-channel codes into readable names, then folded into `dim_order_flags` as a junk dimension alongside status and priority.

**3. Build Facts**
Unified `ORDERS_2025` and `ORDERS_2026` into a single `fact_sales` table filtered by date rather than split by year. Unpivoted the wide monthly inventory sheet into a proper `fact_inventory` table at date/product grain. Built `fact_order_process`, `fact_campaign`, and `fact_sales_target` at their own correct, independently confirmed grains.

**4. Polish**
Applied row-level security by region, and validated every base measure in a plain table visual before it went anywhere near a chart.

### Rules followed throughout
- Star schema only, dimensions surround facts, facts never connect to other facts
- Grain stated and confirmed before any table was touched
- Every column had to earn its place, anything not serving analysis or reporting was dropped

---

## 📁 Query Organization

<div align="center">
  <img src="assets/query-folders.png" alt="Power Query folder structure" width="500">
</div>

All 35 queries organized into a staged pipeline rather than left flat: **01_Stage** (23 raw source queries) → **02_Dimensions** (5) → **03_Facts** (6) → **4_Support**. This structure keeps the transformation logic traceable, anyone opening the file can follow exactly which raw tables feed which dimension or fact, in order.

---

## ⚙️ Model Hygiene

Beyond the base transformations, the model was cleaned up further after the initial build:

- **Removed 10 auto-generated hidden date tables.** Several fact tables carry multiple date columns (order date, ship date, delivery date, invoice date, pay date), which Power BI's Auto Date/Time setting was turning into separate hidden calendars. Disabled Auto Date/Time, related every date column to a single `dim_calender` table, and used `USERELATIONSHIP` in DAX to activate the alternate date paths only when needed, a role-playing dimension pattern.
- **Removed leftover columns** left over from merge operations that were never actually needed downstream, in keeping with the "every column earns its place" rule.

```dax
sales_by_ship_date = 
CALCULATE(
    [total_sales],
    USERELATIONSHIP(fact_order_process[ship_date], dim_calender[Date])
)
```

---

## 📊 Validation Dashboard

A light dashboard was built on top of the finished model, not as the deliverable itself, but to prove the relationships and grain actually produce correct, filterable numbers end to end.

<img src="assets/dashboard-overview.png" width="1000">

**KPIs:** Total Sales, Sales vs Target %, Order Count, Total Quantity
**Charts:** Sales vs Target by Quarter, Sales by Category, Campaign Spend vs Impressions, Customer Active/Inactive split, Monthly Sales Trend

---

## 🎥 Project Demo

<img src="assets/demo.gif" width="1000">

---

## 🔍 Key Findings

1. Black Friday recorded the highest campaign spend but the lowest impressions of all six campaigns, the weakest spend-to-reach ratio in the portfolio
2. Spring Launch generated impressions well above its spend level, the most cost-efficient campaign for reach
3. 21.67% of the customer base shows no recorded activity in the current data window

---

## ⚠️ Known Limitations

Two items were identified during review and intentionally left open rather than papered over:

- **Target-to-date matching**: `fact_sales_target` relates to `dim_calender` on exact date equality. If targets are stored at a coarser grain than daily, values may not surface against every calendar row. Not fully validated against source grain.
- **Customer linkage in `fact_order_process`**: customer ID is resolved through a name-match join rather than a stable ID join, which carries risk of unmatched rows on name variants or duplicates.

Flagged here deliberately rather than fixed silently, both are next-iteration items.

---

## 🧰 Tools Used
- **Power BI Desktop**
- **Power Query (M)**
- **DAX** (including `USERELATIONSHIP` for role-playing dimensions)
- **Data Modelling** (Star Schema, Junk Dimensions, Row-Level Security)

---

## 📁 Project Structure

```bash
power-bi-nightmare-data-model/
│
├── assets/
│   ├── initial-schema.png
│   ├── final-schema.png
│   ├── query-folders.png
│   ├── dashboard-overview.png
│   ├── demo.gif
│
├── data/
│   └── dataset.xlsx
│
├── Dashboard.pbix
│
└── README.md
```
