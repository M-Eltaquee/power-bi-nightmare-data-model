# 🧩 Data Modeling Project: Messy Dataset to Star Schema – Power BI

### 🔗 [My LinkedIn](https://www.linkedin.com/in/saleh2023/)
### 📥 [Download the PBIX File](https://github.com/M-Eltaquee/power-bi-nightmare-data-model/blob/main/dataset/Dashboard.pbix?raw=true)
**📄 [Full Documentation](https://github.com/M-Eltaquee/power-bi-nightmare-data-model/edit/main/PROJECT-DOCUMENTATION.html)**

<br>
<div align="center">
  <img src="assets/preview.gif?raw=true" alt="Final Star Schema" width="1000">
</div>

---

## 📝 Introduction
<details>
  <summary><strong>📌 Overview (click)</strong></summary>

### **Overview**
> This project takes a deliberately unstructured retail distribution dataset, duplicate tables, fact tables split by year, unlabeled columns, no defined keys, and rebuilds it into a clean, working star schema. The focus is the data modeling work itself: identifying grain, separating facts from dimensions, and resolving ambiguous relationships before any reporting happens on top of it.

</details>

<details>
  <summary><strong>📂 Data Sources (click)</strong></summary>

### **Data Sources**
> Simulated multi-source retail distribution dataset (Excel workbook), structured to reflect a real, unmanaged operational export.

**▼ 📑 Dataset Explanation**
1. **Orders**, split across `ORDERS_2025` and `ORDERS_2026`, no shared key structure
2. **Customer records**, spread across `CUST_MASTER`, `Address`, `customer_contacts`, `cities`, joined loosely with no clear grain
3. **Shipments and Payments**, including a duplicate table (`Sheet1`) covering the same data as `shipments`
4. **Inventory**, monthly stock levels stored wide, columns unlabeled (`Column1` to `Column13`)
5. **Campaign log**, spend, impressions, and clicks with no relational structure applied

</details>

---

## 🎯 Case Study
The dataset arrived the way most real exports do before anyone owns the data model, duplicated tables, split fact tables, and raw codes instead of readable fields. Before any dashboard could be trusted, the model itself needed to be rebuilt so that:
- Sales, orders, campaigns, and inventory each sit at a clear, confirmed grain
- Customers, products, and geography are resolved into single, clean dimension tables
- No fact table connects directly to another fact table
- Every column left in the model earns its place

---

## 📊 Main KPIs
Used to validate the rebuilt model, not the focus of the project:
- **💰 Total Sales**
- **🎯 Sales vs Target %**
- **📦 Order Count / Total Quantity**
- **📣 Campaign Spend vs Impressions**
- **👥 Active / Inactive Customers**

---

## ⚙️ Process
1. Reviewed every raw table individually and confirmed its grain before touching it
2. Merged `ORDERS_2025` and `ORDERS_2026` into a single sales fact table, filtered by date instead of split by year
3. Consolidated customer records from four separate tables into one clean `dim_customers` table
4. Unpivoted the wide monthly inventory sheet into a proper fact table at date/product grain
5. Built a manual lookup table to convert raw order-channel codes into readable values, folded into a junk dimension with status and priority
6. Removed 10 auto-generated hidden date tables caused by Power BI's Auto Date/Time setting, related every date column to one calendar table instead, using `USERELATIONSHIP` for the alternate date paths
7. Applied row-level security by region
8. Validated every measure in a plain table visual before adding it to any chart
9. Organized all 35 Power Query steps into a staged folder structure (Stage → Dimensions → Facts → Support) instead of leaving them flat

---

## 📐 Data Model

**Before**
<img src="assets/3%20initial%20shcema%20-%20screenshot.png?raw=true" width="1000">

**After**
<img src="assets/4%20final%20shcema%20-%20screenshot.png?raw=true" width="1000">

---

## 🗂️ Query Organization
<img src="assets/POWER%20query.png?raw=true" width="500">

35 queries organized into a staged pipeline: **01_Stage** (23) → **02_Dimensions** (5) → **03_Facts** (6) → **4_Support**, so the transformation logic is traceable from raw source to final table.

---

## 📈 Validation Dashboard
<img src="assets/6%20dashboard%20%20-%20screenshot.png?raw=true" width="1000">
<img src="assets/6%20dashboard%20visiuals%20%20-%20screenshot.png?raw=true" width="1000">
<img src="assets/7%20dashboard%20KPIs%20-%20screenshot.png?raw=true" width="1000">

---

## 🎥 Project Demo
<img src="assets/preview.gif?raw=true" width="1000">

---

## 🔍 Key Insights
1. Black Friday recorded the highest campaign spend but the lowest impressions of all six campaigns, the weakest spend-to-reach ratio in the portfolio
2. Spring Launch generated impressions well above its spend level, the most cost-efficient campaign for reach
3. 21.67% of the customer base shows no recorded activity in the current data window

---

## 💡 Conclusion
Two known items were identified during review and left open rather than fixed silently, the target-to-date matching may not resolve against every calendar day depending on source grain, and one fact table's customer link relies on a name match rather than a stable ID. Both are documented here as next-iteration items, not hidden.

This project reflects the same discipline used in restructuring the Sheaffer/William Penn dealer network model, a broken structure has to be diagnosed and rebuilt correctly before any number coming out of it can be trusted.

---

## 🧰 Tools Used
- **Power BI Desktop**
- **Power Query**
- **DAX** (including `USERELATIONSHIP` for role-playing date dimensions)
- **Data Modelling** (Star Schema, Junk Dimensions, Row-Level Security)

---

## 📁 Project Structure

```bash
power-bi-nightmare-data-model/
│
├── assets/
│   ├── 3 initial shcema - screenshot.png
│   ├── 4 final shcema - screenshot.png
│   ├── 6 dashboard  - screenshot.png
│   ├── 6 dashboard visiuals  - screenshot.png
│   ├── 7 dashboard KPIs - screenshot.png
│   ├── POWER query.png
│   └── preview.gif
│
├── dataset/
│   ├── 0 dataset.xlsx
│   └── Dashboard.pbix
│
└── README.md
```
