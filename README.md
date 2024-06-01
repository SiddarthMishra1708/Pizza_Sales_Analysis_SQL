# SQL for Data Analysis Project
## Description
#### We have been given 4 sets of pizza sales data. Namely... 
1. order_details - This dataset contains the following information: order_details_id, 	order_id,	 pizza_id  and  quantity
2. orders - This dataset contains the following information: order_id,	date and time
3. pizza_types - This dataset contains the following information: pizza_type_id,	name,	category and ingredients
4. pizzas - This dataset contains the following information: pizza_id,	pizza_type_id,	size and price
#### Using these 4 datasets we have to perform data analysis in SQL and answer the questions that we received in a text file along with the datasets.
#### These are the following queries that need to be solved:
***Questions Link*** - https://github.com/SiddarthMishra1708/SQL-project/blob/main/Questions.txt

**BASIC**:
1. Retrieve the total number of orders placed.
2. Calculate the total revenue generated from pizza sales.
3. Identify the highest-priced pizza.
4. Identify the most common pizza size ordered.
5. List the top 5 most ordered pizza types along with their quantities.


**INTERMEDIATE**:
1. Join the necessary tables to find the total quantity of each pizza category ordered.
2. Determine the distribution of orders by hour of the day.
3. Join relevant tables to find the category-wise distribution of pizzas.
4. Group the orders by date and calculate the average number of pizzas ordered per day.
5. Determine the top 3 most ordered pizza types based on revenue.

**ADVANCED**:
1. Calculate the percentage contribution of each pizza type to total revenue.
2. Analyze the cumulative revenue generated over time.
3. Determine the top 3 most ordered pizza types based on revenue for each pizza category.

## Tools Used
MySQL Workbench 8.0 CE
## Database Schema
![DBOSCHEMA](https://github.com/SiddarthMishra1708/SQL-project/assets/170062307/67f708fc-9e00-4152-b64e-20d219156deb)
## Solution to the quesries
### Basic
#### 1. To retrieve the total number of orders placed
    SELECT 
        COUNT(order_id) AS Total_Orders
    FROM
        order_details;
#### 2. To Calculate the total revenue generated from pizza sales
    SELECT 
        ROUND(SUM(order_details.quantity * pizzas.price),0) AS Revenue
    FROM
        order_details
            JOIN
        pizzas ON order_details.pizza_id = pizzas.pizza_id   
#### 3. To identify the highest-priced pizza
    SELECT 
        pizza_types.name, pizzas.pizza_id, pizzas.price
    FROM
        pizza_types
            JOIN
        pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
    ORDER BY pizzas.price DESC
    LIMIT 1
#### 4. To identify the most common pizza size ordered
    SELECT 
        pizzas.size, SUM(order_details.quantity) AS Quantity
    FROM
        pizzas
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizzas.size
    ORDER BY SUM(order_details.quantity) DESC
    LIMIT 1
#### 5. To list the top 5 most ordered pizza types along with their quantities
    SELECT 
        pizza_types.name, SUM(order_details.quantity) AS Quantity
    FROM
        pizza_types
            JOIN
        pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizza_types.name
    ORDER BY SUM(order_details.quantity) DESC
    LIMIT 5
### Intermediate
#### 1. To find the total quantity of each pizza category ordered
    SELECT 
        pizza_types.category,            
        SUM(order_details.quantity) AS Quantity
    FROM
        pizza_types
            JOIN
        pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id                
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizza_types.category
    ORDER BY SUM(order_details.quantity) DESC
#### 2. To determine the distribution of orders by hour of the day
    SELECT 
        HOUR(orders.order_time) AS Hour,
        COUNT(DISTINCT (orders.order_id)) AS No_of_orders
    FROM
        orders
    GROUP BY HOUR(orders.order_time)
    ORDER BY HOUR(orders.order_time)
#### 3. To find the category-wise distribution of pizzas
    SELECT 
        pizza_types.category,
        COUNT(pizza_types.pizza_type_id) AS Pizza_types
    FROM
        pizza_types
    GROUP BY pizza_types.category
    ORDER BY Pizza_types;
#### 4. To group the orders by date and calculate the average number of pizzas ordered per day
    SELECT 
        AVG(quantity) AS Average
    FROM
        (SELECT 
            orders.order_date, SUM(order_details.quantity) AS quantity
        FROM
            orders
        JOIN order_details ON order_details.order_id = orders.order_id
        GROUP BY orders.order_date) AS order_quantity;
#### 5. To determine the top 3 most ordered pizza types based on revenue
    SELECT 
        pizza_types.name,
        ROUND(SUM(pizzas.price * order_details.quantity)) AS Revenue
    FROM
        pizza_types
            JOIN
        pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizza_types.name
    ORDER BY Revenue DESC
    LIMIT 3
### Advance
#### 1. To calculate the percentage contribution of each pizza type to total revenue
    SELECT 
        pizza_types.category,
        ROUND((SUM(order_details.quantity * pizzas.price) * 100) / (SELECT 
                        ROUND(SUM(order_details.quantity * pizzas.price))
                    FROM
                        order_details
                            JOIN
                        pizzas ON order_details.pizza_id = pizzas.pizza_id),
                0) AS Percent
    FROM
        pizza_types
            JOIN
        pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
    GROUP BY pizza_types.category
    ORDER BY Percent DESC
#### 2. To analyze the cumulative revenue generated over time
    SELECT 
    	order_date, 
        SUM(revenue) 
    		OVER(ORDER BY order_date) 
    			AS Cumulative_revenue
    FROM 
    	(SELECT 
    		orders.order_date,
    		ROUND(SUM(order_details.quantity * pizzas.price),
    				0) AS revenue
    	FROM
    		order_details
    			JOIN
    		pizzas ON order_details.pizza_id = pizzas.pizza_id
    			JOIN
    		orders ON orders.order_id = order_details.order_id
    	GROUP BY orders.order_date) AS sales
#### 3. To determine the top 3 most ordered pizza types based on revenue for each pizza category
    SELECT
    	name,
        category,
        revenue
    FROM
    	(SELECT
    		name,
            revenue,
    		category,
    		RANK() OVER(PARTITION BY category ORDER BY revenue DESC) AS pizza_rank
    	FROM 
    		(SELECT 
    			pizza_types.name,
    			pizza_types.category,
    			ROUND(SUM(order_details.quantity * pizzas.price),0) AS revenue
    		FROM
    			order_details
    				JOIN
    			pizzas ON pizzas.pizza_id = order_details.pizza_id
    				JOIN
    			pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
    		GROUP BY pizza_types.name , pizza_types.category) AS a) AS b
    WHERE pizza_rank <= 3;





