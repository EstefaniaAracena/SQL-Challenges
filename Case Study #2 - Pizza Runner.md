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
- Result:
#### This question will be answered soon...

### Question 2: What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
- Code:
```
SELECT runner_id, round(avg(aveg)) as avg
FROM (
  SELECT order_id, runner_id, avg(CAST(duration AS INTEGER)) as aveg
  FROM seraphic-ripple-464915-d1.Pizza_Runner.data
  where pickup_time is not null
  group by order_id, runner_id
  order by runner_id
)
group by runner_id
```
- Result:

|runner_id|	avg|
|---|---|
|1|	22.0|
|2|	27.0|
|3|	15.0|

### Question 3: Is there any relationship between the number of pizzas and how long the order takes to prepare?
- Code:
```
SELECT no_pizzas, avg(preparation_time) as prep_time
FROM (
  SELECT order_id, count(order_id) as no_pizzas, avg((order_date - cast(pickup_time AS DATETIME))) as  preparation_time
  FROM seraphic-ripple-464915-d1.Pizza_Runner.data
  where pickup_time is not null
  group by order_id
  order by preparation_time
)
group by no_pizzas
```
- Result:

|no_pizzas|	prep_time|
|---|---|
|1|	0-0 0 -0:12:21.400|
|2|	0-0 0 -0:18:22.500|
|3|	0-0 0 -0:29:17|

Yes, it is related the number of pizzas with the preparation time. 

### Question 4: What was the average distance travelled for each customer?
- Code:
```
SELECT customer_id, round(avg(cast(distance as decimal)),2) as distance
FROM seraphic-ripple-464915-d1.Pizza_Runner.data
where distance is not null
group by customer_id
```
- Result:

|customer_id|	distance|
|---|---|
|101|	20|
|102|	16.73|
|103|	23.4|
|104|	10|
|105|	25|

### Question 5: What was the difference between the longest and shortest delivery times for all orders?
- Code:
```
select max(cast(distance as decimal)) - min(cast(distance as decimal)) as diff
from seraphic-ripple-464915-d1.Pizza_Runner.data
where cancellation is null
```
- Result:

|diff|
|---|
|15|

### Question 6: What was the average speed for each runner for each delivery and do you notice any trend for these values?
- Code:
```
SELECT runner_id, round(avg(vel*60),2) as velocity_km_hour
FROM (
  select runner_id, order_id, avg((cast(distance as decimal)) / (cast(duration as integer))) as vel
  from seraphic-ripple-464915-d1.Pizza_Runner.data
  where cancellation is null
  group by order_id, runner_id
  order by runner_id
)
group by runner_id
```
- Result:

|runner_id|	velocity_km_hour|
|---|---|
|1|	45.54|
|2|	62.9|
|3|	40|

### Question 7: What is the successful delivery percentage for each runner?
- Code:
```
select runner_id, round(sum(cancellations_null)/(sum(cancellations_null)+ sum(cancellations_not_null))*100,2) as delivery_percentage
FROM (
  select runner_id, order_id,
  case when cancellation is null then 1 else 0 end as cancellations_null,
  case when cancellation is not null then 1 else 0 end as cancellations_not_null
  from seraphic-ripple-464915-d1.Pizza_Runner.data
)
group by runner_id
```
- Result:

|runner_id|	delivery_percentage|
|---|---|
|1|	100.0|
|2|	83.33|
|3|	50.0|

### C. Ingredient Optimisation
Now, the third part is mostly focused on the ingredients. As you saw, the table "pizza_toppings" doesn't have a nice layout, for that reason, first let's unnest it as save it as pizza_tops: 
```
select DISTINCT pizza_id, cast(toppings as integer) as topping_id
FROM seraphic-ripple-464915-d1.Pizza_Runner.pizza_recipes, 
UNNEST(split(toppings, ',')) toppings
```

Thenm, we must create a join with all the pizza information related to ingredients as we save it as data_pizza: 
```
SELECT pn.pizza_id, pn.pizza_name, pr.topping_id, pt.topping_name
FROM seraphic-ripple-464915-d1.Pizza_Runner.pizza_recipes2 as pr 
join seraphic-ripple-464915-d1.Pizza_Runner.pizza_name as pn
ON pr.pizza_id = pn.pizza_id
JOIN seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings as pt
ON pt.topping_id = pr.topping_id
```

Now, the questions: 

### Question 1: What are the standard ingredients for each pizza?
- Code:
```
SELECT pizza_name, string_agg(topping_name, ',') as ingredients 
FROM seraphic-ripple-464915-d1.Pizza_Runner.data_pizza
GROUP BY pizza_name
```
- Result:

|pizza_name|	ingredients|
|---|---|
|Meat Lovers|	Beef,BBQ Sauce,Salami,Cheese,Pepperoni,Mushrooms,Chicken,Bacon|
|Vegetarian|	Peppers,Tomato Sauce,Mushrooms,Onions,Cheese,Tomatoes|

### Question 2: What was the most commonly added extra?
- Code:
```
SELECT * 
FROM (
  SELECT extras_id, count(extras_id) as count
  FROM (
    SELECT pizza_id, cast(extras as integer) as extras_id
    FROM seraphic-ripple-464915-d1.Pizza_Runner.data,
    UNNEST(split(extras, ',')) extras
    where extras is not null) as temp
    group by extras_id) as temp2
join seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings as pt
on pt.topping_id = temp2.extras_id
ORDER BY count DESC 
LIMIT 1 
```
- Result:

