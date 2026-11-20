# IKEA-Customer-Segmentation-RFM-Analysis
End-to-end Customer Segmentation &amp; RFM Analysis solution featuring data preprocessing, SQL &amp; python-based data cleaning business intelligence, interactive Power BI reporting and a responsive dashboard built using Excel, Python, SQL Server, PowerBI.
# 🛒 IKEA Gifting: Customer Analytics & RFM Segmentation

An end-to-end customer analytics project that segments IKEA Gifting's online customers by value and churn risk, and presents the results in an interactive Power BI dashboard. The workflow runs from SQL data cleaning through RFM modelling to a three-page dashboard, and is documented against a formal Business Requirements Document.

---

## 📑 Table of Contents

- [🎯 Business Problem](#-business-problem)
- [🏁 Objectives](#-objectives)
- [📊 Key Results](#-key-results)
- [🏗️ Architecture](#️-architecture)
- [📁 Repository Structure](#-repository-structure)
- [🗂️ Data Source](#️-data-source)
- [⚙️ Methodology](#️-methodology)
- [🚀 Getting Started](#-getting-started)
- [📈 Dashboard](#-dashboard)
- [📚 Documentation](#-documentation)
- [📄 License](#-license)

---

## 🎯 Business Problem

IKEA Gifting is a UK-based, non-store online retailer of gift ware and home decor. Its customer base mixes individual shoppers with wholesale buyers, and leadership had no structured view of which customers drive revenue or which are disengaging. Retention and marketing were reactive, and spend was not prioritised toward the customers most likely to respond.

## 🏁 Objectives

1. 💰 Identify the most valuable customer segments and their revenue contribution.
2. ⏳ Quantify churn risk using a fixed 90-day inactivity threshold.
3. 📆 Show how value and retention trend over time and differ across markets.
4. ✅ Recommend specific actions for each customer segment.

## 📊 Key Results

Figures are based on 5,881 identified customers and £17.37M in positive-quantity sales, December 2009 to December 2011.

| Segment | Customers | Share of customers | Share of revenue | Churned (>90 days) |
|---|---:|---:|---:|---:|
| 🏆 Champions | 1,291 | 22.0% | 68.1% | 0 |
| 💙 Loyal | 1,416 | 24.1% | 15.5% | 446 |
| ⚠️ At Risk | 821 | 14.0% | 9.1% | 821 |
| 🌱 New | 442 | 7.5% | 2.3% | 0 |
| 🔔 Needs Attention | 387 | 6.6% | 1.2% | 200 |
| 💤 Lost | 1,524 | 25.9% | 3.8% | 1,524 |

- 💎 The top 10% of customers account for 64% of revenue.
- 🚨 50.9% of customers (2,991) are inactive for more than 90 days, representing 19.5% of historical revenue.
- 📉 January to November 2011 revenue was flat (−0.1%) against the same period in 2010.
- 🇬🇧 The UK accounts for 82.8% of revenue.

## 🏗️ Architecture

```
📥 Raw Online Retail 1 & 2 (two sheets)
        │
        ▼
🗄️  1. SQL Server ── SQL_Data_Cleaning.sql
        │            combine, type-convert, validate, deduplicate
        ▼
📤  2. CSV export ── Retail_Cleaned.csv (1,033,036 rows)
        │
        ▼
🔁  3. RFM segmentation ── IKEA_RFM_Segmentation.ipynb
        │            filter, RFM metrics, scoring, segments, churn flag
        ▼
📗  4. Excel export ── IKEA_Ecommerce_Dashboard_Data.xlsx (779,495 rows)
        │
        ▼
📊  5. Power BI ── ikea_dashboard.pbix (Home, Overview, RFM Analysis)
```

## 📁 Repository Structure

```
📦 ikea-gifting-analytics
├── 📄 README.md
├── 📘 docs/
│   └── IKEA_Gifting_BRD.docx                  # Business Requirements Document
├── 📝 reports/
│   └── IKEA_Gifting_Operations_Report.docx    # Full project report
├── 🎤 presentation/
│   └── IKEA_Gifting_Presentation.pptx         # Stakeholder presentation
├── 🗄️ sql/
│   └── SQL_Data_Cleaning.sql
├── 🔁 notebooks/
│   └── IKEA_RFM_Segmentation.ipynb
├── 💾 data/
│   ├── raw/                                   # 🚫 Not tracked; see Data Source
│   ├── processed/
│   │   └── Retail_Cleaned.csv
│   └── output/
│       └── IKEA_Ecommerce_Dashboard_Data.xlsx
└── 📊 dashboard/
    └── ikea_dashboard.pbix
```

## 🗂️ Data Source

📚 **Online Retail I & II** are the raw dataset contains 1,067,371 transaction rows across two sheets (2009–2010 and 2010–2011) with invoices, stock codes, descriptions, quantities, unit prices, customer IDs, and countries.

🔒 The raw file is redistributed in this repository. Download it from this repository and place it in `data/raw/` before running the pipeline.

## ⚙️ Methodology

### 🧹 1. SQL cleaning
- 🔗 Combines both raw sheets into a single table with a `SourceSheet` tag.
- 🔄 Converts dates and prices with `TRY_CONVERT`, so malformed values become `NULL` rather than failing the load.
- ✔️ Runs validation checks: null dates and prices, missing customer IDs, non-positive quantities, missing descriptions, and exact duplicates.
- 🧽 Removes exact duplicates with `ROW_NUMBER()` and writes `dbo.RetailCleaned`.

### 📤 2. Export
💾 The cleaned table is exported to CSV.

🧼 3. Python data cleaning
📥 Loads the CSV and assigns column names, since the export has no header row.
📅 Converts InvoiceDate to a datetime type.
🔍 Removes rows with no customer ID, which cannot be attributed in customer-level analysis.
➖ Removes rows with zero or negative quantity, which represent returns and cancellations.
💷 Adds derived fields: Revenue (quantity × price) and InvoiceMonth.

### 🔁 4. RFM segmentation
- 🔍 **Filter:** keeps rows with a customer ID and positive quantity, leaving 779,495 transactions for 5,881 customers.
- 📅 **Recency:** days from each customer's last purchase to a snapshot date of 10 December 2011.
- 🔢 **Frequency:** count of distinct invoices.
- 💷 **Monetary:** sum of quantity × unit price.
- 🎚️ **Scoring:** quintile scores (1–5). Recency is reversed so that higher is better; frequency is ranked before scoring to handle ties.
- 🏷️ **Segments:** rule-based assignment into Champions, Loyal, At Risk, New, Needs Attention, and Lost.
- 🚩 **Churn flag:** `IsChurned = Recency > 90`.

### 📗 5. Excel export
🔗 Transaction-level rows are merged with customer-level RFM metrics for use in Power BI. Customer metrics repeat on each transaction row, so customer counts must use distinct `Customer_ID` values.

### 📊 6. Dashboard
🖥️ A three-page Power BI report:
- 🏠 **Home:** navigation.
- 📈 **Overview:** headline KPIs, slicers, revenue and customer trends, and retention by segment.
- 🔬 **RFM Analysis:** segment and country performance, and average customer value.

## 🚀 Getting Started

### ✅ Prerequisites
- 🗄️ SQL Server (2016 or later) and SQL Server Management Studio, or Azure Data Studio
- 📊 Power BI Desktop (for the dashboard)

### ▶️ Running the pipeline

**1. 📥 Load the raw data into SQL Server.** Import the two raw sheets as `dbo.online_retail_page_1` and `dbo.online_retail_page_2`.

**2. 🧹 Run the cleaning script.**

```sql
-- Execute sql/SQL_Data_Cleaning.sql in SSMS or Azure Data Studio.
-- Output: dbo.RetailCleaned
```

**3. 📤 Export `dbo.RetailCleaned` to CSV** and save it as `data/processed/Retail_Cleaned.csv`.

**4. 🔁 Run the segmentation notebook** in `notebooks/IKEA_RFM_Segmentation.ipynb`. It writes `IKEA_Ecommerce_Dashboard_Data.xlsx`. Update the file paths in the first two cells if your folder layout differs.

**5. 📊 Open the dashboard.** Open `dashboard/ikea_dashboard.pbix` in Power BI Desktop and point the data source to the Excel file.

> ⚠️ **Note:** The export writes a headerless CSV. The segmentation step assigns column names on load, so the column order must match the SQL `SELECT` list.

## 📈 Dashboard

🖼️ The dashboard is in `dashboard/ikea_dashboardnew.pbix`. Pages are:

1. 🏠 **Home Page:** entry point with navigation buttons.
2. 📈 **Overview:** KPI cards, six slicers, monthly trend, and revenue and customer retention by segment.
3. 🔬 **RFM Analysis:** segment and country summaries, and average customer value by segment and market.

## 📚 Documentation

- 📘 [Business Requirements Document].
- 📝 [Operations Report].
- 🎤 [Presentation].

## 📄 License

📜 Source code is released under the MIT License. The underlying Online Retail II dataset is subject to the terms of the UCI Machine Learning Repository.

---

*🏢 Prepared for the IKEA Gifting Customer Insights & Analytics Division. Internal use.*
