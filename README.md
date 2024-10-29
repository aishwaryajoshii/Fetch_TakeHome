# Fetch_TakeHome
First Section :- 
Q1. Are there any data quality issues present? / Challenges with the data [have added a Jupyter File with EDA and basic visualizations]
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

Second Question :- Queries [ Q2. - Q1. Top 5 brands by receipts scanned among users 21 and over ]





![image alt][https://github.com/aishwaryajoshii/Fetch_TakeHome/blob/52bbfe4d67a9802156113c0c8fd959b1f658faad/query_2_save.png]
