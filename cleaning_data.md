What issues will you address by cleaning the data?

1- some columns contain null values entirely or almost entirely.consider deleting the columns 
2- the prices listed in the data are very large and should be reduced
3- columns with product names had a space before the first character 
4- columns with the title of %time% had the values in intigers and not time format
5- over half the data in the city column did not have a valid city name. 



Queries:
Below, provide the SQL queries you used to clean your data.
1- to remove columns where all the values were null if wanted to remove them

ALTER TABLE table_name
DROP COLUMN column_name;

2- to change the prices in the data 

ALTER TABLE your_table
ALTER COLUMN column TYPE DECIMAL(18, 6) -- for 2 decimal place change 6 to 2;

UPDATE your_table
SET column_name = column_name / 1000000.0



3- to remove the space before the first character use the TRIM function 
UPDATE your_table
SET column_name = TRIM(LEADING ' ' FROM column_name)



4- create new tables with appropriate time format and remove the old ones 

-- check for time format 
SELECT old_column_name from your_table

--create a new column for time and make it into a time format

ALTER TABLE your_table ADD COLUMN new_column_name TIME;
UPDATE your_table SET new_column_name = time '00:00:00' + (interval '1 second' * time::integer);

-- check for time above 24h and bellow 00:00:00
SELECT new_column_name FROM your_table
where new_column_name is null or new_column_name > '23:59:59' or new_column_name < '00:00:00'
order by new_column_name desc

-- remove old table 
ALTER TABLE your_table DROP COLUMN old_column_name;

-- Rename the column_name column to old_column_name
ALTER TABLE your_table
RENAME COLUMN column_name TO old_column_name;

5- 
-- MAKE the missing city name as the country if applicable 
UPDATE your_table
SET CASE
	WHERE city is '(not set)' then country
	WHERE city is null then country
	ELSE city 
    END; 




