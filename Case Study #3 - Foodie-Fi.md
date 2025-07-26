# Case Study #3 - Foodie-Fi
You can find the case study [here](https://8weeksqlchallenge.com/case-study-3/).

Now, Danny has an streaming service called Foodie-Fi where you can find only cooking shows (something like Netflix). He has data recorded and it is time to use it to answer important business questions. First we create the tables, then, we analize the data:

## Create the tables: 
### plans
```
CREATE TABLE seraphic-ripple-464915-d1.Foodie_Fi.plans (
  plan_id INTEGER,
  plan_name STRING,
  price DECIMAL(5,2)
);

INSERT INTO seraphic-ripple-464915-d1.Foodie_Fi.plans
  (plan_id, plan_name, price)
VALUES
  (0, 'trial', 0),
  (1, 'basic monthly', 9.90),
  (2, 'pro monthly', 19.90),
  (3, 'pro annual', 199),
  (4, 'churn', null);
```

  ### subscriptions (for this table, I will write down just a few rows because the original table has 2.650 rows. 
```
  CREATE TABLE seraphic-ripple-464915-d1.Foodie_Fi.subscriptions (
  customer_id INTEGER,
  plan_id INTEGER,
  start_date DATE
);

INSERT INTO seraphic-ripple-464915-d1.Foodie_Fi.subscriptions
  (customer_id, plan_id, start_date)
VALUES
  (1,0, '2020-08-01'),
  (1,1, '2020-08-08'),
  (2,0, '2020-09-20'),
  (2,3, '2020-09-27'),
  (3,0, '2020-01-13'),
  (3,1, '2020-01-20'),
  (4,0, '2020-01-17');
```

First, lets create a table with all the information: 
```
SELECT customer_id, sub.plan_id, start_date, plan_name, price
FROM seraphic-ripple-464915-d1.Foodie_Fi.subscriptions as sub 
JOIN seraphic-ripple-464915-d1.Foodie_Fi.plans as pl
ON sub.plan_id = pl.plan_id
order by customer_id, plan_id
```
Now, the questions: 
## A. Data analysis questions: 
### Question 1: How many customers has Foodie-Fi ever had?
- Code:
```
SELECT COUNT(DISTINCT customer_id) as members
from seraphic-ripple-464915-d1.Foodie_Fi.data
```
- Result:

|members|
|---|
|1000|

### Question 2: What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value
- Code:
```
SELECT EXTRACT(month from start_date) as month, count(*) as monthly_subscriptions
from seraphic-ripple-464915-d1.Foodie_Fi.data
where plan_name = 'trial'
group by month
order by month
```
- Result:

|month|	monthly_subscriptions|
|---|---|
|	1|	88|
|	2|	68|
|	3|	94|
|	4|	81|
|	5|	88|
|	6|	79|
|	7|	89|
|	8|	88|
|	9|	87|
|	10|	79|
|	11|	75|
|	12|	84|

### Question 3: What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name
- Code:
```
SELECT plan_id, plan_name, count(*) as subscriptions
from seraphic-ripple-464915-d1.Foodie_Fi.data
where start_date >= '2021-01-01'
group by plan_id, plan_name
order by plan_id
```
- Result:

|plan_id|	plan_name|	subscriptions|
|	1|	basic monthly|	8|
|	2|	pro monthly|	60|
|	3|	pro annual|	63|
|	4|	churn|	71|

### Question 4: What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
- Code:
```
SELECT plan_name, count(*) as unsubscriptions, (count(*)/10) as churn_rate
from seraphic-ripple-464915-d1.Foodie_Fi.data
where plan_name = 'churn'
group by plan_name
-- as we now, there are 1000 customers so to calculate churn_rate we divide the count by 1000 and the multiply by 100, 100/1000=10
```
- Result:

|plan_name|	unsubscriptions|	churn_rate|
|---|---|---|
|	churn|	307|	30.7|

### Question 5: How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
- Code:
```
select count(*) as didnt_subscribed, (count(*)/10) as rate
FROM ( 
SELECT customer_id, sum(price) as paid
from seraphic-ripple-464915-d1.Foodie_Fi.data
group by customer_id )
where paid = 0
-- as we now, there are 1000 customers so to calculate rate we divide the count by 1000 and the multiply by 100, 100/1000=10
```
- Result:

|didnt_subscribed|	rate|
|---|---|
|	92|	9.2|

### Question 6: What is the number and percentage of customer plans after their initial free trial?
- Code:
```
select plan_id, plan_name, count(*) as members, count(*)/10 as percentage
from (
  select data.*, ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY start_date) AS rn
from seraphic-ripple-464915-d1.Foodie_Fi.data as data)
where rn=2
group by plan_id, plan_name
order by plan_id
-- as we now, there are 1000 customers so to calculate rate we divide the count by 1000 and the multiply by 100, 100/1000=10
```
- Result:

|plan_id|	plan_name|	members|	percentage|
|---|---|---|---|
|	1|	basic monthly|	546|	54.6|
|	2|	pro monthly|	325|	32.5|
|	3|	pro annual|	37|	3.7|
|	4|	churn|	92|	9.2|

### Question 7: What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
- Code:
```
```
- Result:



### Question 8: How many customers have upgraded to an annual plan in 2020?
- Code:
```
```
- Result:


### Question 9: How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
- Code:
```
```
- Result:


### Question 10: Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
- Code:
```
```
- Result:


### Question 11: How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
- Code:
```
```
- Result:

