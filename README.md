# Sales Dashboard Implementation Guide

## Project Overview
This project involves designing and implementing a sales dashboard for the sales team using **Google BigQuery** and **Looker Studio**. The dataset used (`deals.csv`) is stored in a **Google Cloud Storage (GCS) bucket** and contains information on deals, account managers, buyers, and suppliers. The dashboard includes:
- **Account Manager Performance Analysis**
- **Revenue Reporting from Buyer Companies**

---
## Data Processing & Analysis
### **1. Uploading Data to BigQuery**
- Ensure `deals.csv` is stored in a **Google Cloud Storage (GCS) bucket**.
- Load the dataset into **Google BigQuery** using the following SQL command:

```sql
LOAD DATA INTO `your_project.your_dataset.deals`
FROM FILES (
  format = 'CSV',
  uris = ['gs://your-bucket-name/deals.csv']
)
OPTIONS (
  skip_leading_rows = 1, -- Skip header row
  autodetect = TRUE -- Auto-detect schema
);
```

### **2. Account Manager Performance Calculation**
#### **Formula:**
- If an account manager is on **both** buyer and supplier sides: **100%** share.
- If an account manager is on **only one** side: **50%** share.

#### **SQL Query in BigQuery**
```sql
SELECT 
    account_manager,
    SUM(
        CASE 
            WHEN buyer_company IS NOT NULL AND supplier_company IS NOT NULL THEN revenue
            ELSE revenue / 2 
        END
    ) AS adjusted_revenue
FROM `your_project.your_dataset.deals`
GROUP BY account_manager;
```

---
## Revenue Reporting from Buyer Companies
This report aggregates revenue from deals where companies are buyers and provides insights into revenue trends.

#### **SQL Query in BigQuery**
```sql
SELECT 
    buyer_company, 
    SUM(revenue) AS total_revenue,
    DATE_TRUNC(deal_date, MONTH) AS month
FROM `your_project.your_dataset.deals`
GROUP BY buyer_company, month
ORDER BY month;
```

---
## Dashboard Setup in Looker Studio
### **1. Connecting Data**
- Open **Looker Studio**.
- Select **BigQuery** as the data source.
- Choose the appropriate dataset (`your_project.your_dataset.deals`).

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
- Automate data refresh using **Cloud Scheduler & BigQuery scheduled queries**.
- Validate insights with sales team feedback.
- Optimize Looker Studio performance by refining queries.

---
## Conclusion
This guide provides step-by-step instructions for setting up a sales performance dashboard using **Google BigQuery** and **Looker Studio**. By analyzing account manager contributions and buyer revenue trends, the sales team can make data-driven decisions effectively.

