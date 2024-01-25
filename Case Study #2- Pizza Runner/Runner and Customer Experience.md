## Solution - Runner and Customer Experience
1. How many runners signed up for each 1 week period?
````sql
SELECT WEEK(registration_date, 1) AS week_number,COUNT(runner_id) AS registration_count
FROM runners
GROUP BY week_number
ORDER BY week_number;
````
**Answer**
|week_number|registration_count|
|-----------|------------------|
|0          |2                 |
|1          |1                 |
|2          |1                 |

- On the week of Jan 2021, 2 runners signed up
- Second week and Third week, 1 runner registered

2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?;
```sql
With Runner_time AS (SELECT runner_id, TIME_TO_SEC(TIMEDIFF(pickup_time, order_time))/60 AS Time_difference
FROM customer_orders
JOIN runner_orders ON customer_orders.order_id = runner_orders.order_id
WHERE distance <> "null")

SELECT runner_id, FLOOR(AVG(Time_difference)) AS Average_time
FROM Runner_time
GROUP BY runner_id;
```
**Answer**
|runner_id  |Average_time      |
|-----------|------------------|
|1          |15                |
|2          |23                |
|3          |10                |

- On Average, it took Runner 1 about 15minutes
- It took Runner 2 about 23minutes
- It took Runner 3 about 10minutes 

3. Is there any relationship between the number of pizzas and how long the order takes to prepare?;
````sql
SELECT customer_orders.order_id, COUNT(customer_orders.order_id) AS num_of_pizza_per_order, FLOOR(TIME_TO_SEC(TIMEDIFF(pickup_time, order_time))/60) AS Time_in_minutes
FROM customer_orders
JOIN runner_orders ON customer_orders.order_id = runner_orders.order_id
WHERE pickup_time <> "null"
GROUP BY customer_orders.order_id, Time_in_minutes;
````
**Answer**
|order_id  |num_of_order_per_order|Time_in_minutes     |
|----------|----------------------|--------------------|
|1         |1                     |10                  |
|2         |1                     |10                  |
|3         |2                     |21                  |
|4         |3                     |29                  |
|5         |1                     |10                  |
|7         |1                     |10                  |
|8         |1                     |20                  |
|10        |2                     |15                  |

- On Average, it takes about 10minutes to prepare one pizza.
- For two or more pizza you can expect about 20minutes or 15minutes on a good day.

4. What was the average distance travelled for each customer?
```sql
SELECT customer_id, ROUND(AVG(distance),1) AS Average_distance
FROM customer_orders
JOIN runner_orders ON customer_orders.order_id = runner_orders.order_id
WHERE pickup_time <> "null"
GROUP BY Customer_id;
```
**Answer**
|customer_id  |Average_distance  |
|-------------|------------------|
|101          |20                |
|102          |16.7              |
|103          |23.4              |
|104          |10                |
|105          |25                |

- 104 residence is the closest to Pizza Runner headquarters.
  
5. What was the difference between the longest and shortest delivery times for all orders?
```sql
SELECT MAX(duration) as longest_delivery, MIN(duration) as shortest_delivery, MAX(duration)- MIN(duration) as diff_in_delivery
FROM runner_orders 
WHERE pickup_time <> "null";
```
**Answer**
|Longest_delivery|shortest_delivery|diff_in_delivery|
|----------------|-----------------|----------------|
|40minutes       |10minutes        |30minutes       |

- The difference in delivery is 30minutes. Thats definitely something to look into.

7. What was the average speed for each runner for each delivery and do you notice any trend for these values?
 ```sql
SELECT runner_id, order_id, ROUND(AVG(distance /(duration/60)),1) AS Average_speed_kph
FROM runner_orders
WHERE pickup_time <> "null"
GROUP BY runner_id, order_id
ORDER BY runner_id;
```
**Answer**
|runner_id |order_id        |Average_speed_kph   |
|----------|----------------|--------------------|
|1         |1               |37.5                |
|1         |2               |44.4                |
|1         |3               |40.2                |
|1         |10              |60                  |
|2         |4               |35.1                |
|2         |7               |60                  |
|2         |8               |93.6                |
|3         |5               |40                  |

(Average speed = Distance in km / Duration in hour)

8. What is the successful delivery percentage for each runner?
```sql
SELECT runner_id, (SUM(CASE
WHEN pickup_time <> "null" THEN 1 ELSE 0
END)/ COUNT(runner_id)) * 100 AS successful_orders
FROM runner_orders
GROUP BY runner_id;
```
**Answer**
|runner_id    |successful_orders |
|-------------|------------------|
|1            |100               |
|2            |75                |
|3            |50                |

- Runner 1 has 100% successful delivery.
- Runner 2 has 75% successful delivery.
- Runner 3 has 50% successful delivery.

