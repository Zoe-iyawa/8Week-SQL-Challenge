# Case Study #1: Danny's Diner 
![image](https://8weeksqlchallenge.com/images/case-study-designs/1.png)

## Introduction
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.
Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business.

## Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.
He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.
Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

Danny has shared with you 3 key datasets for this case study:
1) sales
2) menu
3) members

## Entity Relationship Diagram

![image](https://user-images.githubusercontent.com/81607668/127271130-dca9aedd-4ca9-4ed8-b6ec-1e1920dca4a8.png)

## Question and Solution

1) What is the total amount each customer spent at the restaurant ?

````sql 
SELECT customer_id, sum(price)
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id;
````
Answer:
| customer_id | Total_sum |
| -----------| ---------- |
| A          | 76         |
| B          | 74         |
| C          | 36         |

2) How many days has each customer visited the restaurant?

````sql
SELECT customer_id, COUNT(DISTINCT order_date) AS Total_visit
FROM sales
GROUP BY customer_id;

````
Answer:
| customer_id | Total_visit |
| -----------| ----------   |
| A          | 4            |
| B          | 6            |
| C          | 2            |

3) What was the first item from the menu purchased by each customer?;

````sql
With Table_1 AS(SELECT customer_id,order_date,product_name, RANK()OVER(PARTITION BY customer_id ORDER BY order_date) AS Product_rank
FROM sales
JOIN menu ON sales.product_id = menu.product_id)

SELECT DISTINCT customer_id, product_name
FROM Table_1
Where Product_rank = 1;

````
Answer:
| customer_id | Total_visit |
| -----------| ----------   |
| A          | sushi        |
| A          | curry        |
| B          | curry        |
| C          |ramen         |
- Customer A placed an order for both curry and sushi simultaneously, making both product the first choice.
- Customer B's first order is curry.
- Customer C's first order is ramen.


4) What is the most purchased item on the menu and how many times was it purchased by all customers?;

````sql
SELECT product_name, count(product_name) AS Num_of_purchase
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY  product_name
ORDER BY Num_of_purchase DESC
LIMIT 1;
````
Answer
| product_name | Num_of_purchase |
| -----------  | ----------------|
| ramen        |8                |


5) Which item was the most popular for each customer?;

````sql
WITH most_popular AS (SELECT customer_id, product_name, COUNT(product_name) AS num_of_purchase, RANK ()OVER(PARTITION BY customer_id ORDER BY COUNT(product_name) DESC) AS Popular_purchase
FROM sales
JOIN menu ON sales.product_id = menu.product_id
GROUP BY customer_id, product_name)

SELECT customer_id, product_name
FROM most_popular
WHERE Popular_purchase =1;
````
Answer 
| customer_id | product_name |
| -----------| ----------   |
| A          | ramen        |
| B          | curry        |
| B          | sushi        |
| B          |ramen         |
| C          |ramen         |

- Customer A and C's favourite item is ramen.
- Customer B is  fan of all three items.

6) Which item was purchased first by the customer after they became a member?;

````sql
WITH Joined_as_member AS (SELECT members.customer_id, product_name, join_date, order_date, RANK () OVER(PARTITION BY customer_id ORDER BY order_date ) AS First_purchase
FROM sales
JOIN members ON sales.customer_id = members.customer_id
JOIN menu ON sales.product_id = menu.product_id
HAVING  order_date >= join_date)

SELECT customer_id, product_name
FROM Joined_as_member
WHERE First_purchase = 1;
````
Answer
| customer_id | product_name |
| -----------| ----------   |
| A          |curry         |
| B          | sushi        |

- Customer A's first order as a member is ramen.
- Customer B's first order as a member is sushi.

7) Which item was purchased just before the customer became a member?;

````sql
WITH Non_member AS (SELECT sales.customer_id, product_name, join_date, order_date, RANK () OVER(PARTITION BY customer_id ORDER BY order_date DESC) AS Last_purchase
FROM sales
JOIN members ON sales.customer_id = members.customer_id
JOIN menu ON sales.product_id = menu.product_id
HAVING  order_date < join_date)

SELECT customer_id, product_name
FROM Non_member
WHERE Last_purchase = 1;

````
Answer

| customer_id | product_name |
| -----------| --------------|
| A          | sushi        |
| A          | curry        |
| B          | sushi        |

- A purchased both sushi and curry just before becoming a member
- B purchased sushi just before becoming a member

8) What is the total items and amount spent for each member before they became a member?;

````sql
WITH Non_member AS (SELECT members.customer_id, product_name, join_date, order_date,price, RANK () OVER(PARTITION BY customer_id ORDER BY order_date ) AS First_purchase
FROM sales
JOIN members ON sales.customer_id = members.customer_id
JOIN menu ON sales.product_id = menu.product_id
HAVING  order_date < join_date)

SELECT customer_id, COUNT(product_name) AS total_purchases, SUM(price) AS total_amount
FROM Non_member
GROUP BY customer_id;
````
Answer
| customer_id | total_purchases | total_amount|
|-------------|------------------|-------------|
|A            |2                 |25           |
|B            |3                 |40           |


9) If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?;

````sql
With Table_1 AS (SELECT customer_id, product_name, price, 
CASE
WHEN product_name = "sushi" THEN Price * 20 
ELSE Price * 10
END AS Points
FROM sales
INNER JOIN menu ON sales.product_id = menu.product_id)

SELECT customer_id, SUM(Points) AS total_num_points
From Table_1
GROUP BY customer_id;
````
Answer

| customer_id | total_num_points |
| -----------| ------------------|
| A          | 860               |
| B          | 940               |
| B          | 360               |



