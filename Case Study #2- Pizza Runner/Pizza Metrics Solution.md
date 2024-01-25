## Solution - Pizza Metrics
1. How many pizzas were ordered?
````sql
SELECT COUNT(pizza_id) AS num_of_orders
FROM customer_orders;
````
**Solution** 
|num_of_orders|
|-------------|
|14           |

- 14 pizzas were ordered

2. How many unique customer orders were made?
````sql
  SELECT COUNT(DISTINCT order_id) AS unique_orders
  FROM customer_orders;
````
**Solution** 
|unique_orders|
|-------------|
|10           |

- There were 10 unique customer orders.

3 How many successful orders were delivered by each runner?
````sql 
  SELECT runner_id, COUNT(order_id) AS Successful_orders
  FROM runner_orders 
  WHERE pickup_time <> 'null'
  GROUP BY runner_id;
````
**Solution** 
|runner_id    |sucessful_orders|
|-------------|----------------|
|1            |4               |
|2            |3               |
|3            |1               |

-Runner 1 had 4 successful orders
-Runner 2 had 3 successful orders
-Runner 3 had 1 successful orders

 4. How many of each type of pizza was delivered?
````sql
  USE pizza_runner;
  SELECT pizza_name, COUNT(customer_orders.pizza_id) AS num_of_pizza
  FROM customer_orders
  JOIN pizza_names ON customer_orders.pizza_id = pizza_names.pizza_id
  JOIN runner_orders ON customer_orders.order_id = runner_orders.order_id
  WHERE pickup_time <> 'null'
  GROUP BY pizza_name;
````
  **Solution** 
|pizza_name    |num_of_pizza    |
|------------- |----------------|
|Meatlovers    |9               |
|Vegeterian    |3               |

- There were 9 Meatlovers pizza ordered
- There were 3 Vegeterian pizza ordered

5. How many Vegetarian and Meatlovers were ordered by each customer?
````sql
  SELECT customer_id, pizza_name,COUNT(pizza_name) AS num_of_pizza
  FROM customer_orders AS c 
  JOIN pizza_names ON c.pizza_id = pizza_names.pizza_id
  GROUP BY customer_id, pizza_name
  ORDER BY customer_id;
  ````
**Solution** 
|runner_id    |sucessful_orders|num_of_pizza|
|-------------|----------------|------------|
|101          |Meatlovers      |2           |
|101          |Vegeterians     |1           |
|102          |Meatlovers      |2           |
|102          |Vegetarian      |1           |
|103          |Meatlovers      |3           |
|103          |Vegetarian      |1           |
|104          |Meatlovers      |3           |
|105          |Vegetraian      |1           |

- Customer 101 ordered 2 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 102 ordered 2 Meatlovers pizzas and 1 Vegetarian pizzas.
- Customer 103 ordered 3 Meatlovers pizzas and 1 Vegetarian pizza.
- Customer 104 ordered 1 Meatlovers pizza.
- Customer 105 ordered 1 Vegetarian pizza.

6. What was the maximum number of pizzas delivered in a single order?
```sql
  SELECT customer_orders.order_id, COUNT(pizza_id) AS num_of_orders
  FROM customer_orders
  JOIN runner_orders ON customer_orders.order_id = runner_orders.order_id
  WHERE pickup_time <> 'null'
  GROUP BY order_id
  ORDER BY num_of_orders DESC
  LIMIT 1;
```
 **Solution** 
|order_id     |num_of_orders |
|-------------|--------------|
|4            |3             |

- The maximum number of pizza in a single order was 3.

7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
````sql
  SELECT customer_id, 
  COUNT(pizza_id) AS total_pizzas,
  SUM(CASE WHEN exclusions = 'null' OR exclusions = '' THEN 1 ELSE 0 END) AS no_changes,
  SUM(CASE WHEN exclusions <> '' AND exclusions <> 'null' THEN 1 ELSE 0 END) AS at_least_one_change
FROM 
  customer_orders AS c
JOIN 
  runner_orders ON c.order_id = runner_orders.order_id
WHERE 
  pickup_time <> 'null'
GROUP BY 
  customer_id;
````
**Solution** 
|customer_id  |total_pizzas    |no_changes    |at_least_one_change| 
|-------------|----------------|--------------|-------------------|
|101          |2               |2             |0                  |
|102          |3               |3             |0                  |
|103          |3               |0             |3                  |
|104          |3               |2             |1                  |
|105          |1               |1             |0                  |

- Customer 101 and 102 had no changes to their pizza
- Customer 103, 104 and 105 had at least one change to their pizza.

8. How many pizzas were delivered that had both exclusions and extras?
```sql
SELECT COUNT(pizza_id) AS total_pizzas, exclusions, extras
FROM customer_orders AS c
JOIN runner_orders ON c.order_id = runner_orders.order_id
WHERE 
pickup_time <> 'null' AND exclusions <> 'null' AND exclusions <> ''
AND extras <> 'null' AND extras <> ''
GROUP BY exclusions, extras;
```
**Solution** 
|total_pizzas |exclusions      |extras      |
|-------------|----------------|------------|
|1            |2,6             |1,4         |

- Only one pizza had both exclusions and extras.

 9. What was the total volume of pizzas ordered for each hour of the day?
```sql
SELECT HOUR(order_time) AS order_hour, COUNT(pizza_id) AS total_volume
FROM customer_orders
GROUP BY order_hour
ORDER BY order_hour;
```
**Solutions**
|order_hour   |total_volume    |
|-------------|----------------|
|11           |1               |
|13           |3               |
|18           |3               |
|19           |1               |
|21           |3               |   
|23           |3               |

- The lowest volume of pizza was ordered at 11 am
- Whereas the highest volume of pizza was seen during the day through to 11pm

10. What was the volume of orders for each day of the week?
```sql
SELECT DAYNAME(order_time) AS new_date, COUNT(order_id) AS total_volume
FROM customer_orders
GROUP BY new_date
ORDER BY new_date;
```

**Solutions**
|new_date     |total_volume    |
|-------------|----------------|
|Friday       |1               |
|Saturday     |5               |
|Thursday     |3               |
|Wednesday    |5               |

- Wednesday and Saturday had the highest volume of pizza orders
- Thursday had the second highest and Friday had the lowest volume (suprisingly, who doesn't love to
 end the week with a nice snack!)
