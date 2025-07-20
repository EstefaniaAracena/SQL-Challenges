# Case Study #2 - Pizza Runner
First we create the tables, then, we analize the data:

## Create the tables: 
### runners
```
CREATE TABLE seraphic-ripple-464915-d1.Pizza_Runner.runners(
  runner_id INTEGER,
  registration_date DATE
);

INSERT INTO seraphic-ripple-464915-d1.Pizza_Runner.runners(runner_id, registration_date)
VALUES
  (1,'2021-01-01'),
  (2,'2021-01-03'),
  (3,'2021-01-08'),
  (4,'2021-01-15');
```
### pizza_name
```
CREATE TABLE seraphic-ripple-464915-d1.Pizza_Runner.pizza_name(
  pizza_id INTEGER,
  pizza_name STRING
);

INSERT INTO seraphic-ripple-464915-d1.Pizza_Runner.pizza_name(pizza_id, pizza_name)
VALUES
(1, 'Meat Lovers'),
(2, 'Vegetarian'); 
```
### pizza_recipes
```
CREATE TABLE seraphic-ripple-464915-d1.Pizza_Runner.pizza_recipes(
  pizza_id INTEGER,
  toppings STRING
);

INSERT INTO seraphic-ripple-464915-d1.Pizza_Runner.pizza_recipes(pizza_id, toppings)
VALUES
(1, '1,2,3,4,5,6,8,10'),
(2, '4,6,7,9,11,12'); 
```
### pizza_toppings
```
CREATE TABLE seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings(
  topping_id INTEGER,
  topping_name STRING
);

INSERT INTO seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings(topping_id, topping_name)
VALUES
  (1, 'Bacon'),
  (2, 'BBQ Sauce'),
  (3, 'Beef'),
  (4, 'Cheese'),
  (5, 'Chicken'),
  (6, 'Mushrooms'),
  (7, 'Onions'),
  (8, 'Pepperoni'),
  (9, 'Peppers'),
  (10, 'Salami'),
  (11, 'Tomatoes'),
  (12, 'Tomato Sauce');
```
### customer_orders
```
CREATE TABLE seraphic-ripple-464915-d1.Pizza_Runner.customer_orders(
  order_id INTEGER,
  customer_id INTEGER,
  pizza_id INTEGER,
  exclusions STRING,
  extras STRING,
  order_date DATETIME
);

INSERT INTO seraphic-ripple-464915-d1.Pizza_Runner.customer_orders(order_id, customer_id, pizza_id, exclusions, extras, order_date)
VALUES
  (1, 101, 1, '', '', '2020-01-01 18:05:02'),
  (2, 101, 1, '', '', '2020-01-01 19:00:52'),
  (3, 102, 1, '', '', '2020-01-02 23:51:23'),
  (3, 102, 2, '', NULL, '2020-01-02 23:51:23'),
  (4, 103, 1, '4', '', '2020-01-04 13:23:46'),
  (4, 103, 1, '4', '', '2020-01-04 13:23:46'),
  (4, 103, 2, '4', '', '2020-01-04 13:23:46'),
  (5, 104, 1, 'null', '1', '2020-01-08 21:00:29'),
  (6, 101, 2, 'null', 'null', '2020-01-08 21:03:13'),
  (7, 105, 2, 'null', '1', '2020-01-08 21:20:29'),
  (8, 102, 1, 'null', 'null', '2020-01-09 23:54:33'),
  (9, 103, 1, '4', '1, 5', '2020-01-10 11:22:59'),
  (10, 104, 1, 'null', 'null', '2020-01-11 18:34:49'),
  (10, 104, 1, '2,6', '1, 4', '2020-01-11 18:34:49');
```
### runner_orders
```
CREATE TABLE seraphic-ripple-464915-d1.Pizza_Runner.runner_orders(
  order_id INTEGER,
  runner_id INTEGER,
  pickup_time STRING,
  distance STRING,
  duration STRING,
  cancellation STRING
);

INSERT INTO seraphic-ripple-464915-d1.Pizza_Runner.runner_orders(order_id, runner_id, pickup_time, distance, duration, cancellation)
VALUES
  (1, 1, '2020-01-01 18:15:34', '20', '32', ''),
  (2, 1, '2020-01-01 19:10:54', '20', '27', ''),
  (3, 1, '2020-01-03 00:12:37', '13.4', '20', NULL),
  (4, 2, '2020-01-04 13:53:03', '23.4', '40', NULL),
  (5, 3, '2020-01-08 21:10:57', '10', '15', NULL),
  (6, 3, 'null', 'null', 'null', 'Restaurant Cancellation'),
  (7, 2, '2020-01-08 21:30:45', '25', '25', 'null'),
  (8, 2, '2020-01-10 00:15:02', '23.4', '15', 'null'),
  (9, 2, 'null', 'null', 'null', 'Customer Cancellation'),
  (10, 1, '2020-01-11 18:50:20', '10', '10', 'null');
```

As you and me noticed, there are many data that is correct, such as "null" which is writen as a word, is this cases we must clean our data, let's check each table: 

