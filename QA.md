What are your risk areas? Identify and describe them.

Here are the identified risk areas and a brief description of each:

1.	Data Profiling and Completeness: This step involves examining the presence of missing values and verifying data types. It's crucial to ensure the data is complete and adheres to the expected formats.

2.	Data Redundancy and Duplication: Detecting duplicate entries within date partitions suggests data redundancy. Investigating these duplicates and ensuring uniqueness in relevant columns can shed light on the nature of redundancy.

3.	Data Relationships and Referential Integrity: Validating foreign key relationships ensures that data references are consistent. The query identifies orphan records in table “all_sessions” that don't have corresponding records in the “products” table.

4.	Data Formats and Types: Checking for dates that don't match the expected format ('YYYY-MM-DD') or are NULL helps identify inconsistencies in data formats and types.

5.	Calculations and Aggregations: Validating aggregations helps ensure the accuracy of calculated values. In this case, checking for non-null aggregated values after converting time values to intervals helps avoid erroneous results.

6.	Data Consistency and Accuracy: Comparing total revenue in the” analytics” table with the sum of individual revenue values helps identify inconsistencies or inaccuracies in data.

7.	Data Completeness and Integrity: Checking for missing “product SKUs” (which is a primary key) in the “sales_by_sku” table compared to the products table reveals data integrity issues that need further investigation.

8.	Data Distribution: Analyzing the distribution of revenue among different product categories helps identify trends and potential issues in how revenue is distributed.

9.	Data Trends: Identifying top product categories by average sentiment score can reveal trends and insights, but there may be challenges if certain categories have insufficient data.
    
10.	Data Evolution: Comparing ordered quantities in sales_by_sku and products tables over time helps identify discrepancies in ordered quantities, which might indicate data issues.

---------------------------------------------

QA Process:
Describe your QA process and include the SQL queries used to execute it.

--1. Data Profiling and Completeness:

--Checked for missing values and data types:

```SQL
SELECT table_name, column_name, data_type, COUNT(*) AS total_rows,
       COUNT(*) - COUNT(column_name) AS missing_values
FROM information_schema.columns
WHERE table_name IN ('all_sessions', 'analytics', 'products', 'sales_by_sku', 'sales_report')
GROUP BY table_name, column_name, data_type;
```

--------------------------

--2. Data Redundancy and Duplication:
--Validated that there are no duplicate entries for the same full_visitor_id within the same date partition in the all_sessions table:

```SQL
SELECT date, full_visitor_id, COUNT(*) AS duplicate_count
FROM all_sessions
GROUP BY date, full_visitor_id
HAVING COUNT(*) > 1;
```

--The results of the query showed that there are duplicates.
--To investigate why there are duplicates, I viewed duplicate entries by showing all columns for duplicate entries of a specific 'full_visitor_id' on a particular 'date':

```SQL
SELECT *
FROM all_sessions
WHERE full_visitor_id = '3292284856612897209'
    AND "date" = '2016-09-18';
```
-- Then, I identified unique values by checking unique values of relevant columns for duplicate entries.

```SQL
SELECT DISTINCT channel_grouping,"time",v2_product_name, time_on_site, pageviews
FROM all_sessions
WHERE full_visitor_id = '3292284856612897209'
    AND date = '2016-09-18';
```
----------------------------------

--3. Data Relationships and Referential Integrity:
--Validated foreign key relationships:

```SQL
SELECT COUNT(*) AS orphan_records
FROM all_sessions a
LEFT JOIN products p ON a.product_sku = p.product_sku
WHERE p.product_sku IS NULL;
```
----------------------------------

--4. Data Formats and Types:
--To identified distinct dates that do not match the 'YYYY-MM-DD' pattern or are NULL:

```SQL
SELECT DISTINCT date
FROM all_sessions
WHERE NOT TO_CHAR(date, 'YYYY-MM-DD') ~ '^\d{4}-\d{2}-\d{2}$'
   OR date IS NULL;
```   
----------------------------------

--5. Calculations and Aggregations:
--Validated aggregations:

```SQL
SELECT channel_grouping, AVG(TIME '00:00:00' + time_on_site::INTERVAL) AS avg_time_on_site
FROM all_sessions
GROUP BY channel_grouping
HAVING AVG(TIME '00:00:00' + time_on_site::INTERVAL) IS NOT NULL;
```
----------------------------------

--6. Data Consistency and Accuracy:
--Checked if the total revenue in the analytics table matches the sum of individual revenue values:

```SQL
SELECT SUM(revenue) AS total_revenue_in_table,
       (SELECT SUM(revenue) FROM analytics) AS sum_of_individual_revenues
FROM analytics;
```
----------------------------------

--7. Data Completeness and Integrity:
--Validated that all product_sku values in the sales_by_sku table exist in the products table:

```SQL
SELECT COUNT(*) AS missing_products
FROM sales_by_sku s
WHERE NOT EXISTS (
    SELECT 1 FROM products p WHERE s.product_sku = p.product_sku
);
```
--The result is 8, which indicates the need to investigate and reconcile the missing product SKUs.

----------------------------------

--8. Data Distribution:
--Analyzed the distribution of revenue among different product categories:

```SQL
SELECT
    a.v2_product_category,                             -- Product category
    COUNT(*) AS total_products,                        -- Total number of products in category
    SUM(an.revenue) AS total_revenue,                  -- Total revenue from products in category
    AVG(an.revenue) AS avg_revenue,                    -- Average revenue per product in category
    STDDEV(an.revenue) AS revenue_stddev               -- Standard deviation of revenue in category
FROM all_sessions a
JOIN analytics an ON a.full_visitor_id = an.full_visitor_id
WHERE a.v2_product_category IS NOT NULL
GROUP BY a.v2_product_category
HAVING
    COUNT(*) IS NOT NULL
    AND SUM(an.revenue) IS NOT NULL
    AND AVG(an.revenue) IS NOT NULL
ORDER BY total_revenue DESC;
```
----------------------------------

--9. Data Trends:
--Identified the top product categories by average sentiment score:

```SQL
SELECT a.v2_product_category, AVG(p.sentiment_score) AS avg_sentiment_score
FROM all_sessions a
JOIN products p ON a.product_sku = p.product_sku
    GROUP BY a.v2_product_category
    ORDER BY avg_sentiment_score DESC;
```
----------------------------------

--10. Data Evolution:
--Compared the total ordered quantity in the sales_by_sku table with the ordered quantity in the products table over time:

```SQL
SELECT s.product_sku, p.name, s.total_ordered, p.ordered_quantity
FROM sales_by_sku s
JOIN products p ON s.product_sku = p.product_sku
ORDER BY s.product_sku;
```
----------------------------------




