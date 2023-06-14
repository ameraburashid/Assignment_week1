What are your risk areas? Identify and describe them.

Using the all_sessions table. The all_sessions table cotained a lot of NULL values which could not bring correct analysis 

there is not enough data to support selling outside the USA 


QA Process:
Describe your QA process and include the SQL queries used to execute it.


THERE was a difficulty assessing revenue type questions due to the lack of data in the columns. most of the data is null 

SELECT count(*) FROM all_sessions;
SELECT count(*) FROM all_sessions where productrevenue is null;

SELECT count(*) FROM analytics;
SELECT COUNT(*) FROM analytics WHERE revenue is null;
-- switched the use from productrevenue/revenue to units_sold 

--fullvisitorid column should have a unique value for each customer


SELECT fullvisitorid, count(*) FROM all_sessions  GROUP BY fullvisitorid HAVING count(*) > 1

-- fullvisitorid had duplicates
-- customers bought multiple products and thus the fullvisitorid is duplicated with different product

SELECT fullvisitorid, v2productname, count(*) FROM all_sessions  
GROUP BY fullvisitorid, v2productname HAVING count(*) > 1

-- after making a separate visitor table, the visitors had duplicates 
SELECT fullvisitorid, count(*) from visitor
group by fullvisitorid
HAVING count(*) > 1
