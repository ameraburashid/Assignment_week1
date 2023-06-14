Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

--Countries that has the highest transaction revenues 
SELECT country, SUM(totalTransactionRevenue) AS total_revenue
FROM all_sessions
where totalTransactionRevenue is not null
GROUP BY country
ORDER BY total_revenue DESC
LIMIT 3;

-- top cities with the highest transaction revenues 
SELECT country, city, SUM(totalTransactionRevenue) AS total_revenue
FROM all_sessions
WHERE country in ('United States', 'Israel', 'Australia')
GROUP BY country, city
HAVING SUM(totalTransactionRevenue) >= 0
ORDER BY total_revenue DESC
LIMIT 3;

Answer: 
-top 3 countries are (United states, then israel, then australia)
- top 3 cities in United states are  (not available in demo dataset, San Francisco, Sunnyvale) NOTE: one of the cities is missing data. 
- only city in Israel is Tel Aviv-Yafo
- only city in Australia is Sydney




**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:

SELECT 
	als.country,
	CASE
		WHEN als.city = 'not available in demo dataset' THEN 'N/A'
		ELSE als.city
	END AS city,
	CAST(AVG(an.units_sold) AS DECIMAL(10,2)) AS averageProductCount
FROM all_sessions AS als
JOIN analytics AS an ON als.visitId = an.visitId
WHERE an.units_sold IS NOT NULL
GROUP BY als.country, als.city
ORDER BY als.country;


Answer:
"country"	"city"	"averageproductcount"
"Australia"	"N/A"	1.00
"Austria"	"N/A"	1.00
"Belgium"	"N/A"	1.00
"Bulgaria"	"N/A"	1.50
"Canada"	"N/A"	2.50
"Canada"	"Toronto"	2.33
"Chile"	"Santiago"	1.00
"Colombia"	"Bogota"	1.00
"Denmark"	"N/A"	1.00
"Egypt"	"N/A"	1.00
"France"	"N/A"	1.00
"France"	"Paris"	1.00
"Germany"	"Berlin"	1.00
"Germany"	"Munich"	1.00
"Hong Kong"	"(not set)"	1.00
"Hong Kong"	"Hong Kong"	1.00
"India"	"Hyderabad"	1.50
"India"	"N/A"	1.00
"Indonesia"	"N/A"	1.00
"Ireland"	"Dublin"	1.00
"Israel"	"Tel Aviv-Yafo"	1.00
"Japan"	"N/A"	1.40
"Maldives"	"N/A"	1.00
"Mexico"	"N/A"	1.50
"Netherlands"	"N/A"	1.00
"Romania"	"N/A"	1.00
"Singapore"	"Singapore"	1.00
"South Korea"	"N/A"	1.00
"Sweden"	"N/A"	1.00
"Switzerland"	"N/A"	1.00
"Switzerland"	"Zurich"	1.00
"Taiwan"	"(not set)"	1.00
"Taiwan"	"N/A"	1.00
"Thailand"	"Bangkok"	1.00
"Thailand"	"N/A"	1.00
"United Kingdom"	"London"	1.00
"United States"	"(not set)"	1.00
"United States"	"Ann Arbor"	1.00
"United States"	"Atlanta"	1.00
"United States"	"Austin"	1.00
"United States"	"Chicago"	5.00
"United States"	"Dallas"	1.00
"United States"	"Detroit"	1.00
"United States"	"Houston"	2.50
"United States"	"Kirkland"	1.00
"United States"	"Mountain View"	1.56
"United States"	"New York"	3.82
"United States"	"N/A"	2.04
"United States"	"Palo Alto"	1.00
"United States"	"Pittsburgh"	4.00
"United States"	"San Bruno"	1.00
"United States"	"San Francisco"	1.36
"United States"	"San Jose"	1.00
"United States"	"Seattle"	1.44
"United States"	"Sunnyvale"	2.26
"United States"	"Washington"	1.00
"Vietnam"	"N/A"	1.00

**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:

with table_1 as (
SELECT
	a.country as country,
	a.city as city,
	a.v2ProductCategory as productcategory,
	SUM(an.units_sold) as totalUnitsSold
FROM all_sessions AS a
JOIN analytics AS an ON a.visitId = an.visitId
JOIN products AS p ON a.productSKU = p.SKU
WHERE a.country != '(not set)'
	AND a.city != '(not set)'
	AND a.v2ProductCategory != '(not set)'
GROUP BY a.country, a.city, a.v2ProductCategory
HAVING SUM(an.units_sold) IS NOT NULL
ORDER BY a.country, a.city, totalUnitsSold DESC
)

SELECT country, city, productcategory, totalunitssold
FROM table_1
WHERE city != 'not available in demo dataset'
ORDER BY country, city




Answer:
-- if we limit the search to have totalunitssold to more than 10 then we see that the unitedstates is the most spender 
-- NewYork, mountainview, chicago, and san-fransisco all have categories of clothing and bodywears 
-- seatle and sunnyvale's category was in housewares/ home items  



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

SELECT 
	als.v2productname,
	als.country,
	sum(ana.units_sold) as summing 
FROM all_sessions as als 
JOIN analytics as ana
	ON als.visitid = ana.visitid 
WHERE ana.units_sold is not null
GROUP by als.v2productname, als.country
ORDER BY summing desc



Answer:
this query returns 132 rows which we can skim through quickly to get the most units sold per country and their respected product name
-- it looks like there is a big demand on google products 

"v2productname"	"country"	"unitssold"
"Google Alpine Style Backpack"	"United States"	227
"Waze Pack of 9 Decal Set"	"Canada"	10
"Android RFID Journal"   	"Egypt"	        9
"Google Lunch Bag"      	"Japan"		7
"YouTube Hard Cover Journal"	"Israel"	5
"Google Women's Short Sleeve Hero Tee Sky Blue"	"Sweden"	4
"Android Men's Vintage Tank"	"Netherlands"	4
"YouTube Men's 3/4 Sleeve Henley"	"Switzerland"	4
"YouTube Men's Vintage Tank"	"Bulgaria"	3
"26 oz Double Wall Insulated Bottle"	"Thailand"	3
"8 pc Android Sticker Sheet"	"Romania"	2
"Google Laptop Backpack"	"Ireland"	2
"YouTube Men's 3/4 Sleeve Henley"	"Colombia"	2
"YouTube RFID Journal"	"Vietnam"	2
"Android Sticker Sheet Ultra Removable"	"Australia"	2
"Nest® Learning Thermostat 3rd Gen-USA - White"	"Hong Kong"	2
"Nest® Cam Indoor Security Camera - USA"	"Mexico"	2
"Google Men's Performance Full Zip Jacket Black"	"Maldives"	2
"YouTube Custom Decals"		"India"		1
"Google Women's Short Sleeve Hero Tee Black"	"Taiwan"	1
"Android Lunch Kit"		"France"	1
"Galaxy Screen Cleaning Cloth"	"South Korea"	1
"Google Car Clip Phone Holder"	"Austria"	1
"Google Doodle Decal"	"Germany"	1
"Google Men's Short Sleeve Performance Badge Tee Navy"	"Singapore"	1
"Google Rucksack"	"Indonesia"	1
"26 oz Double Wall Insulated Bottle"	"Denmark"	1


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT
    als.country,
    als.city,
    SUM(an.revenue) AS totalRevenue
FROM all_sessions AS als
JOIN analytics AS an USING (visitId)
WHERE an.revenue IS NOT NULL
GROUP BY als.country, als.city
ORDER BY totalRevenue DESC;


Answer:
"country"           "city"                          "totalRevenue"
"United States"     "not available in demo dataset"  1198.97
"United States"     "New York"                      1142.73
"United States"     "Sunnyvale"                     672.15
"United States"     "Mountain View"                 509.73
"United States"     "Seattle"                       358.00
"United States"     "San Francisco"                 312.69
"United States"     "Chicago"                       306.00
"United States"     "San Jose"                      153.00
"United States"     "Palo Alto"                     151.00
"Israel"            "Tel Aviv-Yafo"                 32.99
"Switzerland"       "Zurich"                        16.99


the most known city to generate the most revenue is newyork 
the united states generated 98% of the revenue 







