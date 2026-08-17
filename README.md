# 📊 Customer Churn Analysis

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![SQLite](https://img.shields.io/badge/Database-SQLite3-003B57?style=flat&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![Pandas](https://img.shields.io/badge/Data%20Analysis-Pandas-150458?style=flat&logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Seaborn](https://img.shields.io/badge/Visualization-Seaborn-3776AB?style=flat)](https://seaborn.pydata.org/)

An end-to-end data analytics and feature engineering project built on customer demographics, subscription details, and support logs stored in an SQLite relational database (`customer_churn.db`). 

This project cleans messy relational data, handles duplicate customer support logs without row explosion, engineers predictive risk metrics, computes core SaaS financial/operational KPIs, and visualizes churn drivers using Seaborn and Matplotlib.

---

## 🛠️ Technology Stack

* **Database**: SQLite3 (`customer_churn.db`)
* **Data Processing & Manipulation**: `pandas`, `numpy`
* **Data Visualization**: `matplotlib`, `seaborn`
* **Environment**: Jupyter Notebook / Python 3.10

---

## 📂 Project Structure

```text
.
├── customer_churn.db             # SQLite source database (db_customer, db_subscription, db_support)
├── Customer_Churn_Analysis.ipynb # Primary analysis & visualization notebook
├── exported_churn_data.csv       # Cleaned & merged master output dataset
└── README.md                     # Repository documentation
```

---

## 🔄 Data Pipeline & System Architecture

```text
┌──────────────────────────────────────────────────────────┐
│                   SQLite Database                        │
│ ┌──────────────────┐ ┌────────────────┐ ┌──────────────┐ │
│ │  db_customer     │ │db_subscription │ │  db_support  │ │
│ └─────────┬────────┘ └───────┬────────┘ └──────┬───────┘ │
└───────────┼──────────────────┼─────────────────┼─────────┘
            │                  │                 │
            ▼                  ▼                 ▼
┌──────────────────────────────────────────────────────────┐
│                     Data Cleaning                        │
│ • Column renaming & missing value imputation             │
│ • Gender standardization ('Men'/'Women' ➔ 'Male'/'Female')│
│ • Date parsing (`dob`, dates to `datetime64`)            │
│ • Unused column removal (`interests`, `pincode`, etc.)   │
└───────────────────────────┬──────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│                  Feature Engineering                     │
│ • `churn_flag` generation from `cancellation_date`      │
│ • Deduplication & calculation of `complaint_count`       │
│ • Calculation of `tenure_days`                           │
│ • Discretization of `churn_score` into `churn_risk`      │
└───────────────────────────┬──────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│                 Data Merging & Export                    │
│ • Left-join on `customerid` to create master dataframe    │
│ • Export to `exported_churn_data.csv`                    │
└───────────────────────────┬──────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│                Exploratory Data Analysis                 │
│ • KPI metrics computation (Churn %, ARPU, Revenue Risk)  │
│ • Statistical modeling & correlation analysis            │
│ • Seaborn visualization (Heatmaps, Pairplots, Catplots)  │
└───────────────────────────┘
```

---

## 🧹 Data Cleaning & Feature Engineering Highlights

1. **Relational Data Cleaning**:
   * Standardized categorical variables (`gender`: `Men`/`Women` $
ightarrow$ `Male`/`Female`).
   * Imputed missing geographic values using state-level mapping heuristics.
   * Dropped uninformative columns (`interests`, `pincode`, `col_1`, `comment`).
2. **Handling Row Explosion on Merges**:
   * Customer support logs contained multiple complaints per customer (`1:N` relationship).
   * Aggregated `complaint_count` per customer and deduplicated support records prior to merging, keeping a strict `1:1` primary key relationship (maintaining 21 distinct customer records).
3. **Engineered Features**:
   * **`churn_flag`**: Binary flag derived from non-null `cancellation_date`.
   * **`tenure_days`**: Total active duration (calculated from subscription start date to cancellation date or today's date).
   * **`churn_risk`**: Binned categorical risk buckets (`low` < 50, `med` 50–69, `high` $\ge$ 70) derived from `churn_score`.

---

## 📊 Core Business KPIs

| Metric | Value | Description |
| :--- | :--- | :--- |
| **Total Customer Base** | **21** | Total unique users evaluated |
| **Churn Rate** | **28.57%** | Share of accounts canceled |
| **Retention Rate** | **71.43%** | Active user retention rate |
| **ARPU** | **$18.85** | Average Revenue Per User (monthly) |
| **Average Tenure** | **1,517.29 Days** | Lifespan across active/churned accounts (~4.1 years) |
| **Revenue at Risk** | **$73.94** | Direct monthly revenue lost from churned accounts |
| **Escalation Rate** | **19.00%** | Support complaints escalated to higher tiers |
| **Avg Complaints / User** | **0.43** | Total complaint volume divided by user base |

---

## 📌 Key Insights & Segment Analysis

### 1. Churn Rate by Plan Type
* **Basic Plan**: **60.00%** Churn Rate (5 Customers | $52.95 Total Revenue)
* **Standard Plan**: **22.22%** Churn Rate (9 Customers | $123.91 Total Revenue)
* **Premium Plan**: **14.29%** Churn Rate (7 Customers | $218.93 Total Revenue)

### 2. Geographic & Support Trends
* **High-Risk Regions**: **Karnataka** and **Meghalaya** exhibited elevated churn rates.
* **Support Escalations**: Support ticket escalations (`escalations == 'Y'`) show a direct positive correlation with account cancellations (`churn_flag`).

---

## ⚡ Quickstart

1. **Clone the repository**:
   ```bash
   git clone https://github.com/roshansutihar/customer-churn-analysis.git
   cd customer-churn-analysis
   ```

2. **Install required dependencies**:
   ```bash
   pip install pandas numpy matplotlib seaborn
   ```

3. **Run the Jupyter Notebook**:
   ```bash
   jupyter notebook Customer_Churn_Analysis.ipynb
   ```

---

## 💡 Recommendations

1. **Re-evaluate Basic Tier Value**: Address the 60% churn rate on Basic plans by improving onboarding and initial product adoption.
2. **Automate Escalation Interventions**: Implement automated customer success check-ins immediately when a support ticket is marked as escalated.
3. **Targeted Annual Upgrades**: Promote annual subscription conversions to decrease monthly contract churn volatility.
