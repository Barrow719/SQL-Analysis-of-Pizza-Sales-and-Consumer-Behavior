# SQL-Analysis-of-Pizza-Sales-and-Consumer-Behavior
NOTE: CURRENCY IN THE DATASET IS IN USA DOLLAR
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

1. What is the average price of a pizza?

SELECT Round(AVG(price),2) AS average_price

FROM pizzas;



![Image](https://github.com/user-attachments/assets/d20590c9-5eb1-4ee4-a935-0be6e62b239f)






2. How many orders were placed for each pizza type?

SELECT pizza_type, COUNT(*) AS order_count

FROM orders

GROUP BY pizza_type

ORDER BY order_count DESC;




![Image](https://github.com/user-attachments/assets/c091c066-8a68-42b5-b36c-1013b3627fef)













3. How much revenue was generated from pizzas sold during a specific month (e.g., January 2015)?




SELECT SUM(item_total) AS total_revenue

FROM orders

WHERE order_date BETWEEN '2015-01-01' AND '2015-01-31';






![Image](https://github.com/user-attachments/assets/23e0a8fb-aba2-4e9c-aa20-7dec43e401a6)











4. Which day of the week has the most orders?

   

SELECT TO_CHAR(order_date, 'Day') AS weekday, COUNT(*) AS order_count

FROM orders

GROUP BY weekday

ORDER BY order_count DESC;




![Image](https://github.com/user-attachments/assets/35297ae6-f934-4dca-b117-26509f4d2942)













5. What is the price difference between the cheapest and most expensive pizza in the menu?


SELECT MAX(price) - MIN(price) AS price_difference

FROM pizzas;


![Image](https://github.com/user-attachments/assets/567c8101-35ac-4f7e-8f31-df1d25637301)












6. What is the total number of orders placed during weekends (Saturday and Sunday)?


SELECT COUNT(*) AS weekend_orders

FROM orders

WHERE EXTRACT(DOW FROM order_date) IN (0, 6);

![Image](https://github.com/user-attachments/assets/c67e31fe-6782-4add-9218-7fcaa6578a6c)














7. How many orders were placed per hour of the day?

SELECT EXTRACT(HOUR FROM order_time) AS hour_of_day, COUNT(*) AS orders_per_hour

FROM orders

GROUP BY hour_of_day

ORDER BY hour_of_day;

![Image](https://github.com/user-attachments/assets/13773a38-8c68-4e42-9e08-9847b5ee43ab)











8. What is the total sales revenue for each pizza type?

SELECT pizza_type, 

       SUM(item_total) AS total_revenue
       
FROM orders

GROUP BY pizza_type

ORDER BY total_revenue DESC;

![Image](https://github.com/user-attachments/assets/06b1b012-f4b4-479c-9a35-cfe391668eaa)













9. Which pizza has been ordered the most?

SELECT pizza_name, 

       COUNT(pizza_name) AS order_count
       
FROM orders

GROUP BY pizza_name

ORDER BY order_count DESC

LIMIT 1;

![Image](https://github.com/user-attachments/assets/cc132d71-c548-4c4f-9c33-3600e589b549)















10. How many orders were made each month, and what was the total revenue per month?

SELECT TO_CHAR(order_date, 'YYYY-MM') AS month, 

       COUNT(order_id) AS total_orders,
       
       SUM(item_total) AS total_revenue
       
FROM orders

GROUP BY TO_CHAR(order_date, 'YYYY-MM')

ORDER BY month;

![Image](https://github.com/user-attachments/assets/38e9e225-3c84-4e14-8002-9f3311b894a8)















11. What is the average price of pizzas by size?

SELECT size, 

       Round(AVG(price),2) AS average_price
       
FROM pizzas

GROUP BY size

ORDER BY size;

![Image](https://github.com/user-attachments/assets/8509e99d-2ace-403a-8c8a-3b343e41edbc)


















12. What is the total order quantity and total revenue for each order?

SELECT order_id, 

       SUM(quantity) AS total_quantity,
       
       SUM(item_total) AS total_revenue
       
FROM orders

GROUP BY order_id

ORDER BY order_id;

![Image](https://github.com/user-attachments/assets/8509e99d-2ace-403a-8c8a-3b343e41edbc)



















13. What orders include a specific pizza type, like 'Classic'?

SELECT orders.order_id, orders.pizza_name, orders.pizza_type, orders.quantity, orders.item_total

FROM orders

JOIN pizza_types ON orders.pizza_type = pizza_types.pizza_type_id

WHERE pizza_types.category = 'Classic';


![Image](https://github.com/user-attachments/assets/e166caf7-ee12-4d34-94b4-477f1a615a88)


















14. How much money was made from orders that happened between a certain time (like 12:00 PM and 2:00 PM)?

SELECT SUM(orders.item_total) AS total_revenue

FROM orders

JOIN order_times ON orders.order_id = order_times.order_id

WHERE order_times.time BETWEEN '12:00:00' AND '14:00:00';


![Image](https://github.com/user-attachments/assets/a5ec7a42-1b7f-44f0-a5d1-70de95bc6118)



















15. What percentage of the total revenue came from each type of pizza?


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

![Image](https://github.com/user-attachments/assets/66276474-c45b-49d3-bf70-99112bf10e54)






















16. Which pizza sizes are the most popular based on the number of orders?


SELECT pizzas.size, COUNT(*) AS num_orders

FROM orders

JOIN pizzas ON orders.pizza_id = pizzas.pizza_id

GROUP BY pizzas.size

ORDER BY num_orders DESC;


![Image](https://github.com/user-attachments/assets/7fe74ceb-8546-4863-b93d-9c615d2fd126)

















17. Can we group pizzas into 'Cheap', 'Medium', and 'Expensive' based on their price?


SELECT pizza_types.name AS pizza_name,

       CASE
       
           WHEN pizzas.price <= 15 THEN 'Cheap'
           
           WHEN pizzas.price > 15 AND pizzas.price <= 20 THEN 'Medium'
           
           ELSE 'Expensive'
           
       END AS price_category
       
FROM pizzas

JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id;


![Image](https://github.com/user-attachments/assets/4037e632-8e2d-4a46-b42a-26b09b4c0629)















18. Which orders included more than one pizza?


SELECT orders.order_id, COUNT(*) AS num_pizzas

FROM orders

GROUP BY orders.order_id

HAVING COUNT(*) > 1;


![Image](https://github.com/user-attachments/assets/f0833ada-d31f-48d2-8120-fbbb7dce798b)























19. Which pizza in the 'Classic' category is the most expensive?


SELECT pizza_types.name AS pizza_name, pizzas.price

FROM pizzas

JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id

WHERE pizza_types.category = 'Classic'

ORDER BY pizzas.price DESC

LIMIT 1;

![Image](https://github.com/user-attachments/assets/bc11a208-5088-414d-93de-a51df71d2b7c)























20. How can we filter pizza orders to show only certain ones after we’ve grouped them by type or time?


SELECT pizza_types.category AS pizza_type, COUNT(*) AS order_count

FROM orders

JOIN pizza_types ON orders.pizza_type = pizza_types.pizza_type_id

GROUP BY pizza_types.category
HAVING pizza_types.category = 'Classic';


![Image](https://github.com/user-attachments/assets/4478007d-0dbb-4ddf-8369-1e511f89a1eb)








