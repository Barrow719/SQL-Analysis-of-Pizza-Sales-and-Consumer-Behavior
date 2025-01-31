# SQL-Analysis-of-Pizza-Sales-and-Consumer-Behavior

Steps Involved:

Loading four excel Datasets Into SQL(Pgadmin)

CREATE TABLE orders (

    order_id INT,
    
    pizza_id VARCHAR(50),
    
    pizza_type VARCHAR(50),
    
    category VARCHAR(50),
    
    pizza_name VARCHAR(100),
    
    order_date DATE,
    
    order_time TIME,
    
    quantity INT,
    
    price DECIMAL(10, 2),
    
    item_total DECIMAL(10, 2)
    
);


CREATE TABLE order_times (

    order_id INT,
    
    order_date DATE,
    
    order_time TIME
    
);


CREATE TABLE pizza_types (

    pizza_type_id VARCHAR(50),
    
    name VARCHAR(100),
    
    category VARCHAR(50),
    
    ingredients TEXT
    
);

CREATE TABLE pizzas (

    pizza_id VARCHAR(50),
    
    pizza_type_id VARCHAR(50),
    
    size CHAR(1), 
    
    price DECIMAL(10, 2)
    
);

Here is a list of all the questions extracted from the SQL queries:

What is the average price of a pizza?

SELECT Round(AVG(price),2) AS average_price

FROM pizzas;

How many orders were placed for each pizza type?

SELECT pizza_type, COUNT(*) AS order_count

FROM orders

GROUP BY pizza_type

ORDER BY order_count DESC;

How much revenue was generated from pizzas sold during a specific month (e.g., January 2015)?


SELECT SUM(item_total) AS total_revenue

FROM orders

WHERE order_date BETWEEN '2015-01-01' AND '2015-01-31';

Which day of the week has the most orders?

SELECT TO_CHAR(order_date, 'Day') AS weekday, COUNT(*) AS order_count

FROM orders

GROUP BY weekday

ORDER BY order_count DESC;

What is the price difference between the cheapest and most expensive pizza in the menu?


SELECT MAX(price) - MIN(price) AS price_difference

FROM pizzas;

What is the total number of orders placed during weekends (Saturday and Sunday)?


SELECT COUNT(*) AS weekend_orders

FROM orders

WHERE EXTRACT(DOW FROM order_date) IN (0, 6);

How many orders were placed per hour of the day?

SELECT EXTRACT(HOUR FROM order_time) AS hour_of_day, COUNT(*) AS orders_per_hour

FROM orders

GROUP BY hour_of_day

ORDER BY hour_of_day;

What is the total sales revenue for each pizza type?

SELECT pizza_type, 

       SUM(item_total) AS total_revenue
       
FROM orders

GROUP BY pizza_type

ORDER BY total_revenue DESC;

Which pizza has been ordered the most?

SELECT pizza_name, 

       COUNT(pizza_name) AS order_count
       
FROM orders

GROUP BY pizza_name

ORDER BY order_count DESC

LIMIT 1;

How many orders were made each month, and what was the total revenue per month?

SELECT TO_CHAR(order_date, 'YYYY-MM') AS month, 

       COUNT(order_id) AS total_orders,
       
       SUM(item_total) AS total_revenue
       
FROM orders

GROUP BY TO_CHAR(order_date, 'YYYY-MM')

ORDER BY month;

What is the average price of pizzas by size?

SELECT size, 

       Round(AVG(price),2) AS average_price
       
FROM pizzas

GROUP BY size

ORDER BY size;

What is the total order quantity and total revenue for each order?

SELECT order_id, 

       SUM(quantity) AS total_quantity,
       
       SUM(item_total) AS total_revenue
       
FROM orders

GROUP BY order_id

ORDER BY order_id;

What orders include a specific pizza type, like 'Classic'?

SELECT orders.order_id, orders.pizza_name, orders.pizza_type, orders.quantity, orders.item_total

FROM orders

JOIN pizza_types ON orders.pizza_type = pizza_types.pizza_type_id

WHERE pizza_types.category = 'Classic';

How much money was made from orders that happened between a certain time (like 12:00 PM and 2:00 PM)?

SELECT SUM(orders.item_total) AS total_revenue

FROM orders

JOIN order_times ON orders.order_id = order_times.order_id

WHERE order_times.time BETWEEN '12:00:00' AND '14:00:00';

What percentage of the total revenue came from each type of pizza?


WITH total_revenue AS (

    SELECT Round(SUM(item_total),2) AS total
    
    FROM orders
)

SELECT pizza_types.category AS pizza_type,

       SUM(orders.item_total) AS pizza_revenue,
       
       (SUM(orders.item_total) / total_revenue.total) * 100 AS percentage_revenue
       
FROM orders

JOIN pizza_types ON orders.pizza_type = pizza_types.pizza_type_id

CROSS JOIN total_revenue

GROUP BY pizza_types.category, total_revenue.total

ORDER BY percentage_revenue DESC;

Which pizza sizes are the most popular based on the number of orders?


SELECT pizzas.size, COUNT(*) AS num_orders

FROM orders

JOIN pizzas ON orders.pizza_id = pizzas.pizza_id

GROUP BY pizzas.size

ORDER BY num_orders DESC;

Can we group pizzas into 'Cheap', 'Medium', and 'Expensive' based on their price?


SELECT pizza_types.name AS pizza_name,

       CASE
       
           WHEN pizzas.price <= 15 THEN 'Cheap'
           
           WHEN pizzas.price > 15 AND pizzas.price <= 20 THEN 'Medium'
           
           ELSE 'Expensive'
           
       END AS price_category
       
FROM pizzas

JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id;


Which orders included more than one pizza?


SELECT orders.order_id, COUNT(*) AS num_pizzas

FROM orders

GROUP BY orders.order_id

HAVING COUNT(*) > 1;

Which pizza in the 'Classic' category is the most expensive?


SELECT pizza_types.name AS pizza_name, pizzas.price

FROM pizzas

JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id

WHERE pizza_types.category = 'Classic'

ORDER BY pizzas.price DESC

LIMIT 1;


How can we filter pizza orders to show only certain ones after we’ve grouped them by type or time?


SELECT pizza_types.category AS pizza_type, COUNT(*) AS order_count

FROM orders

JOIN pizza_types ON orders.pizza_type = pizza_types.pizza_type_id

GROUP BY pizza_types.category

HAVING pizza_types.category = 'Classic';







