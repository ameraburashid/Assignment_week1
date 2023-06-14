Question 1: -- which products generated the most ammount in the USA


SQL Queries:


SELECT 
	als.v2productname,
	als.country,
	sum(ana.units_sold) AS total_units_sold,
	SUM(ana.units_sold * als.productprice) AS total_sum
FROM all_sessions AS als 
JOIN analytics AS ana
	ON als.visitid = ana.visitid 
WHERE ana.units_sold IS NOT null AND country = 'United States'
GROUP by als.v2productname, als.country
ORDER BY total_units_sold DESC

Answer: Google Alpine Style Backpack, 22697.73


Question 2: --which country had the most pageviews 

SQL Queries: 

with views_per_country AS (
SELECT distinct(country) as country, SUM(pageviews) as views
FROM all_sessions as als 
GROUP BY country
)

SELECT country, views 
FROM views_per_country
ORDER BY views DESC

Answer: 

"country"	"views"
"United States"	43584



Question 3: -- do visitors from the United States spend more time on the website than other visitors from other countries?


SQL Queries:


SELECT 'USA', AVG(timeonsite) as time, country 
FROM all_sessions 
WHERE country = 'United States'
GROUP BY country

UNION 

SELECT 'other', AVG(timeonsite) as time, country 
FROM all_sessions 
WHERE country != 'United States'
GROUP BY country
order by time DESC

Answer:
The average view time of USA lists it number 28 on the list between countries. there are 27 other countries that spends time on the website more 


Question 4: what is the percentage of total_sales in the USA vs other counties 

SQL Queries 

SELECT
    country,
    SUM(ana.units_sold * als.productprice) AS total_revenue,
    (SUM(ana.units_sold * als.productprice) / (SELECT SUM(ana.units_sold * als.productprice) FROM all_sessions as als JOIN analytics as ana USING (visitid))) * 100 AS percentage_of_total
FROM all_sessions as als
JOIN analytics as ana USING (visitid)
GROUP BY country
HAVING SUM(ana.units_sold * als.productprice) IS NOT NULL
ORDER BY total_revenue DESC;

Answer: 94%


Question 5: -- does the time spent browsing the website relates to the amount of products sold? 


SQL Queries:



with T1 AS (
SELECT timeonsite, units_sold AS sold
FROM analytics
WHERE timeonsite BETWEEN '00:00:00' AND '00:30:00' AND units_sold IS NOT NULL
GROUP BY timeonsite, units_sold
), 
T2 AS (
SELECT timeonsite, units_sold AS sold
FROM analytics
WHERE timeonsite BETWEEN '00:31:00' AND '01:00:00' AND units_sold IS NOT NULL
GROUP BY timeonsite, units_sold
), 
T3 AS (
SELECT timeonsite, units_sold AS sold
FROM analytics
WHERE timeonsite BETWEEN '01:00:00' AND '01:30:00' AND units_sold IS NOT NULL
GROUP BY timeonsite, units_sold
), 
T4 AS (
SELECT timeonsite, units_sold AS sold
FROM analytics
WHERE timeonsite BETWEEN '01:31:00' AND '02:00:00' AND units_sold IS NOT NULL
GROUP BY timeonsite, units_sold
)
SELECT 'T1 | 30minutes' AS timing, SUM(sold) AS units_sold FROM T1
UNION 
SELECT 'T2 | 1Hour'     AS timing, SUM(sold) AS units_sold FROM T2
UNION 
SELECT 'T3 | 1.5Hour'   AS timing, SUM(sold) AS units_sold FROM T3
UNION 
SELECT 'T4 | 2Hour'     AS timing, SUM(sold) AS units_sold FROM T4
Order BY timing

Answer:
it seems that there is a negative corrlation between time spent browsing the website and the total ammount of pieces sold. 
