# Case Study #1 - Danny's Diner.md
First we create the tables, then, we analize the data: 

## Create the tables: 
```
CREATE TABLE seraphic-ripple-464915-d1.Dannys_Diner.sales(
  customer_id STRING,
  order_date DATE, 
  product_id INTEGER
);

INSERT INTO seraphic-ripple-464915-d1.Dannys_Diner.sales (customer_id, order_date, product_id)
VALUES
  ('A', '2021-01-01', 1), 
  ('A', '2021-01-01', 2), 
  ('A', '2021-01-07', 2), 
  ('A', '2021-01-10', 3),
  ('A', '2021-01-11', 3),
  ('A', '2021-01-11', 3),
  ('B', '2021-01-01', 2),
  ('B', '2021-01-02', 2),
  ('B', '2021-01-04', 1), 
  ('B', '2021-01-11', 1),
  ('B', '2021-01-16', 3),
  ('B', '2021-01-01', 3),
  ('C', '2021-01-01', 3),
  ('C', '2021-01-01', 3),
  ('C', '2021-01-07', 3);

CREATE TABLE seraphic-ripple-464915-d1.Dannys_Diner.menu(
    product_id INTEGER,
    product_name STRING,
    price INTEGER
  );

  INSERT INTO seraphic-ripple-464915-d1.Dannys_Diner.menu (product_id, product_name, price)
  VALUES
    (1, 'sushi', 10), 
    (2, 'curry', 15),
    (3, 'ramen', 12); 

CREATE TABLE seraphic-ripple-464915-d1.Dannys_Diner.members (
  customer_id STRING,
  join_date DATE
);

INSERT INTO seraphic-ripple-464915-d1.Dannys_Diner.members (customer_id,join_date)
VALUES 
  ('A', '2021-01-07'),
  ('B', '2021-01-09'); 
```

## Then, we create a join to have the whole data: 
```
SELECT sales.customer_id, sales.order_date, sales.product_id, menu.product_name, menu.price, members.join_date
FROM seraphic-ripple-464915-d1.Dannys_Diner.sales as sales
INNER JOIN seraphic-ripple-464915-d1.Dannys_Diner.menu as menu
ON sales.product_id = menu.product_id
LEFT JOIN seraphic-ripple-464915-d1.Dannys_Diner.members as members
ON sales.customer_id = members.customer_id
```

## Now we answer the questions: 

### Question 1: What is the total amount each customer spent at the restaurant?
- Code: 
```
SELECT customer_id, SUM(price) as customer_spent
FROM seraphic-ripple-464915-d1.Dannys_Diner.data
group by customer_id
order by customer_spent DESC
```
- Result:

| customer_id | customer_spent |
| --- | --- |
| A | 76 |
| B | 74 | 
| C | 36 |

### Question 2: How many days has each customer visited the restaurant?
- Code:
```
SELECT customer_id, count(*) as visits
FROM  (SELECT customer_id, order_date 
  FROM seraphic-ripple-464915-d1.Dannys_Diner.data
  group by customer_id, order_date)
GROUP BY customer_id
ORDER BY customer_id
```

- Result:

| customer_id | visits |
| --- | --- |
| A | 4 |
| B | 5 | 
| C | 2 |

### Question 3: What was the first item from the menu purchased by each customer?
- Code:
```
SELECT DISTINCT customer_id,product_name 
FROM 
    (SELECT customer_id,
            product_name,
            DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS dn
    FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data)
WHERE dn = 1
ORDER BY customer_id
```

- Result:

| customer_id | product_name |
| --- | --- |
| A | curry |
| A | sushi | 
| B | curry |
| B | ramen |
| C | ramen|

### Question 4: What is the most purchased item on the menu and how many times was it purchased by all customers?
- Code:
```
SELECT product_name, count(*) as purchases
FROM seraphic-ripple-464915-d1.Dannys_Diner.data
GROUP BY product_name 
order by purchases DESC
limit 1
```
- Result:

| product_name | purchases |
| --- | --- |
| ramen | 8 |

### Question 5: Which item was the most popular for each customer?
- Code:
```
SELECT customer_id,product_name 
FROM 
    (SELECT customer_id,
            product_name,
            DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY count(product_name) DESC) AS dn
    FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data
    GROUP BY customer_id, product_name)
WHERE dn = 1
ORDER BY customer_id
```

- Result:
- 
| customer_id | product_name |
| --- | --- |
| A | ramen |
| B | sushi | 
| B | curry |
| B | ramen |
| C | ramen|

### Question 6: Which item was purchased first by the customer after they became a member?
- Code:
```
SELECT customer_id,product_name 
FROM 
    (SELECT customer_id,
            product_name,
            DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS dn
    FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data
    WHERE order_date > join_date)
WHERE dn = 1
ORDER BY customer_id
```

- Result:

| customer_id | product_name |
| --- | --- |
| A | ramen |
| B | sushi | 

### Question 7: Which item was purchased just before the customer became a member?
- Code:
```
SELECT customer_id,product_name
FROM 
    (SELECT customer_id,
            product_name, 
            DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS dn
    FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data
    WHERE order_date < join_date)
WHERE dn = 1
ORDER BY customer_id
```

- Result:

| customer_id | product_name |
| --- | --- |
| A | sushi |
| A | curry |
| B | sushi | 

### Question 8: What is the total items and amount spent for each member before they became a member?
```
SELECT customer_id, count(product_name) as total_items, sum(price) as total_price
FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data
where order_date < join_date 
group by customer_id
```

- Result:

| customer_id | total_items | total_price |
| --- | --- | --- |
| A | 2 | 25 |
| B | 4 | 52 |

### Question 9: If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
- Code:
```
SELECT customer_id, sum(
CASE
     WHEN product_name = 'sushi' THEN price*10*2
     ELSE price*10 
END) as points
FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data
group by customer_id
```
- Result:

| customer_id | points |
| --- | --- |
| A | 860 |
| B | 940 |
| C | 360 | 

### Question 10: In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
- Code:
```
SELECT customer_id, sum(
  CASE 
  WHEN (product_name = 'sushi' OR date_diff(order_date, join_date, day) BETWEEN 0 AND 6) THEN price*20
  ELSE price*10
  END) as points,
FROM seraphic-ripple-464915-d1.Dannys_Diner.data as data
WHERE join_date IS NOT NULL OR order_date >= join_date
group by customer_id
```

- Result:

| customer_id | points |
| --- | --- |
| A | 1370 |
| B | 940 |
