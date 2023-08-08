Question 1: What are the most common channel groupings for visitors who make a purchase on the website?

SQL Queries:

SELECT channel_grouping, COUNT(*) AS purchase_count
FROM all_sessions
	WHERE total_transaction_revenue > 0
	GROUP BY channel_grouping
	ORDER BY purchase_count DESC;

Answer: 

"channel_grouping"	  "purchase_count"
"Referral"	          32
"Direct"		  24
"Organic Search"	  22
"Paid Search"	          3

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/9a743de4-b481-41cc-8394-e355c23fe22d

--------------------------------------------

Question 2: Channel Grouping Influence: Which channel groupings lead to the highest revenue generation?

SQL Queries:

SELECT channel_grouping, SUM(revenue) AS total_revenue
FROM analytics
	WHERE revenue IS NOT NULL
	GROUP BY channel_grouping
	ORDER BY total_revenue DESC;

Answer:

"channel_grouping"	"total_revenue"
"Referral"		833754.2690060000000000
"Direct"		181513.6595910000000000
"Organic Search"	113670.9794790000000000
"Paid Search"		20128.0799240000000000
"Display"		14585.2099730000000000
"Social"		2895.5399930000000000
"Affiliates"		530.6999910000000000

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/fbab9ad0-a4ff-4bce-871c-a837cec78ef2

----------------------------------------------------

Question 3: Popular Products by Revenue: What are the top-selling products, ordered by revenue, along with their corresponding product names?

SQL Queries:

SELECT a.product_sku, p.name, SUM(analytics.revenue) AS total_revenue
FROM all_sessions a
	JOIN products p ON a.product_sku = p.product_sku
	JOIN analytics ON a.full_visitor_id = analytics.full_visitor_id
		WHERE analytics.revenue IS NOT NULL
		GROUP BY a.product_sku, p.name
		HAVING SUM(analytics.revenue) IS NOT NULL
		ORDER BY total_revenue DESC
			LIMIT 10;
Answer:

"product_sku"		"name"						"total_revenue"
"GGOEGAAX0363"		" Women's Long Sleeve Tee Lavender"		9832.2299960000000000
"GGOEAHPA004110"	"Android Wool Heather Cap Heather/Black"	9172.6999960000000000
"GGOEYOLR018699"	" Leatherette Notebook Combo"			9117.2299960000000000
"9182581"		" Women's Fleece Hoodie"			9117.2299960000000000
"GGOEGAAX0098"		"7 Dog Frisbee"					9034.1799970000000000
"GGOEGAAX0231"		" Collapsible Pet Bowl"				9034.1799970000000000
"GGOENEBB078899"	" Cam Indoor Security Camera - USA"		3800.8100000000000000
"GGOEYDHJ056099"	"22 oz  Bottle Infuser"				3548.3099980000000000
"GGOENEBQ079099"	" Protect Smoke + CO White Battery Alarm-USA"	2104.9699980000000000
"GGOENEBQ079199"	" Protect Smoke + CO White Wired Alarm-USA"	1822.0899960000000000

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/60c0bebe-a8e9-4fa6-8f2c-cd953a94bf46

-----------------------------------------------------

Question 4: Best-Selling Products: Which products are the best-sellers in terms of the total number of units sold?

SQL Queries:

SELECT p.name, SUM(a.product_quantity) AS total_units_sold
FROM all_sessions a
	JOIN products p ON a.product_sku = p.product_sku
		GROUP BY p.name
		HAVING SUM(a.product_quantity) IS NOT NULL
		ORDER BY total_units_sold DESC
			LIMIT 5;

Answer:

"name"							"total_units_sold"
"Leatherette Journal"					65
" Dress Socks"						10
"Red Spiral  Notebook"					8
" Cam Outdoor Security Camera - USA"			6
" Learning Thermostat 3rd Gen-USA - Stainless Steel"	5

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/c8d96251-b20f-4f20-8f56-3482cd5e0144

--------------------------------------------------------

Question 5: Transaction Performance by Product: Which products have the highest transaction revenue and the lowest restocking lead time?

SQL Queries:

SELECT p.name, 
       SUM(a.transaction_revenue) AS total_transaction_revenue,
       MIN(p.restocking_lead_time) AS min_restocking_lead_time
FROM all_sessions a
	JOIN products p ON a.product_sku = p.product_sku
		GROUP BY p.name
		HAVING SUM(a.transaction_revenue) IS NOT NULL
		ORDER BY total_transaction_revenue DESC, min_restocking_lead_time ASC;

Answer:

"name"					"total_transaction_revenue"	"min_restocking_lead_time"
" Bongo Cupholder Bluetooth Speaker"	1005				"08:00:00"
" Cam Indoor Security Camera - USA"	200				"10:00:00"

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/0220debb-03b2-4ad6-92c9-d1e98dfaa686 

---------------------------------------------------------
