What issues will you address by cleaning the data?

```
**Answer: ** The issues that will be addressed to clean up the data are highlighted below.
  1.	Data Type Adjustments and Conversion:
•	Data types of various columns will be converted to appropriate ones, such as numeric, varchar, and interval.
•	Consistent data types will ensure improved storage and analysis.
  2.	Column Name Consistency:
•	Column names will be renamed from camel case to snake case to achieve uniformity and enhance readability.
  3.	Data Quality Enhancement:
•	Specific values (like '(not set)' and 'not available in demo dataset') will be replaced with NULL, resulting in cleaner and more accurate data representation.
•	Columns containing time-related information will be cleaned up, converting values to appropriate time formats.
•	Rows with NULL and missing values will be filtered out as needed.
•	Duplicate rows will be removed from the tables.
  4.	Data Precision Enhancement:
•	Values in certain columns will be divided by 1000000 to achieve a more meaningful scale.
  5.	Data Integrity and Consistency:
•	The uniqueness of certain columns will be ensured, making them primary keys where necessary.
•	Outliers will be addressed by filtering out rows with dates earlier than '2016-01-01'.
•	Rows with NULL values in specific columns will be removed.
  6.	Data Validation:
•	Unique and non-null values in specific columns will be checked to guarantee data quality.
These steps will collectively enhance the overall quality, consistency, and integrity of the data, making it more suitable for future analysis and reporting.
```

Queries:
Below, provide the SQL queries you used to clean your data.
  
--I created a backup of the source database by right-clicking the ecommerce database and clicking on Backup...

```SQL
SELECT * FROM all_sessions;
```

--To convert the data type of column "fullVisitorId" to varying character

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "fullVisitorId" TYPE varchar;
```

--To convert "fullVisitorId" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "fullVisitorId" TO "full_visitor_id";
```

--To convert "channelGrouping" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "channelGrouping" TO "channel_grouping";
```

--To convert the data type of column "time" to interval

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "time" TYPE interval USING ("time" || ' seconds'):: interval;
```
 
--To replace '(not set)' in column "country" to NULL

```SQL
UPDATE all_sessions
	SET country =
		CASE
			WHEN country = '(not set)' THEN NULL
			ELSE country
		END;
```

--To replace '(not set)' and 'not available in demo dataset' in column "city" to NULL

```SQL
UPDATE all_sessions
	SET city =	
		CASE
			WHEN city IN ('(not set)', 'not available in demo dataset') THEN NULL
			ELSE city
		END;
```

--To convert the data type of column "totalTransactionRevenue" to bigint

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "totalTransactionRevenue" TYPE bigint
	USING ("totalTransactionRevenue"::bigint);
```

--To convert "totalTransactionRevenue" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "totalTransactionRevenue" TO "total_transaction_revenue";
```

--To get the appropriate total_transaction_revenue, divide by 1000000

```SQL
UPDATE all_sessions
SET total_transaction_revenue = total_transaction_revenue / 1000000;
```

--To convert the data type of column "transactions" to numeric

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN transactions TYPE numeric
	USING (transactions::numeric)
```

--To update "timeOnSite" to time format

```SQL
UPDATE all_sessions
	SET "timeOnSite" = ("timeOnSite" || ' seconds')::interval;
```

--To convert "timeOnSite" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "timeOnSite" TO "time_on_site";
```

--To convert column "pageviews" to bigint

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN pageviews TYPE bigint
	USING (transactions::bigint);
```

--To convert column "sessionQualityDim" to smallint

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "sessionQualityDim" TYPE smallint
	USING (transactions::smallint);
```

--To convert column "sessionQualityDim" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "sessionQualityDim" TO "session_quality_dim";
```

--To convert the data type of column "date" to date 

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "date" TYPE date
	USING ("date"::date);
```

--To convert the data type of column "visitId" to varying character

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "visitId" TYPE varchar;
```

--To convert column "visitId" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "visitId" TO "visitid";
```

--To convert the data type of column "productRefundAmount" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "productRefundAmount" TYPE bigint
    USING ("productRefundAmount"::bigint);
```

--To convert column "productRefundAmount" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "productRefundAmount" TO "product_refund_amount";
```

--To convert the data type of column "productQuantity" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "productQuantity" TYPE bigint
    USING ("productQuantity"::bigint);
```

--To convert column "productQuantity" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "productQuantity" TO "product_quantity";
```

--To convert the data type of column "productPrice" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "productPrice" TYPE bigint
    USING ("productPrice"::bigint);
```

--To convert column "productPrice" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "productPrice" TO "product_price";
```

--To get the appropriate product_price, divide by 1000000

```SQL
UPDATE all_sessions
SET product_price = product_price / 1000000;
```

--To convert the data type of column "productRevenue" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "productRevenue" TYPE bigint
    USING ("productRevenue"::bigint);
```

--To convert column "productRevenue" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "productRevenue" TO "product_revenue";
```

--To get the appropriate product_revenue, divide by 1000000

```SQL
UPDATE all_sessions
SET product_revenue = product_revenue / 1000000;
```

--To convert the data type of column "productSKU" to varying character

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "productSKU" TYPE varchar;
```

--To convert column "productSKU" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "productSKU" TO "product_sku";
```

--To convert column "v2ProductName" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "v2ProductName" TO "v2_product_name";
```

--To convert column "v2ProductCategory" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "v2ProductCategory" TO "v2_product_category";
```

--To replace '(not set)' in column "v2_product_category" to NULL

```SQL
UPDATE all_sessions
	SET "v2_product_category" =
		CASE
			WHEN "v2_product_category" = '(not set)' THEN NULL
			ELSE "v2_product_category"
		END;
```

--To replace '(not set)' in column "productVariant" to NULL

```SQL
UPDATE all_sessions
	SET "productVariant" =
		CASE
			WHEN "productVariant" = '(not set)' THEN NULL
			ELSE "productVariant"
		END;
```

--To convert column "productVariant" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "productVariant" TO "product_variant";
```

--To convert column "currencyCode" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "currencyCode" TO "currency_code";
```

--To convert data type of column "itemQuantity" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "itemQuantity" TYPE bigint
    USING ("itemQuantity"::bigint);
```

--To convert column "itemQuantity" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "itemQuantity" TO "item_quantity";
```

--To convert data type of column "itemRevenue" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "itemRevenue" TYPE bigint
    USING ("itemRevenue"::bigint);
```

--To convert column "itemRevenue" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "itemRevenue" TO "item_revenue";
```

--To get the appropriate item_revenuee, divide by 1000000

```SQL
UPDATE all_sessions
	SET item_revenue = item_revenue / 1000000;
```

--To convert data type of column "transactionRevenue" to bigint

```SQL
ALTER TABLE all_sessions
    ALTER COLUMN "transactionRevenue" TYPE bigint
    USING ("transactionRevenue"::bigint);
```

--To convert column "transactionRevenue" from camel case to snake case

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "transactionRevenue" TO "transaction_revenue";
```

--To get the appropriate transaction_revenue, divide by 1000000

```SQL
UPDATE all_sessions
	SET transaction_revenue = transaction_revenue / 1000000;
```

--To convert data type of column "transactionId" to varying character

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "transactionId" TYPE varchar;
```

--To convert "transactionId" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "transactionId" TO "transaction_id";
```

--To convert "pageTitle" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "pageTitle" TO "page_title";
```

--To convert "searchKeyword" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "searchKeyword" TO "search_keyword";
```

--To convert "pagePathLevel1" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "pagePathLevel1" TO "page_path_level1";
```

--To convert data type of column "eCommerceAction_type" to numeric

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "eCommerceAction_type" TYPE numeric
	USING ("eCommerceAction_type"::numeric);
