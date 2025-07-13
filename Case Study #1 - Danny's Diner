# Create the tables: 

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