|extras_id|	count	|topping_id|	topping_name|
|---|---|---|---|
|	1	|4	|1	|Bacon|

### Question 3: What was the most common exclusion?
- Code:
```
SELECT * 
FROM (
  SELECT exclusions_id, count(exclusions_id) as count
  FROM (
    SELECT pizza_id, cast(exclusions as integer) as exclusions_id
    FROM seraphic-ripple-464915-d1.Pizza_Runner.data,
    UNNEST(split(exclusions, ',')) exclusions
    where exclusions is not null) as temp
    group by exclusions_id) as temp2
join seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings as pt
on pt.topping_id = temp2.exclusions_id
ORDER BY count DESC 
LIMIT 1 
```
- Result:

|exclusions_id|	count|	topping_id|	topping_name|
|---|---|---|---|
|4|	4|	4|	Cheese|

### Question 4: Generate an order item for each record in the customers_orders table in the format of one of the following:
* Meat Lovers
* Meat Lovers - Exclude Beef
* Meat Lovers - Extra Bacon
* Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers

- Code:
```
with exclusions1 as (
  SELECT _id, customer_id, order_id, pizza_id, exclusions, topping_name as exc_top
  FROM
    (SELECT _id, customer_id, order_id, pizza_id, cast(exclusions as integer) as exclusions
    FROM (
    SELECT ROW_NUMBER() OVER () AS _id, data.customer_id, data.order_id, data.pizza_id, exclusions
    FROM seraphic-ripple-464915-d1.Pizza_Runner.data
    ),
    UNNEST(split(exclusions, ',')) exclusions) as tem
  LEFT JOIN seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings as pt 
  ON tem.exclusions = pt.topping_id), 

extras1 as (
  SELECT _id, customer_id, order_id, pizza_id, extras, topping_name as ext_top
  FROM
    (SELECT _id, customer_id, order_id, pizza_id, cast(extras as integer) as extras
    FROM (
    SELECT ROW_NUMBER() OVER () AS _id, data.customer_id, data.order_id, data.pizza_id, extras
    FROM seraphic-ripple-464915-d1.Pizza_Runner.data
    ),
    UNNEST(split(extras, ',')) extras) as tem
  LEFT JOIN seraphic-ripple-464915-d1.Pizza_Runner.pizza_toppings as pt 
  ON tem.extras = pt.topping_id
),

data1 as (
  SELECT data._id, data.customer_id, data.order_id, data.pizza_id, extras1.extras, ext_top
  from (
    SELECT ROW_NUMBER() OVER () AS _id, data.customer_id, data.order_id, data.pizza_id, exclusions, extras
    from seraphic-ripple-464915-d1.Pizza_Runner.data) as data
  LEFT JOIN extras1 
  ON data._id = extras1._id), 

data2 as (
  select temp._id, temp.order_id, temp.pizza_id, temp.extras, temp.ext_top, temp.exc_top, pizza_name
  from(
    SELECT data1._id, data1.customer_id, data1.order_id, data1.pizza_id, data1.extras, exclusions1.exclusions, ext_top, exc_top
    from data1
    LEFT JOIN exclusions1 
    ON data1._id = exclusions1._id) as temp
  join seraphic-ripple-464915-d1.Pizza_Runner.pizza_name as pn 
  on temp.pizza_id = pn.pizza_id
), 

ordenes as (
  select order_id, orders
  from (
    select _id, 
    case 
    when exclusions is not null and extras is null then concat(pizza_name, ' - ', 'Exclude ', exclusions)
    when exclusions is null and extras is not null then concat(pizza_name, ' - ', 'Extra ', extras)
    when exclusions is not null and extras is not null then concat(pizza_name, ' - ', 'Exclude ', exclusions,' - ', 'Extra ', extras)
    else pizza_name
    end as orders
    from (
      select _id, pizza_name, string_agg(distinct exc_top, ', ') as exclusions, string_agg(distinct ext_top, ', ') as extras
      from data2
      group by _id, pizza_name) ) tem1
  JOIN (select row_number() over() as _id, order_id
  from seraphic-ripple-464915-d1.Pizza_Runner.data) as tem2
  ON tem1._id = tem2._id)

select *
from ordenes
order by order_id
```
- Result:

|order_id|	orders|
|---|---|
|	1|	Meat Lovers|
|	2|	Meat Lovers|
|	3|	Vegetarian|
|	3|	Meat Lovers|
|	4|	Meat Lovers - Exclude Cheese|
|	4|	Vegetarian - Exclude Cheese|
|	4|	Meat Lovers - Exclude Cheese|
|	5|	Meat Lovers - Extra Bacon|
|	6|	Vegetarian|
|	7|	Vegetarian - Extra Bacon|
|	8|	Meat Lovers|
|	9|	Meat Lovers - Exclude Cheese - Extra Bacon, Chicken|
|	10|	Meat Lovers|
|	10|	Meat Lovers - Exclude BBQ Sauce, Mushrooms - Extra Bacon, Cheese|

### Question 5: Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"

- Code:
```
```
- Result:


### Question 6: What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?
- Code:
```
```
- Result:


