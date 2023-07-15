# MandySQLToolbox
Collection of useful SQL 

## Frequency, the occurrence of each value in the field, count the number of records

The number of rows can be found with count(*) and profiled field is in the GROUP BY

<ins>SELECT column_name, count(*) as quantity 
FROM table_name
GROUP BY 1 ;</ins>

**We can use count(*) when we want the number of records but use count distinct to find out how many unique items there are**

## Finding duplicates 

<ins>SELECT count(*)
FROM
(
  SELECT column_a, column_b, column_c,.....
  ,count(*) as records
  FROM .....
  GROUP BY 1,2,3,...
) a
WEHRE records>1;</ins>

This will tell us whether there are any cases of duplicates. If the query returns 0, no duplicate.

## Full details on which records have duplicates

<ins>SELECT *
FROM
(  
  SELECT column_a, column_b, column_c,.....
  ,count(*) as records
  FROM ...
  GROUP BY 1,2,3,..
)a
WHERE records = 2;</ins>
