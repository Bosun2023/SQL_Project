Question 1: What are the most common channel groupings for visitors who make a purchase on the website?

SQL Queries:

SELECT channel_grouping, COUNT(*) AS purchase_count
FROM all_sessions
	WHERE total_transaction_revenue > 0
	GROUP BY channel_grouping
	ORDER BY purchase_count DESC;

Answer: 

"channel_grouping"	  "purchase_count"
"Referral"	            32
"Direct"	              24
"Organic Search"	      22
"Paid Search"	           3

![image] https://github.com/Bosun2023/SQL_Project/assets/129349143/a221247c-3041-4a4d-aafa-0cc3e3f17f8b

--------------------------------

Question 2: 

SQL Queries:

Answer:



Question 3: 

SQL Queries:

Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