```

--To convert "eCommerceAction_type" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "eCommerceAction_type" TO "e_commerce_action_type";
```

--To convert data type of column "eCommerceAction_step" to numeric

```SQL
ALTER TABLE all_sessions
	ALTER COLUMN "eCommerceAction_step" TYPE numeric
	USING ("eCommerceAction_step"::numeric);
```

--To convert "eCommerceAction_step" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "eCommerceAction_step" TO "e_commerce_action_step";
```

--To convert "eCommerceAction_option" from camel case to snake case 

```SQL
ALTER TABLE all_sessions
	RENAME COLUMN "eCommerceAction_option" TO "e_commerce_action_option";
```

--To remove missing data from table all_sessions

```SQL
SELECT * 
FROM all_sessions
	WHERE full_visitor_id IS NOT NULL;
```

--To clean outlier

```SQL
SELECT * 
FROM all_sessions
	WHERE "date" >= '2016-01-01';
```

--To check if column "product_sku" is unique and not NULL

```SQL
SELECT product_sku
FROM all_sessions
WHERE product_sku IS NULL OR product_sku IN (
    SELECT product_sku
    FROM all_sessions
    GROUP BY product_sku
    HAVING COUNT(*) > 1
);
```

--To validate the cleaned all_sessions table 

```SQL
SELECT * 
FROM all_sessions;
```
---------------------------

```SQL
SELECT * FROM analytics;
```

--To convert data type of column "visitNumber" to numeric

```SQL
ALTER TABLE analytics
    ALTER COLUMN "visitNumber" TYPE numeric
    	USING ("visitNumber"::numeric);
```

--To convert column "visitNumber" from camel case to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "visitNumber" TO "visit_number";
```

--To convert data type of column "visitId" to varying character

```SQL
ALTER TABLE analytics
	ALTER COLUMN "visitId" TYPE varchar;
```

--To convert column "visitId" from camel case to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "visitId" TO "visit_id";
```

--To convert data type of column "visitStartTime" to varying character

```SQL
ALTER TABLE analytics
	ALTER COLUMN "visitStartTime" TYPE varchar;
```

--To convert "visitStartTime" from camel case to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "visitStartTime" TO "visit_start_time";
```

--To convert data type of column "date" to date 

```SQL
ALTER TABLE analytics
	ALTER COLUMN "date" TYPE date
	USING ("date"::date);
```

--To convert data type of column "fullvisitorId" to varying character

```SQL
ALTER TABLE analytics
	ALTER COLUMN "fullvisitorId" TYPE varchar;
```

--To convert column "fullvisitorId" from camel case to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "fullvisitorId" TO "full_visitor_id";
```

--To convert column "channelGrouping" from camel case to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "channelGrouping" TO "channel_grouping";
```

--To convert column "socialEngagementType" from camel case to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "socialEngagementType" TO "social_engagement_type";
```

--To convert data type of column "units_sold" to bigint

```SQL
ALTER TABLE analytics
    ALTER COLUMN "units_sold" TYPE bigint
    	USING ("units_sold"::bigint);
```

--To convert data type of column "pageviews" to bigint

```SQL
ALTER TABLE analytics
    ALTER COLUMN  "pageviews" TYPE bigint
    	USING ( "pageviews"::bigint);
---
--To update column "timeonsite" to time format

```SQL
UPDATE analytics
	SET "timeonsite" = ("timeonsite" || ' seconds')::interval;
```

--To convert column "timeonsite" to snake case 

```SQL
ALTER TABLE analytics
	RENAME COLUMN "timeonsite" TO "time_on_site";
```

--To convert data type of column "bounces" to integer

```SQL
ALTER TABLE analytics
    ALTER COLUMN  "bounces" TYPE integer
    	USING ( "bounces"::integer);
```

--To convert data type of column "revenue" to numeric

```SQL
ALTER TABLE analytics
    ALTER COLUMN revenue TYPE numeric
    	USING (revenue::numeric);
```

--To get the appropriate revenue, divide by 1000000

```SQL
UPDATE analytics
SET revenue = revenue / 1000000;
```

--To convert data type of column "unit_price" to numeric

```SQL
ALTER TABLE analytics
    ALTER COLUMN unit_price TYPE numeric
    	USING (unit_price::numeric);
```

--To get the appropriate unit_price, divide by 1000000

```SQL
UPDATE analytics
SET unit_price = unit_price / 1000000;
```

--To remove duplicate data from table analytics

```SQL
SELECT DISTINCT *
FROM analytics;
```

--To clean missing data from column "units_sold"

```SQL
SELECT units_sold
FROM analytics
	WHERE units_sold IS NOT NULL
```

--To validate the cleaned analytics table 

```SQL
SELECT * 
FROM analytics;
```
-------------------------------------

```SQL
SELECT * FROM products
```

--To convert data type of column "SKU" to varying character

```SQL
ALTER TABLE products
	ALTER COLUMN "SKU" TYPE varchar;
```

--To convert column "SKU" to snake case

```SQL
ALTER TABLE products
	RENAME COLUMN "SKU" TO "product_sku";
```
	
--To convert data type of column "orderedQuantity" to bigint

```SQL
ALTER TABLE products
    ALTER COLUMN "orderedQuantity" TYPE bigint
    	USING ("orderedQuantity"::bigint);
```

--To convert column "orderedQuantity" from camel case to snake case 

```SQL
ALTER TABLE products
	RENAME COLUMN "orderedQuantity" TO "ordered_quantity";
```

--To convert data type of column "stockLevel" to bigint

```SQL
ALTER TABLE products
    ALTER COLUMN "stockLevel" TYPE bigint
    	USING ("stockLevel"::bigint);
```

--To convert column "stockLevel" from camel case to snake case 

```SQL
ALTER TABLE products
	RENAME COLUMN "stockLevel" TO "stock_level";
```
	
--To update "restockingLeadTime" to time format

```SQL
UPDATE products
SET "restockingLeadTime" = ("restockingLeadTime" || ' hours')::interval;
```

--To convert column "restockingLeadTime" from camel case to snake case 

```SQL
ALTER TABLE products
	RENAME COLUMN "restockingLeadTime" TO "restocking_lead_time";
```

--To convert data type of column "sentimentScore" to double precision

```SQL
ALTER TABLE products
    ALTER COLUMN "sentimentScore" TYPE double precision
    	USING ("sentimentScore"::double precision);
```

--To convert column "sentimentScore" from camel case to snake case 

```SQL
ALTER TABLE products
	RENAME COLUMN "sentimentScore" TO "sentiment_score";
```

--To convert data type of column "sentimentMagnitude" to double precision

```SQL
ALTER TABLE products
    ALTER COLUMN "sentimentMagnitude" TYPE double precision
    	USING ("sentimentMagnitude"::double precision);
```

--To convert column "sentimentMagnitude" from camel case to snake case 

```SQL
ALTER TABLE products
	RENAME COLUMN "sentimentMagnitude" TO "sentiment_magnitude";
```

--To make sure that column "product_sku" is unique and not NULL

```SQL
SELECT product_sku
FROM products
WHERE product_sku IS NULL OR product_sku IN (
    SELECT product_sku
    FROM products
    GROUP BY product_sku
    HAVING COUNT(*) > 1
);
```

--To make column "product_sku" a primary key in table products

```SQL
ALTER TABLE products
	ADD PRIMARY KEY (product_sku);
```

--To validate cleaned products table 

```SQL
SELECT * 
FROM products; 
```
----------------------------------

```SQL
SELECT * FROM sales_by_sku
```

--To convert data type of column "productSKU" to varying character

```SQL
ALTER TABLE sales_by_sku
	ALTER COLUMN "productSKU" TYPE varchar;
```

--To convert column "productSKU" from camel case to snake case

```SQL
ALTER TABLE sales_by_sku
	RENAME COLUMN "productSKU" TO "product_sku";
```
	
--To convert data type of column "total_ordered" to bigint

```SQL
ALTER TABLE sales_by_sku
    ALTER COLUMN "total_ordered" TYPE bigint
    	USING ("total_ordered"::bigint);
```

--To make sure that column "product_sku" is unique and not NULL

```SQL
SELECT product_sku
FROM sales_by_sku
WHERE product_sku IS NULL OR product_sku IN (
    SELECT product_sku
    FROM sales_by_sku
    GROUP BY product_sku
    HAVING COUNT(*) > 1
);
```

--To make column "product_sku" a primary key in table products

```SQL
ALTER TABLE sales_by_sku
	ADD PRIMARY KEY (product_sku);
```
	
--To validate cleaned sales_by_sku table 

```SQL
SELECT * 
FROM sales_by_sku; 
```
-----------------------------------

```SQL
SELECT * FROM sales_report
```

--To convert data type of column "productSKU" to varying character

```SQL
ALTER TABLE sales_report
	ALTER COLUMN "productSKU" TYPE varchar;
```

--To convert column "productSKU" from camel case to snake case

```SQL
ALTER TABLE sales_report
	RENAME COLUMN "productSKU" TO "product_sku";
```

--To convert data type of column "total_ordered" to bigint

```SQL
ALTER TABLE sales_report
    ALTER COLUMN "total_ordered" TYPE bigint
    	USING ("total_ordered"::bigint);
```

--To convert data type of column "stockLevel" to bigint

```SQL
ALTER TABLE sales_report
    ALTER COLUMN "stockLevel" TYPE bigint
    	USING ("stockLevel"::bigint);
```

--To convert column "stockLevel" from camel case to snake case

```SQL
ALTER TABLE sales_report
	RENAME COLUMN "stockLevel" TO "stock_level";
```

--To update "restockingLeadTime" to time format

```SQL
UPDATE sales_report
SET "restockingLeadTime" = ("restockingLeadTime" || ' hours')::interval;
```

--To convert column "restockingLeadTime" from camel case to snake case 

```SQL
ALTER TABLE sales_report
	RENAME COLUMN "restockingLeadTime" TO "restocking_lead_time";
```

--To convert data type of column "sentimentScore" to double precision

```SQL
ALTER TABLE sales_report
	ALTER COLUMN "sentimentScore" TYPE double precision
    	USING ("sentimentScore"::double precision);
```

--To convert column "sentimentScore" from camel case to snake case 

```SQL
ALTER TABLE sales_report
	RENAME COLUMN "sentimentScore" TO "sentiment_score";
```

--To convert data type of column "sentimentMagnitude" to double precision

```SQL
ALTER TABLE sales_report
    ALTER COLUMN "sentimentMagnitude" TYPE double precision
    	USING ("sentimentMagnitude"::double precision);
```

--To convert column "sentimentMagnitude" from camel case to snake case 

```SQL
ALTER TABLE sales_report
	RENAME COLUMN "sentimentMagnitude" TO "sentiment_magnitude";
```

--To convert the data type of column "ratio" to double precision

```SQL
ALTER TABLE sales_report
    ALTER COLUMN "ratio"TYPE double precision
    	USING ("ratio"::double precision);
```

--To make sure that column "product_sku" is unique and not NULL

```SQL
SELECT product_sku
FROM sales_report
WHERE product_sku IS NULL OR product_sku IN (
    SELECT product_sku
    FROM sales_report
    GROUP BY product_sku
    HAVING COUNT(*) > 1
);
```

--To make column "product_sku" a primary key in table sales_report

```SQL
ALTER TABLE sales_report
	ADD PRIMARY KEY (product_sku);
```

--To validate cleaned sales_report table 

```SQL
SELECT * 
FROM sales_report; 
```
---------------------------------------
