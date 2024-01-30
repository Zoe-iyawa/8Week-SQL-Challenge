# Case study 2 - Pizza Runner
## Data cleaning and Transformation
### Table: customer_orders
From the `customer_orders` table, there are 
- In the `exclusions` column, there are blank spaces ' ' and null values. 
- In the `extras` column, there are blank spaces ' ' and null values.
We going to clean the table by:
- Create a temporary table with all the columns
- Remove null values in `exlusions` and `extras` columns and replace with blank space ' '.

````sql
CREATE TEMPORARY TABLE new_customer_orders AS 
SELECT order_id, customer_id,pizza_id, 
CASE 
WHEN exclusions IS NULL OR exclusions = ' ' THEN ' ' 
ELSE exclusions 
END AS exclusions,
CASE 
WHEN extras IS NULL OR extras = ' ' THEN ' '
ELSE extras
END AS extras, order_time
FROM customer_orders;
````

****
### Table: runner_orders
From the `runner_orders` table, there are 
- In the `exclusions` column, there are blank spaces ' ' and null values. 
- In the `extras` column, there are blank spaces ' ' and null values

We going to clean the table by:
- In `pickup_time` column, remove nulls and replace with blank space ' '.
- In `distance` column, remove "km" and nulls and replace with blank space ' '.
- In `duration` column, remove "minutes", "minute", "mins" and nulls and replace with blank space ' '.
- In `cancellation` column, remove NULL and null and and replace with blank space ' '.

```sql
DROP TABLE IF EXISTS new_runner_orders;
CREATE TEMPORARY TABLE new_runner_orders AS 
SELECT order_id, runner_id, 
CASE 
WHEN pickup_time = "null" THEN " "
ELSE STR_TO_DATE(pickup_time, '%Y-%m-%d %H:%i:%s')
END AS pickup_time,
CASE 
WHEN distance = 'null' THEN ' '
WHEN distance LIKE '%km' THEN TRIM('km' FROM distance)
ELSE distance 
END AS distance, 
CASE 
WHEN duration = "null" THEN ' '
WHEN duration LIKE '%mins' THEN TRIM('mins' FROM duration)
WHEN duration LIKE '%minutes' THEN TRIM('minutes' FROM duration)
WHEN duration LIKE '%minute' THEN TRIM('minute' FROM duration)
ELSE duration 
END AS duration,
CASE 
WHEN cancellation = "null" OR cancellation IS NULL THEN " "
ELSE cancellation
END AS cancellation
FROM runner_orders;
```

Then, we alter the `pickup_time`, `distance` and `duration` columns to the correct data type.

```sql
ALTER TABLE new_runner_orders
MODIFY COLUMN pickup_time DATETIME,
MODIFY COLUMN distance FLOAT,
MODIFY COLUMN duration INTEGER;
```

This is how the clean `customer_orders` table looks like.
![image](


This is how the clean `runner_orders` table looks like.
