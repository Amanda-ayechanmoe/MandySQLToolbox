# MandySQLToolbox
Collection of useful SQL 

## Frequency, the occurrence of each value in the field, count the number of records

The number of rows can be found with count(*) and profiled field is in the GROUP BY

'SELECT column_name, count(*) as quantity 
FROM table_name
GROUP BY 1'

** We can use count(*) when we want the number of records but use count distinct to find out how many unique items there are**
