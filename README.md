# Fetch_TakeHome
## First Section :- 
### Q1. Are there any data quality issues present? / Challenges with the data [have added a Jupyter File with EDA and basic visualizations]
The dataset initially faced several data quality issues that could hinder accurate analysis. 

1. Missing and Invalid Values in `FINAL_SALE`:
   - Issue: Some entries in `FINAL_SALE` were missing or non-numeric, which could lead to errors in calculations and an incomplete view of total sales.
   - Resolution: Converted `FINAL_SALE` to numeric, replacing invalid entries with `NaN` and then filling `NaN` with `0`. This standardization ensures that `FINAL_SALE` only contains valid numeric values.
2. Non-Numeric Entries in `FINAL_QUANTITY`:
   - Issue: The `FINAL_QUANTITY` column contained non-numeric entries like "zero," creating inconsistencies that could affect quantity-based calculations.
   - Resolution: Replaced non-numeric values with `0` and converted the column to numeric, allowing for straightforward and reliable analysis of product quantities.
3. Gaps in `CATEGORY` Levels:
   - Issue: Missing values in `CATEGORY_1` through `CATEGORY_4` left some products uncategorized, which could lead to incomplete or misleading results in category-based analysis.
   - Resolution: Filled missing category values with "Uncategorized," providing each product with a defined category and making category analysis more comprehensive.
4. Inconsistent Formatting in `USER_ID` and `ID` Fields:
   - Issue: Inconsistent formatting (such as leading/trailing spaces or different data types) in `USER_ID` and `ID` made it difficult to join user and transaction data accurately.
   - Resolution: Converted these identifiers to strings and removed whitespace, ensuring compatibility between the tables and facilitating accurate joins.
5. Inconsistent `STATE` Codes:
   - Issue: `STATE` codes in the user data were inconsistently formatted (e.g., some in lowercase or full names), which could lead to duplicate entries and errors in state-based analysis.
   - Resolution: Standardized all state codes to uppercase, eliminating ambiguity and enabling consistent grouping by state.
6. Inconsistent and Unreliable Date Formats:
   - Issue: `PURCHASE_DATE`, `SCAN_DATE`, and `CREATED_DATE` columns contained non-standard formats and invalid dates, which could distort time-based analysis.
   - Resolution: Converted these columns to a consistent datetime format and dropped rows with invalid dates, ensuring reliable time-series analysis.
7. Missing Descriptive Details for Products (`MANUFACTURER` and `BRAND`):
   - Issue: `NaN` values in `MANUFACTURER` and `BRAND` fields left some products without essential details, potentially impacting brand- or manufacturer-level insights.
   - Resolution: Filled these missing values with "Unknown" or "Not Provided," ensuring every product has defined manufacturer and brand fields.
8. Duplicate Transactions:
   - Issue: Duplicate entries in the transactions table could inflate metrics such as transaction counts or total sales.
   - Resolution: Remove  duplicates based on key columns (`RECEIPT_ID`, `PURCHASE_DATE`, and `USER_ID`), ensuring each transaction is unique and providing accurate transaction data.

## Second Question :- Queries [ Close Ended Questions ]
### Q1. Top 5 brands by receipts scanned among users 21 and over 

Query 1 :-

SELECT p.BRAND, COUNT(t.RECEIPT_ID) AS receipt_count 
FROM Transactions t JOIN Users u ON t.USER_ID = u.ID
 JOIN Products p ON t.BARCODE = p.BARCODE 
WHERE TIMESTAMPDIFF(YEAR, u.BIRTH_DATE, CURDATE()) >= 21 
GROUP BY p.BRAND 
ORDER BY receipt_count DESC;

![image alt](https://github.com/aishwaryajoshii/Fetch_TakeHome/blob/23e58f7b99359d38d5eedfa4afa07c5d5179bc73/Query_1.png)

### Q2. What are the top 5 brands by sales among users that have had their account for at least six months? 
Query 2 :- 

WITH account_filtered_users AS (
 SELECT ID AS user_id,
  CREATED_DATE
  FROM 
   USER_TAKEHOME WHERE CREATED_DATE <= DATEADD(MONTH, -6, GETDATE()))
SELECT p.BRAND,
SUM(CAST(t.FINAL_SALE AS DECIMAL(10, 2))) AS total_sales
FROM TRANSACTION_TAKEHOME t
JOIN 
account_filtered_users u ON t.USER_ID = u.user_id
JOIN PRODUCTS_TAKEHOME p ON t.BARCODE = p.BARCODE
GROUP BY p.BRAND
ORDER BY total_sales DESC
LIMIT 5;

![image alt](https://github.com/aishwaryajoshii/Fetch_TakeHome/blob/52bbfe4d67a9802156113c0c8fd959b1f658faad/query_2_save.png)

Second Question [Open Ended Questions]

- Who are Fetch’s power users?

Assumptions:

A power user is defined as someone who engages heavily with the Fetch app, which can include:
Frequent receipt scanning (e.g., more than 2 scans per month).
High cumulative spending on transactions.
Long account tenure with consistent activity.
Based on these assumptions, Fetch’s power users are likely those who:

Have a High Number of Monthly Scans: Users scanning more than 20 receipts per month likely have higher engagement and are actively collecting points.
Show High Cumulative Spending: Users with high cumulative FINAL_SALE values across transactions. These users likely use Fetch not just frequently but also for larger purchases, demonstrating greater buying power.
Have Long Account Tenure and Consistent Activity: Users who have maintained their accounts for over a year and have scanned receipts regularly, indicating loyalty and long-term value.
Identifying these power users would allow Fetch to tailor special incentives, such as exclusive discounts or reward tiers, to further enhance loyalty and engagement.

SQL Query

```
WITH monthly_scans AS (
    SELECT 
        USER_ID,
        YEAR(PURCHASE_DATE) AS year,
        MONTH(PURCHASE_DATE) AS month,
        COUNT(RECEIPT_ID) AS monthly_scan_count
    FROM 
        TRANSACTION_TAKEHOME
    GROUP BY 
        USER_ID, YEAR(PURCHASE_DATE), MONTH(PURCHASE_DATE)
),
frequent_users AS (
    SELECT 
        USER_ID,
        AVG(monthly_scan_count) AS avg_monthly_scans
    FROM 
        monthly_scans
    GROUP BY 
        USER_ID
    HAVING 
        AVG(monthly_scan_count) > 2
),
high_spending_users AS (
    SELECT 
        USER_ID,
        SUM(CAST(FINAL_SALE AS DECIMAL(10, 2))) AS total_spending
    FROM 
        TRANSACTION_TAKEHOME
    GROUP BY 
        USER_ID
    HAVING 
        SUM(CAST(FINAL_SALE AS DECIMAL(10, 2))) > 0
),
long_tenure_users AS (
    SELECT 
        ID AS USER_ID,
        DATEDIFF(MONTH, CREATED_DATE, GETDATE()) AS account_age_months
    FROM 
        USER_TAKEHOME
    WHERE 
        DATEDIFF(MONTH, CREATED_DATE, GETDATE()) >= 12
)
SELECT 
    u.ID AS User_ID,
    f.avg_monthly_scans,
    h.total_spending,
    l.account_age_months
FROM 
    USER_TAKEHOME u
JOIN 
    frequent_users f ON u.ID = f.USER_ID
JOIN 
    high_spending_users h ON u.ID = h.USER_ID
JOIN 
    long_tenure_users l ON u.ID = l.USER_ID
ORDER BY 
    h.total_spending DESC
LIMIT 10;
```
## Visual

![image alt](![image alt](https://github.com/aishwaryajoshii/Fetch_TakeHome/blob/67b7484c3942a25f95b065856a6b69c86845435b/table_1.png))

- What is Top Dips and Salsa Category ?

Assumptions:

A leading brand is defined by either:
The highest number of receipts scanned with purchases of its products in the "Dips & Salsa" category.
Or, the highest total sales in the "Dips & Salsa" category.
Answer: To determine the leading brand in the "Dips & Salsa" category, we would analyze:

Receipt Count: The brand with the most scanned receipts in the "Dips & Salsa" category would indicate popularity or frequent purchases. This can suggest brand loyalty or a competitive advantage in market penetration.
Sales Volume: Alternatively, the brand with the highest cumulative FINAL_SALE in this category represents the highest sales value, which could indicate premium positioning or preferred brand status among Fetch users.
The leading brand in the "Dips & Salsa" category is likely one that combines both high receipt counts and high total sales, signifying both popularity and strong revenue contribution within this product category.

SQL Query

```
WITH dips_and_salsa_products AS (
    SELECT 
        BARCODE,
        BRAND
    FROM 
        PRODUCTS_TAKEHOME
    WHERE 
        CATEGORY_2 = 'Dips & Salsa' 
        AND BRAND != 'Not Provided'
),

-- Join the filtered Dips & Salsa products with transactions to get transaction data
dips_and_salsa_transactions AS (
    SELECT 
        t.RECEIPT_ID,
        t.FINAL_SALE,
        p.BRAND
    FROM 
        TRANSACTION_TAKEHOME t
    INNER JOIN 
        dips_and_salsa_products p ON t.BARCODE = p.BARCODE
)

-- Query to get the leading brand by receipt count and by total sales
SELECT 
    -- Leading brand by receipt count
    (SELECT BRAND 
     FROM dips_and_salsa_transactions 
     GROUP BY BRAND 
     ORDER BY COUNT(RECEIPT_ID) DESC 
     LIMIT 1) AS leading_brand_by_receipt_count,

    -- Leading brand by total sales
    (SELECT BRAND 
     FROM dips_and_salsa_transactions 
     GROUP BY BRAND 
     ORDER BY SUM(FINAL_SALE) DESC 
     LIMIT 1) AS leading_brand_by_total_sales;

```
The answer is :- Tostitos


