# Sales Dashboard Implementation 

Task 2 requirements -
Analysis & Insights 
● Objective: Design and implement a dashboard for the sales team. 
● Tools: Looker Studio, SQL 
● Datasets: Using the deals datasets (`deals.csv`). 
● Task: create the following reports: 
○ Account Manager Performance 
■ Based on the data, understand and analyse to create relevant reports. ■ The formula to calculate each account managers share is as follows:
● If they are on the buyer and supplier side, then 100%. 
● If they are on one side than 50% 
○ Revenue Reporting from Buyer Companies 



## Project Overview
This project involves designing and implementing a sales dashboard for the sales team 
I have used **Google BigQuery** and **Looker Studio**. 

Create a project in big query so that you can start working on gcp console.

The dataset used (`deals.csv`) is stored in a **Google Cloud Storage (GCS) bucket** and contains information on deals, account managers, buyers, and suppliers. 
The bucket I have created in GCP named vanila_steel_task_2 which has all the resources and tasks data.

<img width="1435" alt="image" src="https://github.com/user-attachments/assets/f9747344-1628-4adb-95bc-4efda7320a61" />

I have fetched the data from bucket to big query.

The dashboard includes:
- **Account Manager Performance Analysis** looker studio link - https://lookerstudio.google.com/u/0/reporting/7a4bef53-914c-4588-bac2-d371c523a95e/page/tEnnC
- **Revenue Reporting from Buyer Companies** looker studion link - https://lookerstudio.google.com/u/0/reporting/415259d0-5e8b-4547-b95b-568569c69fdc/page/tEnnC

---
## Data Processing & Analysis
### **1. Upload Data to BigQuery**
- Ensured `deals.csv` is stored in a **Google Cloud Storage (GCS) bucket**.

Create a dataset named as deals and add a table as shown in the snipshot below 
<img width="1436" alt="image" src="https://github.com/user-attachments/assets/53848cf1-6ef6-4286-af89-d40cfe38b065" />

### **2. Account Manager Performance Calculation**
#### **Formula:**
- If an account manager is on **both** buyer and supplier sides: **100%** share.
- If an account manager is on **only one** side: **50%** share.

#### **SQL Query in BigQuery**
```sql
WITH am_shares AS (
    SELECT 
        deal_id,
        buyer_am, 
        supplier_am, 
        confirmed_gross_revenue,
        confirmed_gross_profit,
        -- Calculate Share Percentage
        CASE 
            WHEN buyer_am IS NOT NULL AND supplier_am IS NOT NULL AND buyer_am = supplier_am THEN 1.00  -- 100% if same AM on both sides
            ELSE 0.50  -- 50% if AM is on one side only
        END AS am_share
    FROM yash-452215.cscdcfd.dscasxd
),
am_revenue AS (
    -- Unpivot to get each Account Manager separately
    SELECT 
        buyer_am AS account_manager,
        confirmed_gross_revenue * am_share AS revenue_share
    FROM am_shares
    WHERE buyer_am IS NOT NULL

    UNION ALL

    SELECT 
        supplier_am AS account_manager,
        confirmed_gross_revenue * am_share AS revenue_share
    FROM am_shares
    WHERE supplier_am IS NOT NULL
)
-- Aggregate Total Revenue Per Account Manager
SELECT 
    account_manager,
    SUM(revenue_share) AS total_generated_revenue
FROM am_revenue
GROUP BY account_manager
ORDER BY total_generated_revenue DESC;
 
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
Buyer Revenue Report

sql

SELECT 
    buyer_company, 
    SUM(booked_gross_revenue) AS total_revenue
FROM `vanilla-steel-task-2.deals_vanila_steel.deals_vanila` 
GROUP BY buyer_company
ORDER BY total_revenue DESC;

<img width="1440" alt="image" src="https://github.com/user-attachments/assets/c5ff3d86-728c-4cd2-b163-bb9076e8a925" />




## Dashboard Setup in Looker Studio
### **1. Connecting Data to big query**
- Open **Looker Studio**.
- Select **BigQuery** as the data source.
- Choose the appropriate dataset which is (`vanilla-steel-task-2.deals_vanila_steel.deals_vanila`).

### **2. Creating Reports**
whenever you create a report you need dimension and metric use it accordinly for the data that you have to get the results

#### **Account Manager Performance Dashboard**
- **Metric:** Adjusted revenue per account manager.
- **Charts:** Bar chart and table view.


#### **Buyer Revenue Report**
- **Metric:** Total revenue from buyers.
- **Charts:** company-wise revenue distribution with percentage.



