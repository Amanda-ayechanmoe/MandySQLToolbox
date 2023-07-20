# MandySQLToolbox

## Frequency, the occurrence of each value in the field, count the number of records
****
The number of rows can be found with count(*) and profiled field is in the GROUP BY
``` SQL
SELECT column_name, count(*) as quantity 
FROM table_name
GROUP BY 1 ;
```
**We can use count(*) when we want the number of records but use count distinct to find out how many unique items there are**

## Finding duplicates 
****
``` SQL
SELECT count(*)
FROM
(
  SELECT column_a, column_b, column_c,.....
  ,count(*) as records
  FROM .....
  GROUP BY 1,2,3,...
) a
WEHRE records>1;</ins>
```
This will tell us whether there are any cases of duplicates. If the query returns 0, no duplicate.

## Full details on which records have duplicates
****
``` SQL
SELECT *
FROM
(  
  SELECT column_a, column_b, column_c,.....
  ,count(*) as records
  FROM ...
  GROUP BY 1,2,3,..
)a
WHERE records = 2;
```

## 3 ways to remove duplicate 
****
1. DISTINCT
   ``` SQL
   SELECT DISTINCT a.customer_id, a.customer_name, a.customer_email
   FROM customers a
   JOIN transactions b on a.customr_id = b.customer_id
   ```
2. GROUP BY
    ``` SQL
    SELECT a.customer_id, a.customer_name, a.customer_email
    FROM customers a
    JOIN transactions b on a.customr_id = b.customer_id
    GROUP BY 1,2,3
    ```
3. To perform an aggregation that returns one row per entity
   ``` SQL
   SELECT customer_id
   ,min(transaction_date) as first_transaction_date
   ,max(transaction_date) as last_transaction_date
   ,count(*) as total orders
   FROM table
   GROUP BY customer_id
   ```

## Check missing data by comparing values in two tables
****
We might expect that the customer in the transactions table also has a record in the customer table. To check this, query the table using a LEFT JOIN and add a WHERE condition to find the customers that do not exist in the second table.

```sql
SELECT distinct a.customer_id
FROM transactions a
LEFT JOIN customers b on a.customer_id = b.customer_id
WHERE b.customer_id is null ;
```

## Shaping Data - manipulating the way data is represented in columns and rows
****
### 1. Pivoting with CASE statements and aggregation
It is a way to summarize data sets by arranging the data into rows, according to the value of an attribute, and columns, according to the values of another attribute.

```SQL
SELECT order_date
,sum(CASE WHEN product = 'shirt' then order_amount
          ELSE 0
          END) as shirts_amount
,sum(CASE WHEN product = 'shoes' then order_amount
          ELSE 0
          END) as shoes_amount
,sum(CASE WHEN product = 'hat' then order_amount
          ELSE 0
          END) as hats_amount
FROM orders
GROUP BY 1;
```

| order_date | shirts_amount | shoes_amount | hats_amount |
| ----------- | ----------- |----------- | ----------- |
| 2020-05-01 | 5268.56 | 1211.65 | 562.25 |
| 2020-05-01 | 5533.84 | 522.25 | 325.62 |

With Sum aggregation, we can optionally use "else 0" to avoid nulls in the result set. 
With COUNT or COUNT DISTINCT, we should not include an ELSE statement, as doing so would inflate the result set. This is because the database won't count null but it will count a substitute value such as zero.
Pivoting data with a combination of aggregation and CASE statements works well when there are a finite number of items to pivot.

### 2. Unpivoting with UNION Statements
Move data stored in columns into rows.

| Country | year_1980 | year_1990 | year_2000 | year_2010 |
| ----------- | ----------- |----------- | ----------- | ----------- 
| Canada | 24,593 | 27,791 | 31,100 | 34,207 |
| Mexico | 68,347 | 84,634 | 99,775 | 114,061 |
| United States | 249,623 | 282,162 | 325.62 | 309,326 |

UNION is a way to combine data sets from multiple queries into a single result set. There are two forms UNION and UNION ALL. UNION removes duplicates from the result set whereas UNION ALL retains all records whether duplicates or not. UNION ALL is faster. The number of columns in each component query must match. The data type must match or be compatible (integer and float can be mixed). The column name in the result set comes from the first query.

``` SQL
SELECT country 
,'1980' as year
,year_1980 as population 
FROM country_populations
  UNION ALL
SELECT country
,'1990' as year
,year_1990 as population
FROM country_populations 
  UNION ALL
SELECT country
,'2000' as year 
,year_2000 as population
FROM country_populations
  UNION ALL
SELECT country
,'2010' as year
,year_2010 as population
FROM country_populations;
```

| Country | year | population 
| ----------- | ----------- |----------- | 
| Canada | 1980 | 24,593
| Mexico | 1980 | 68,347
| United States | 1980 | 249,623 
| .... | .... | .... 

## Time Zone Conversions
****
Many databases are set to Coordinated Universal Time (UTC), the global standard used to regulate clocks and record events in this time zone.
All local time zones have UTC offset. For example, the offset for PDT (Pacific Standard Time) is UTC - 7 hours, while the offset for PST is UTC - 8 hours.

Converting from one timezone to another can be accomplished with <ins> at time zone </ins> followed by the <ins> destination time zone's abbreviation </ins>
```sql
SELECT '2020-09-01 00:00:00 -0' at time zone 'pst';
```
convert_timezone or convert_tz function
```sql
SELECT convert_timezone('pst', '2020-09-01 00:00:00 -0);
```
Check databases' documentation for the exact name and ordering of the target time zone and the source timestamp argument.

| Database | Table Name
| ----------- | ----------- 
| Postgres| pg_timezone_names
| MySQL | mysql.time_zone_names
| SQL Server | sys.time_zone_info
| Redshift | pg_timezone_names