### Table runners_orders
```
with runners_orders_clean as (
SELECT
  order_id, 
  runner_id, 
  case when pickup_time = '' or pickup_time = 'null' THEN NULL ELSE pickup_time END AS pickup_time, 
  case when distance = '' or distance = 'null' THEN NULL ELSE distance END AS distance, 
  case when duration = '' or duration = 'null' THEN NULL ELSE duration END AS duration, 
  case when cancellation = '' or cancellation = 'null' THEN NULL ELSE cancellation END AS cancellation, 
  FROM seraphic-ripple-464915-d1.Pizza_Runner.runner_orders)
SELECT * 
FROM runners_orders_clean
```
### Table customer_orders
```
WITH customer_orders_clean AS (
  SELECT
    order_id,
    customer_id,
    pizza_id,
    CASE WHEN exclusions = '' OR exclusions = 'null' THEN NULL ELSE exclusions END AS exclusions,
    CASE WHEN extras = '' OR extras = 'null' THEN NULL ELSE extras END AS extras,
    order_date
  FROM seraphic-ripple-464915-d1.Pizza_Runner.customer_orders)
SELECT * FROM customer_orders_clean
```
Now, we join the tables customer_orders, runners_orders and pizza_name as save it as "data": 
```
SELECT col.order_id, col.customer_id, col.pizza_id, col.exclusions, col.extras, col.order_date, roc.runner_id, roc.pickup_time, roc.distance, roc.duration, roc.cancellation, pn.pizza_name
from seraphic-ripple-464915-d1.Pizza_Runner.customer_orders_clean as col
JOIN seraphic-ripple-464915-d1.Pizza_Runner.runners_orders_clean as roc
ON  col.order_id = roc.order_id
JOIN seraphic-ripple-464915-d1.Pizza_Runner.pizza_name as pn
ON col.pizza_id = pn.pizza_id
ORDER BY order_id
```

Since the challenge has many questions (lots), we have followed the instructions and divided in sections: 

## A. Pizza Metrics
### Question 1: How many pizzas were ordered?
- Code:
```
SELECT count(*) as orders
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
```
- Result:

|orders|
|---|
|14|

### Question 2: How many unique customer orders were made?
- Code:
```
SELECT count(DISTINCT(order_id)) as unique_no_orders
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
```
- Result:

|unique_no_orders|
|---|
|10|

### Question 3: How many successful orders were delivered by each runner?
- Code:
```
SELECT runner_id, count(distinct order_id) as successful_orders
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
WHERE cancellation IS NULL
group by runner_id
```
- Result:

|runner_id|successful_orders|
|---|---|
|1|4|
|2|3|
|3|1|

### Question 4: How many of each type of pizza was delivered?
- Code:
```
SELECT pizza_name, count(pizza_name) as pizza_type
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
WHERE cancellation IS NULL
group by pizza_name
```
- Result:

|pizza_name|pizza_type|
|---|---|
|Meat Lovers|9|
|Vegetarian|3|

### Question 5: How many Vegetarian and Meatlovers were ordered by each customer?
- Code:
```
SELECT customer_id, pizza_name, count(*) as orders
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
group by customer_id, pizza_name
ORDER BY customer_id
```
- Result:

|customer_id|pizza_name|orders|
|---|---|---|
|101|Meat Lovers|2
|101|Vegetarian|1
|102|Vegetarian|1
|102|Meat Lovers|2
|103|Meat Lovers|3
|103|Vegetarian|1
|104|Meat Lovers|3
|105|Vegetarian|1

### Question 6: What was the maximum number of pizzas delivered in a single order?
- Code:
```
SELECT count(*) as max_no_single_order
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
group by pickup_time
ORDER BY max_no_single_order DESC
LIMIT 1
```
- Result:

|max_no_single_order|
|---|
|3|

### Question 7: For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
- Code:
```
SELECT 
  customer_id, 
  sum(case when exclusions IS NULL and extras IS NULL then 1 else 0 END) as no_changes,
  sum(case when exclusions IS NOT NULL or extras IS NOT NULL then 1 else 0 END) as at_least_one_changes 
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
where cancellation IS NULL
group by customer_id
```
- Result:

|customer_id|	no_changes|	at_least_one_changes|
|---|---|---|
|	101|	0|	2|
|	102|	0	|3|
|	103|	0	|3|
|	104|	0	|3|
|	105|	0|	1|

### Question 8: How many pizzas were delivered that had both exclusions and extras?
- Code:
```
SELECT 
  sum(case when exclusions IS NOT NULL AND extras IS NOT NULL then 1 else 0 END) as no_pizza_exclusions_and_extras
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
where cancellation IS NULL
```
- Result:

|no_pizza_exclusions_and_extras|
|---|
|1|

### Question 9: What was the total volume of pizzas ordered for each hour of the day?
- Code:
```
SELECT extract(hour from order_date) as hour, count(*) as pizzas
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
group by hour
order by hour
```
- Result:

|hour	|pizzas|
|---|---|
|11	|1|
|13	|3|
|18	|3|
|19	|1|
|21	|3|
|23	|3|

### Question 10: What was the volume of orders for each day of the week?
- Code:
```
SELECT extract(day from order_date) as day, count(*) as pizzas
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
group by day
order by day
```
- Result:

|day|	pizzas|
|---|---|
|	1|	2|
|	2|	2|
|	4|	3|
|	8|	3|
|	9|	1|
|	10|	1|
|	11|	2|

## B. Runner and Customer Experience
### Question 1: How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
- Code:
```

```
- Result:


### Question 2: What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
- Code:
```

```
- Result:


### Question 3: Is there any relationship between the number of pizzas and how long the order takes to prepare?
- Code:
```

```
- Result:


### Question 4: What was the average distance travelled for each customer?
- Code:
```

```
- Result:


### Question 5: What was the difference between the longest and shortest delivery times for all orders?
- Code:
```

```
- Result:


### Question 6: What was the average speed for each runner for each delivery and do you notice any trend for these values?
- Code:
```

```
- Result:


### Question 7: What is the successful delivery percentage for each runner?
- Code:
```

```
- Result:


