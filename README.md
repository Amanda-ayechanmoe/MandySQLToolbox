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

## 3 ways to remove duplicate 

1. DISTINCT\
   <ins> SELECT DISTINCT a.customer_id, a.customer_name, a.customer_email
   FROM customers a
   JOIN transactions b on a.customr_id = b.customer_id </ins>
2. GROUP BY\
    <ins> SELECT a.customer_id, a.customer_name, a.customer_email
    FROM customers a
    JOIN transactions b on a.customr_id = b.customer_id
    GROUP BY 1,2,3 </ins>
3. To perform an aggregation that returns one row per entity\
   <ins> SELECT customer_id
   ,min(transaction_date) as first_transaction_date
   ,max(transaction_date) as last_transaction_date
   ,count(*) as total orders
   FROM table
   GROUP BY customer_id </ins>

## Check missing data by comparing values in two tables

We might expect that the customer in the transactions table also has a record in the customer table. To check this, query the table using a LEFT JOIN and add a WHERE condition to find the customers that do not exist in the second table.

<ins> SELECT distinct a.customer_id
FROM transactions a
LEFT JOIN customers b on a.customer_id = b.customer_id
WHERE b.customer_id is null ; </ins>


