Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:

```SQL
SELECT DISTINCT city, country, SUM(total_transaction_revenue) AS total_revenue
FROM all_sessions
	WHERE city IS NOT NULL AND country IS NOT NULL
		GROUP BY city, country
			HAVING SUM(total_transaction_revenue) IS NOT NULL
				ORDER BY total_revenue DESC
				LIMIT 12;
```

Answer:

```"city"	            "country"	        "total_revenue"
"San Francisco"	    "United States"	    1561
"Sunnyvale"	        "United States"	    991
"Atlanta"	        "United States"	    853
"Palo Alto"	        "United States"	    608
"Tel Aviv-Yafo"	    "Israel"	        602
"New York"	        "United States"	    526
"Mountain View"	    "United States"	    479
"Los Angeles"	    "United States"	    479
"Chicago"	        "United States"	    448
"Sydney"	        "Australia"	        358
"Seattle"	        "United States"	    358
"San Jose"	        "United States"	    262```

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/0856d746-006f-496d-8368-c5c1535d2516

------------------------------

**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:

WITH avg_products_cte AS (
    SELECT DISTINCT
        city,
        country,
        AVG(product_quantity) OVER (PARTITION BY city, country) AS avg_products_ordered
    FROM all_sessions
    WHERE city IS NOT NULL AND country IS NOT NULL
)
SELECT city, country, avg_products_ordered
FROM avg_products_cte
	WHERE avg_products_ordered IS NOT NULL
	ORDER BY avg_products_ordered DESC
		LIMIT 10;

Answer:

"city"	    "country"	        "avg_products_ordered"
"Madrid"	"Spain"	            10.0000000000000000
"Salem"	    "United States"    	8.0000000000000000
"Atlanta"	"United States"	    4.0000000000000000
"Houston"	"United States"	    2.0000000000000000
"New York"	"United States"	    1.1666666666666667
"Palo Alto"	"United States"	    1.00000000000000000000
"Dublin"	"Ireland"	        1.00000000000000000000
"San Jose"	"United States"	    1.00000000000000000000
"Ann Arbor"	"United States"	    1.00000000000000000000
"Sunnyvale"	"United States"	    1.00000000000000000000

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/7d097cea-7417-4674-999d-153c998f6405

-----------------------------------


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

SELECT
    city,
    country,
    STRING_AGG(DISTINCT v2_product_category, ', ') AS product_categories
FROM all_sessions
WHERE city IS NOT NULL AND country IS NOT NULL AND v2_product_category IS NOT NULL
GROUP BY city, country
    LIMIT 13;


Answer:

"city"	        "country"	        "product_categories"
"Adelaide"	    "Australia"	        "Home/Apparel/Men's/Men's-Outerwear/"
"Ahmedabad"	    "India"	            "Home/Accessories/, Home/Apparel/Men's/Men's-Outerwear/, Home/Apparel/Men's/Men's-T-Shirts/, Home/Bags/, Home/Shop by Brand/Google/, Home/Shop by Brand/YouTube/"
"Akron"	        "United States"	    "Home/Apparel/Men's/"
"Alexandria"	"Egypt"	            "Home/Apparel/Men's/Men's-T-Shirts/"
"Amã"	        "Jordan"	        "Home/Bags/"
"Amsterdam"	    "Netherlands"	    "Home/Accessories/Fun/, Home/Bags/, Home/Drinkware/Mugs and Cups/, Home/Electronics/, Home/Electronics/Flashlights/, Home/Electronics/Power/, Home/Office/, Home/Shop by Brand/Google/, Home/Shop by Brand/YouTube/"
"Amsterdam"	    "United States"	    "Home/Apparel/Men's/"
"Ann Arbor"	    "United States"	    "Home/Accessories/, Home/Accessories/Fun/, Home/Accessories/Stickers/, Home/Apparel/, Home/Apparel/Kid's/, Home/Apparel/Kid's/Kids-Youth/, Home/Apparel/Men's/Men's-Outerwear/, Home/Apparel/Men's/Men's-T-Shirts/, Home/Apparel/Women's/, Home/Apparel/Women's/Women's-Outerwear/, Home/Apparel/Women's/Women's-T-Shirts/, Home/Bags/, Home/Clearance Sale/, Home/Electronics/, Home/Electronics/Audio/, Home/Electronics/Electronics Accessories/, Home/Lifestyle/, Home/Limited Supply/Bags/, Home/Nest/Nest-USA/, Home/Shop by Brand/Android/, Home/Shop by Brand/Google/, Home/Shop by Brand/YouTube/"
"Antwerp"	     "Belgium"	        "Home/Drinkware/, Home/Office/"
"Appleton"	     "United States"	"Home/Electronics/"
"Ashburn"	     "United States"	"Home/Accessories/Housewares/, Home/Lifestyle/Fun/"
"Asuncion"	     "Paraguay"	        "Home/Apparel/Men's/Men's-T-Shirts/"
"Athens"	     "Greece"	        "Home/Apparel/Men's/Men's-T-Shirts/, Home/Bags/, Home/Shop by Brand/YouTube/"


![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/e7676cbc-4b36-40ef-ae2d-58c1103b7e1f 

-------------------------------------------

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

WITH product_sales AS (
    SELECT
        session.city,
        session.country,
        session.v2_product_name,
        session.channel_grouping,
        SUM(sales.total_ordered) AS total_quantity,
        RANK() OVER (PARTITION BY session.city, session.country ORDER BY SUM(sales.total_ordered) DESC) AS rank
    FROM all_sessions session
    JOIN sales_by_sku sales ON session.product_sku = sales.product_sku
    WHERE session.city IS NOT NULL AND session.country IS NOT NULL
    GROUP BY session.city, session.country, session.v2_product_name, session.channel_grouping
),
total_sales_by_city_country AS (
    SELECT
        city,
        country,
        SUM(sales.total_ordered) AS total_sales
    FROM all_sessions session
    JOIN sales_by_sku sales ON session.product_sku = sales.product_sku
    WHERE session.city IS NOT NULL AND session.country IS NOT NULL
    GROUP BY session.city, session.country
),
channel_grouping_distribution AS (
    SELECT
        session.city,
        session.country,
        session.channel_grouping,
        COUNT(*) AS channel_count
    FROM all_sessions session
    WHERE session.city IS NOT NULL AND session.country IS NOT NULL
    GROUP BY session.city, session.country, session.channel_grouping
)
SELECT
    ps.city,
    ps.country,
    ps.v2_product_name,
    ps.total_quantity,
    (ps.total_quantity * 100.0) / NULLIF(tsc.total_sales, 0) AS percentage,
    cd.channel_grouping,
    cd.channel_count
FROM product_sales ps
JOIN total_sales_by_city_country tsc ON ps.city = tsc.city AND ps.country = tsc.country
LEFT JOIN channel_grouping_distribution cd ON ps.city = cd.city AND ps.country = cd.country AND ps.channel_grouping = cd.channel_grouping
	WHERE ps.rank = 1 AND ps.total_quantity > 0
	ORDER BY ps.city, ps.country, ps.v2_product_name
	LIMIT 13;

Answer:

"city"	        "country"	    "v2_product_name"	                                                "total_quantity"	"percentage"	        "channel_grouping"	"channel_count"
"Adelaide"	    "Australia"	    "Google Men's Watershed Full Zip Hoodie Grey"	                    3	                100.0000000000000000	"Organic Search"	1
"Ahmedabad"	    "India"	        "Google Canvas Tote Natural/Navy"	                                62	                68.8888888888888889	    "Organic Search"	10
"Akron"	        "United States"	"Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	            13	                100.0000000000000000	"Affiliates"	    1
"Amsterdam"	    "Netherlands"	"YouTube Hard Cover Journal"	                                    85	                42.7135678391959799	    "Organic Search"	9
"Ann Arbor"	    "United States"	"Koozie Can Kooler"	                                                253	                37.0967741935483871	    "Referral"	        29
"Antwerp"	    "Belgium"	    "Colored Pencil Set"	                                            3	                100.0000000000000000	"Organic Search"	2
"Ashburn"	    "United States"	"Compact Selfie Stick"	                                            5	                62.5000000000000000	    "Direct"	        2
"Asuncion"	    "Paraguay"	    "Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	            13	                100.0000000000000000	"Direct"	        1
"Athens"	    "Greece"	    "YouTube Hard Cover Journal"	                                    85	                41.4634146341463415	    "Organic Search"	4
"Atlanta"	    "United States"	"Nest® Protect Smoke + CO White Wired Alarm-USA"	                126	                30.0000000000000000	    "Referral"	        11
"Austin"	    "United States"	"Leatherette Journal"	                                            319	                19.0561529271206691	    "Referral"	        39
"Avon"	        "United States"	"Google Blackout Cap"	                                            189	                94.5000000000000000	    "Organic Search"	2
"Bandung"	    "Indonesia"	    "YouTube Men's Short Sleeve Hero Tee Charcoal"	                    6	                100.0000000000000000	"Organic Search"	1

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/b49b0354-201e-46cb-8e63-a0c02880f360 

------------------------------------

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:


WITH revenue_summary AS (
    SELECT
        city,
        country,
        SUM(total_transaction_revenue) AS total_revenue,
        AVG(total_transaction_revenue) AS avg_revenue_per_session,
        COUNT(DISTINCT full_visitor_id) AS session_count
    FROM all_sessions
    WHERE city IS NOT NULL AND country IS NOT NULL
    GROUP BY city, country
)
SELECT
    city,
    country,
    total_revenue,
    avg_revenue_per_session,
    session_count,
    CASE
        WHEN avg_revenue_per_session < 50 THEN 'Low'
        WHEN avg_revenue_per_session >= 50 AND avg_revenue_per_session < 200 THEN 'Medium'
        WHEN avg_revenue_per_session >= 200 THEN 'High'
    END AS revenue_impact
FROM revenue_summary
WHERE total_revenue IS NOT NULL AND avg_revenue_per_session IS NOT NULL
ORDER BY total_revenue DESC
	LIMIT 12;

Answer:

"city"	            "country"	        "total_revenue"	    "avg_revenue_per_session"	"session_count"	"revenue_impact"
"San Francisco"	    "United States"	    1561	            130.0833333333333333	    430	            "Medium"
"Sunnyvale"	        "United States"	    991	                247.7500000000000000	    343	            "High"
"Atlanta"	        "United States"	    853	                426.5000000000000000	    62	            "High"
"Palo Alto"	        "United States"	    608	                202.6666666666666667	    95	            "High"
"Tel Aviv-Yafo"	    "Israel"	        602	                602.0000000000000000	    45	            "High"
"New York"	        "United States"	    526	                65.7500000000000000	        602	            "Medium"
"Los Angeles"	    "United States"	    479	                239.5000000000000000	    205	            "High"
"Mountain View"	    "United States"	    479	                59.8750000000000000	        1083	        "Medium"
"Chicago"	        "United States"	    448	                149.3333333333333333	    178	            "Medium"
"Sydney"	        "Australia"	        358	                358.0000000000000000	    72	            "High"
"Seattle"	        "United States"	    358	                358.0000000000000000	    116	            "High"
"San Jose"	        "United States"	    262	                131.0000000000000000	    228	            "Medium"

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/c536020f-290b-4ba2-bc91-2faff0bcd059 

---------------------------------------




