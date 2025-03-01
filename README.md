# Vanilla_Steel_task2
Task 2 
# Sales Dashboard Implementation Guide

## Project Overview
This project involves designing and implementing a sales dashboard for the sales team using Looker Studio and SQL. The dataset used (`deals.csv`) contains information on deals, account managers, buyers, and suppliers. The dashboard includes:
- **Account Manager Performance Analysis**
- **Revenue Reporting from Buyer Companies**

---
## Data Processing & Analysis
### **1. Understanding the Dataset**
Before designing the reports, inspect the `deals.csv` dataset to understand its structure and key columns. Ensure necessary data cleaning steps like handling missing values and formatting inconsistencies.

### **2. Account Manager Performance Calculation**
#### **Formula:**
- If an account manager is on **both** buyer and supplier sides: **100%** share.
- If an account manager is on **only one** side: **50%** share.

#### **SQL Query for Performance Calculation**
```sql
SELECT
    account_manager,
    SUM(
        CASE
            WHEN buyer_company IS NOT NULL AND supplier_company IS NOT NULL THEN revenue
            ELSE revenue / 2
        END
    ) AS adjusted_revenue
FROM deals
GROUP BY account_manager;
```

---
## Revenue Reporting from Buyer Companies
This report aggregates revenue from deals where companies are buyers and provides insights into revenue trends.

#### **SQL Query for Buyer Revenue Reporting**
```sql
SELECT
    buyer_company,
    SUM(revenue) AS total_revenue,
    DATE_TRUNC(deal_date, MONTH) AS month
FROM deals
GROUP BY buyer_company, month
ORDER BY month;
```

---
## Dashboard Setup in Looker Studio
### **1. Connecting Data**
- Upload `deals.csv` to Google Sheets or a database (BigQuery, PostgreSQL, etc.).
- Connect Looker Studio to the data source.

### **2. Creating Reports**
#### **Account Manager Performance Dashboard**
- **Metric:** Adjusted revenue per account manager.
- **Charts:** Bar chart and table view.
- **Filters:** Date range, account manager name.

#### **Buyer Revenue Report**
- **Metric:** Total revenue from buyers.
- **Charts:** Time-series graph and company-wise revenue distribution.
- **Filters:** Date range, buyer company.

---
## Next Steps
- Ensure the data pipeline refreshes automatically if using a database.
- Validate insights with sales team feedback.
- Optimize Looker Studio performance by refining queries.

---
## Conclusion
This guide provides step-by-step instructions for setting up a sales performance dashboard using Looker Studio and SQL. By analyzing account manager contributions and buyer revenue trends, the sales team can make data-driven decisions.

