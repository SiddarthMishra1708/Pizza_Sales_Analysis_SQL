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
