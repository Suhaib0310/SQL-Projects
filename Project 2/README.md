# Case Study #2 Pizza Runner🍕  
![image](https://github.com/user-attachments/assets/df2c8094-0668-4349-8225-3ae3a6d377f2)

## Contents

1. Introduction

2. Entity Relationship Diagram
3. Data Cleaning & Data Transformation
4. Case Study Questions & Solutions
   + A. Pizza Metrics
    + B. Runner And Customer Experience
   + C. Ingredient Optimisation
5. Key Insights

## Introduction
##### Welcome to the Pizza Runner Case Study! Follow Danny's journey as he combines the irresistible allure of "80s Retro Styling and Pizza Is The Future" to launch Pizza Runner, an innovative venture in the pizza delivery industry. With his background in data science, Danny understands the significance of data collection for business growth. Now, he seeks assistance in cleaning and analyzing the data to optimize Pizza Runner's operations and guide his runners more efficiently. Join us as we explore how data-driven decisions propel Pizza Runner towards success and elevate the pizza delivery experience to new heights.

## Entity Relationship Diagram

![image](https://github.com/user-attachments/assets/05458e9d-e893-449c-81fe-d2d40c3e5ef6)


## Data Cleaning & Data Transformation

#### Customer data table before

![image](https://github.com/user-attachments/assets/b1df35e6-49c9-4cd7-95a8-94398323ab2d)


+ The customer_orders table consists of individual pizza orders, with each row representing a unique pizza.
+ Key columns in the table are pizza_id, exclusions, and extras.
+ Before utilizing the data for queries, the exclusions and extras columns require a data cleaning process to ensure accuracy and consistency.
+ Data cleaning involves handling missing or null values in the exclusions and extras columns.
+ The ingredient_id values in the exclusions and extras columns need to be standardized for uniformity.
+ Inconsistencies and duplicates in the exclusions and extras data should be resolved to eliminate ambiguities.
+ By performing thorough data cleaning, the customer_orders table will be optimized for effective analysis.
+ The cleaned data will provide valuable insights into customer preferences, enabling better decision-making for Pizza Runner's operations.
+ With accurate data, Pizza Runner can efficiently meet customer demands and deliver an enhanced pizza ordering experience.

      CREATE TEMPORARY TABLE customer_orders_temp 
      SELECT 
		order_id,
        customer_id,
        pizza_id,
        CASE WHEN exclusions IS NULL OR exclusions LIKE 'null' or exclusions= '' THEN null
			ELSE exclusions END AS exclusions,
		CASE WHEN extras IS NULL OR extras LIKE 'null' OR extras = '' THEN null
			ELSE extras END AS extras,
		order_time
      FROM customer_orders;

+ customer_orders table After AS customer_order_temp

![image](https://github.com/user-attachments/assets/0b7ace1c-b74b-4267-a2ed-d33d7627d1b4)


+ runner_orders table Before

![image](https://github.com/user-attachments/assets/15d2bb70-cb78-4303-9180-fa238f6d3809)


The data in the orders table of Pizza Runner contains valuable information regarding the assignment of orders to runners, including pickup times, distances, and durations. However, it is crucial to note that the table may have some known data issues that require careful handling during data cleaning.

Here are the key points to consider when cleaning the data in the orders table:

+ Verify Data Types: Before proceeding with data cleaning, it is essential to check the data types for each column in the schema SQL. Ensuring accurate data types will prevent potential data type mismatches and errors in subsequent queries.
+ Handle Incomplete Orders: Some orders may not be fully completed and can be canceled by either the restaurant or the customer. It is necessary to identify and properly handle these incomplete orders during the data cleaning process.
+ Address Null Values: The table may contain null values in certain columns, such as pickup_time, distance, and duration. Properly handling these null values is crucial to avoid inaccuracies in the analysis.
+ Validate Timestamps: The pickup_time column represents the timestamp when the runner arrives at Pizza Runner headquarters to pick up the pizzas. Validating and ensuring the consistency of these timestamps will be essential to maintain data integrity.
+ Clean Distance and Duration: The distance and duration fields provide information about the runner's travel to deliver the order. Cleaning these fields involves checking for any outliers or inconsistencies that may affect analysis results.
+ Address Known Data Issues: As there are known data issues in the table, special attention must be given to resolving these issues during the data cleaning process. Identifying and rectifying data discrepancies will enhance the accuracy and reliability of the dataset.

       CREATE TEMPORARY TABLE runner_orders_temp
       SELECT 
		order_id,
        runner_id,
        CASE 
			WHEN pickup_time IS NULL OR pickup_time LIKE 'null' THEN null
			ELSE pickup_time END AS pickup_time,
		CASE 
			WHEN distance IS NULL OR distance LIKE 'null' THEN null
			 WHEN distance LIKE '%KM' THEN TRIM('km' FROM distance)
             ELSE distance END AS distance,
		CASE 
			WHEN duration IS NULL OR duration LIKE 'null' THEN null
			WHEN duration LIKE '%mins' THEN TRIM('mins' FROM duration)
            WHEN duration LIKE '%minutes' THEN TRIM('minutes' FROM duration)
            WHEN duration LIKE '%minute' THEN TRIM('minute' FROM duration)
            ELSE duration END AS duration,
		CASE 
			WHEN cancellation IS NULL OR cancellation LIKE 'null' or cancellation = '' THEN null
			ELSE cancellation END AS cancellation
      FROM runner_orders;

+ runner_orders table After AS runner_orders_temp

  ![image](https://github.com/user-attachments/assets/360dfc6a-e0b4-4ae9-a0b8-ed6e33daf323)



## Case Study Questions & Solutions

### A. Pizza Metrics🍕🍕

#### 1. How many pizzas were ordered?

    SELECT COUNT(order_id)AS total_pizzas
    FROM customer_orders_temp;

![image](https://github.com/user-attachments/assets/f474021c-8faf-4ed7-a565-f6f8f9972dd7)


+ The SQL query selects the number of pizza orders (`pizza_orders`) from the `customer_orders_temp table.
+ The `COUNT(order_id)` function calculates the total number of order IDs in the `customer_orders_temp` table, effectively giving the count of pizza orders.
+ As a result, the query presents the total count of pizza orders as `pizza_orders`.

#### 2. How many unique customer orders were made?

	SELECT COUNT(DISTINCT order_id) AS unique_orders
    FROM customer_orders_temp;

![image](https://github.com/user-attachments/assets/54e65ca7-d595-469d-85d2-79a34b1a12fc)


+ The SQL query selects the number of unique orders (`unique_orders`) from the `customer_orders_tem` table.
+ The `COUNT(DISTINCT order_id)` function calculates the total count of distinct order IDs in the `customer_orders_temp` table, effectively giving the count of unique orders.
+ As a result, the query presents the total count of unique orders as `unique_orders`.

#### 3. How many successful orders were delivered by each runner?

     SELECT 
		runner_id,
        COUNT(order_id) AS order_count
    FROM runner_orders_temp 
    WHERE cancellation IS NULL
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/aa7c625a-ff6d-43bb-b285-b9ca80c7e9c4)

+ The SQL query selects the `runner_id` and counts the number of orders delivered (ordeR_count) for each runner from the `runner_orders_temp` table.
+ It retrieves data from the `runner_orders_temp` table.
+ The query filters the data using the WHERE clause, selecting only the rows where the cancellation column is empty (i.e., no cancellation).
+ Results are grouped by `runner_id`.
+ The `COUNT(order_id)` function calculates the number of occurrences of each `order_id` in the `runner_orders_temp` table, giving the count of orders delivered by each runner.
+ As a result, the query presents the total count of orders delivered as `orders_delivered` for each runner from the `runner_orders_temp` table.

#### 4. How many of each type of pizza was delivered?


       SELECT 
		pizza_name,
        COUNT(c.pizza_id) AS pizza_count
    customer_orders_temp c
    LEFT JOIN pizza_names p 
    ON p.pizza_id = c.pizza_id
    LEFT JOIN runner_orders_temp r
    ON r.order_id = c.order_id
    WHERE r.cancellation IS NULL
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/6a10c206-b73c-49ba-8631-567609e88786)

+ The SQL query retrieves the `pizza_name` and counts the number of delivered orders for each pizza (`pizza_count`) from the `customer_orders_temp` table.
+ It retrieves data from the `customer_orders_temp` table and joins it with the `runner_orders_temp` table and the `pizza_names` table.
+ The query performs joins between the tables based on matching `order_id`, `pizza_id`, and `pizza_name`.
+ It also filters the data using the `WHERE` clause, selecting only the rows where the cancellation column is empty (i.e., no cancellation).
+ Results are grouped by `pizza_name` to get the count of delivered orders for each pizza.
+ The COUNT(C.pizza_id) function calculates the number of occurrences of each pizza_id in the customer_orders_tempp table, giving the count of delivered orders for each pizza.
+ As a result, the query presents the total count of delivered orders as delivered_order_count for each pizza from the customer_orders_tempp, runner_orders_temp, and pizza_names tables.

#### 5. How many Vegetarian and Meatlovers were ordered by each customer?


    SELECT 
		customer_id,
            SUM(CASE WHEN pizza_name = 'Meatlovers' THEN 1 ELSE 0 END) AS Meatlovers,
            SUM(CASE WHEN pizza_name = 'Vegetarian' THEN 1 ELSE 0 END) AS Vegetarian
    FROM customer_orders_temp c
    LEFT JOIN pizza_names p
    ON p.pizza_id = c.pizza_id
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/b629a614-5a4f-4e3f-b005-9c406e94fdd1)

+ The SQL query retrieves the `customer_id`, `pizza_name`, and counts the number of ordered pizzas for each customer and pizza combination from the `customer_orders_temp` table.
+ It retrieves data from the `customer_orders_temp` table and joins it with the `pizza_names` table based on matching `pizza_id`.
+ Results are grouped by both `customer_id` and `pizza_name` to calculate the count of ordered pizzas for each customer and pizza combination.
+ As a result, the query presents the total count of ordered pizzas for each customer and pizza combination from the `customer_orders_temp` and `pizza_names` tables.


#### 6. What was the maximum number of pizzas delivered in a single order?

    SELECT 
        COUNT(c.order_id) pizza_count
    FROM customer_orders_temp c
    LEFT JOIN runner_orders_temp r
    ON r.order_id = c.order_id
    WHERE cancellation IS NULL
    GROUP BY order_time
    ORDER BY pizza_count DESC
    LIMIT 1;


![image](https://github.com/user-attachments/assets/44781a52-22fc-4ff9-b4f9-d544da6e94b3)

+ It retrieves the `order_id` and calculates the number of pizzas delivered for each order (`orders_delivered`) from the `customer_orders_temp` table.
+ It performs an left join between the `customer_orders_tempp` table and the `runner_orders_temp` table on matching `order_id` to get the pizza delivery information.
+ The query filters the data using the `WHERE` clause, selecting only the rows where the cancellation column (cancellation) in the `runner_orders_temp` table is empty (i.e., no cancellation).
+ Results are grouped by `order_time` to calculate the count of pizzas delivered for each order.
+ The `COUNT(C.order_id`) function calculates the number of occurrences of each `order_id` in the `customer_orders_temp` table, giving the count of pizzas delivered for each order.
+ Next, the query selects the maximum value of `orders_delivered`.
+ As a result, the query presents the maximum count of pizzas delivered as `max_deliver_pizza` among all the orders from the `customer_orders_temp` and `runner_orders_temp` tables, excluding any orders with cancellations.

#### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?

    SELECT 
		customer_id,
		SUM(CASE WHEN exclusions IS NOT NULL OR extras IS NOT NULL THEN 1 ELSE 0 END) AS 'changes',
        SUM(CASE WHEN exclusions IS NULL AND extras IS  NULL THEN 1 ELSE 0 END) AS 'no_changes'
    FROM customer_orders_temp c
    LEFT JOIN runner_orders_temp r
    ON r.order_id = c.order_id
    WHERE cancellation IS NULL
    GROUP BY 1;


![image](https://github.com/user-attachments/assets/94557234-a034-4876-92fa-446cb381aadd)

+ It retrieves the `customer_id` and calculates two aggregates:
`changes` - the count of customers who have at least one change in their orders.
no_changes - the count of customers who have no changes in their orders (neither exclusions nor extras).
+ It performs an inner join between the `customer_orders_temp` table and the `runner_orders_temp` table on matching `order_id` to get the order information.
+ The query filters the data using the WHERE clause, selecting only the rows where the cancellation column (cancellation) in the `runner_orders_temp` table is empty (i.e., no cancellation).
+ The CASE statement is used to conditionally evaluate whether there are exclusions or extras in the orders for each customer.
+ The SUM function is then used to calculate the totals of `changes` and `no_changes` for each customer.
+ Next, the main query selects the sum of `changes` and `no_changes` from the CTE CTE.
+ The SUM function returns the sum of these values, giving the total count of customers who have at least one change in their orders and the total count of customers who have no changes in their orders.
+ As a result, the query presents the total count of customers with at least one change (`changes`) and the total count of customers with no changes (`no_changes`) among all the orders from the `customer_orders_temp` and `runner_orders_temp` tables, excluding any orders with cancellations.

#### 8. How many pizzas were delivered that had both exclusions and extras?


 
	SELECT 
		count(c.pizza_id) AS pizza_count
    FROM customer_orders_temp c
    LEFT JOIN runner_orders_temp r
    ON r.order_id = c.order_id
    WHERE  cancellation IS NULL
	  AND exclusions IS NOT NULL 
	  AND extras IS NOT NULL;

![image](https://github.com/user-attachments/assets/fb15a399-26d5-4a2f-b04f-7fd341a14c24)

+ The SQL query calculates the count of pizzas with both `exclusions` and `extras` from the `customer_orders_temp` table.
+ It performs an left join between the `customer_orders_temp` table and the `runner_orders_temp` table on matching `order_id` to get the order information.
+ The query filters the data using the `WHERE` clause, selecting only the rows where the cancellation column (cancellation) in the `runner_orders_temp` table is empty (i.e., no cancellation).
+ The `CASE` statement is used to conditionally evaluate whether there are both exclusions and extras in the orders for each pizza.
+ The SUM function then calculates the total count of pizzas with both exclusions and extras by summing up the ones that meet the specified condition.
+ As a result, the query presents the total count of pizzas with both exclusions and extras among all the orders from the `customer_orders_temp` and `runner_orders_temp` tables, excluding any orders with cancellations.

#### 9. What was the total volume of pizzas ordered for each hour of the day?

    SELECT 
		HOUR(order_time) AS hr,
        COUNT(pizza_id) AS pizza_count
    FROM customer_orders_temp
    GROUP BY 1
    ORDER BY hr ASC;


![image](https://github.com/user-attachments/assets/b290c886-8731-48fc-bba2-366a645f7560)

+ The SQL query selects the hours portion of the `order_time` and the count of orders (pizza_count) from the `customer_orders_temp` table.
+ It uses the `HOUR` function with HOURS to extract the hour portion from the `order_time`.
+ Results are grouped by the extracted hour value to calculate the count of orders for each hour.
+ The `COUNT(pizza_id)` function calculates the number of occurrences of each `order_id` in the `customer_orders_temp` table, giving the count of orders for each hour.
+ The query presents the extracted hours as hours and the corresponding count of orders as pizza ordered.
+ Finally, the results are sorted in ascending order based on the extracted hours.

#### 10. What was the volume of orders for each day of the week?


    SELECT
		dayname(order_time) AS days,
        COUNT(pizza_id) AS pizza_count
    FROM customer_orders_temp
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/3f73b468-bca2-4bb9-a55a-6a51bae33591)

+ The SQL query selects the day of the week from the `order_time` and the count of orders (`pizza_count`) from the `customer_orders_tempp` table.
+ It uses the `DAY_NAME`function to convert the `order_time` into a textual representation of the day of the week (DAY).
+ Results are grouped by the textual representation of the day of the week to calculate the count of orders for each day.
+ The `COUNT(pizza_id) `function calculates the number of occurrences of each `order_id` in the `customer_orders_temp` table, giving the count of orders for each day of the week.
+ The query presents the textual representation of the day of the week as day and the corresponding count of orders as `ordered_pizza`.
+ Finally, the results are sorted based on the textual representation of the day of the week.



### B. Runner And Customer Experience 💁‍♂️🍕

#### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)


     SELECT WEEK(registration_date,1) AS weeks,
       COUNT(runner_id) AS signed_runner_week
    FROM runners
    GROUP BY weeks
    ORDER BY weeks;

![image](https://github.com/user-attachments/assets/1919ec13-cd2a-4897-afc2-6620246837bc)

+ The SQL query retrieves the week number of the year from the `registration_date` column in the runners table and the count of signed-up runners for each week (`signed_runner_week`).
+ It uses the `WEEK` function to convert the `registration_date` to a text representation of the week number of the year ('ww').
+ Results are grouped by the `week number` of the year to calculate the count of `signed-up runners` for each week.
+ The `COUNT(runner_id)` function calculates the number of occurrences of each `runner_id` in the runners table, giving the count of signed-up runners for each week.
+ As a result, the query presents the week number of the year as weeks and the corresponding count of signed-up runners as `signed_runner_week`..

#### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pick up the order?


	WITH CTE AS (
          SELECT 
               c.order_id,
               r.runner_id,
               timestampdiff(MINUTE , order_time, pickup_time) AS time_diff
          FROM  
	           customer_orders_temp c
         LEFT JOIN 
               runner_orders_temp r ON r.order_id = c.order_id
         GROUP BY 
               1,2, c.order_time, r.pickup_time)
    SELECT 
	    runner_id,
        ROUND(AVG(time_diff),0) AS time_diff
    FROM CTE
    GROUP BY 1;


![image](https://github.com/user-attachments/assets/a528deef-9875-4ee6-8ff9-1a84d35126d0)

+ The SQL query starts by creating a Common Table Expression (CTE) named CTE.
+ Within the CTE, it retrieves the `order_id`, `order_time`, `pickup_time`, and calculates the time difference in minutes `time_diff` between the `pickup_time` and `order_time` for each order from the `runner_orders_temp` and `customer_orders_temp` tables.
+ It performs an left join between the `runner_orders_temp` table and the `customer_orders_temp` table on matching `order_id` to get the order and pickup time information.
+ Results are grouped by `order_id`, `order_time`, and `pickup_time` to calculate the time difference for each order.
+ The `TIMESTAMPDIFF(minutes, order_time. pickup_time)` expression calculates the time difference in minutes between the `pickup_time` and `order_time` for each order.
+ Next, the main query retrieves the value of the average of all values from the CTE CTE..
+ As a result, the query presents the integer value of the average pickup time for all orders with pickup times after the order times from the `runner_orders_temp` and `customer_orders_temp` tables, excluding any orders with cancellations.

#### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?


     WITH CTE AS (
		SELECT 
			count(c.pizza_id) AS pizza_count,
			TIMESTAMPDIFF(MINUTE, c.order_time, r.pickup_time) AS time_diff
		FROM customer_orders_temp c 
		LEFT JOIN runner_orders_temp r
		ON r.order_id = c.order_id
		GROUP BY  order_time, pickup_time)
    SELECT 
	pizza_count,
    ROUND(AVG(time_diff),0) AS time_diff
    FROM CTE
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/62c3f701-1174-4db3-8fff-54e9c399d4a6)

+ The SQL query starts by creating a Common Table Expression (CTE) named CTE.
+ Within the CTE, it calculates the count of pizzas ordered for each order, calculates the time difference in minutes (min) between the `pickup_time` and `order_time` for each order from the `customer_orders_temp` and `runner_orders_temp` tables.
+ It performs an left join between the `customer_orders_temp` table and the `runner_orders_temp` table on matching `order_id` to get the order and pickup time information.
+ Results are grouped by `order_time`, and `pickup_time` to calculate the count of pizzas ordered and the time difference (min) for each order.
+ The `COUNT(C.pizza_id) function calculates the number of occurrences of each `order_id` in the `customer_orders_tempp` table, giving the count of pizzas ordered for each order.
+ The `TIMESTAMPDIFF(minutes, order_time, pickup_time)` expression calculates the time difference in minutes between the `pickup_time` and `order_time` for each order.
+ Next, the main query retrieves the count of pizzas ordered (pizza_order) and the rounded average of all min values (average preparation time in minutes) for orders.
+ The `ROUND(AVG(min),2)` expression calculates the average preparation time for pizzas and rounds it to two decimal places.
+ Results are grouped by `pizza_count`, which represents the count of pizzas ordered for each order.
+ As a result, the query presents the count of pizzas ordered (pizza_count) and the rounded average preparation time in minutes for orders from the `customer_orders_tempp` and `runner_orders_temp` tables, excluding any

#### 4. What was the average distance travelled for each customer?

       SELECT 
		customer_id,
        ROUND(AVG(distance),2) AS dist_km
      FROM customer_orders_temp c
      LEFT JOIN runner_orders_temp r
      ON c.order_id = r.order_id
      GROUP BY 1;

![image](https://github.com/user-attachments/assets/afb9362e-dadc-4d00-a4ab-2e01a6dfba4b)

+  The SQL query retrieves the `customer_id` and calculates the rounded average distance traveled for each customer from the `customer_orders_tempp` and `runner_orders_temp` tables.
+ It performs an left join between the `customer_orders_tempp` table and the `runner_orders_temp` table on matching `order_id` to get the order and delivery information.
+ Results are grouped by `customer_id` to calculate the average distance traveled for each customer.
+ The `ROUND(AVG(distance),2)` expression calculates the rounded average distance for each customer to two decimal places.
+ As a result, the query presents the `customer_id` and the corresponding rounded average distance traveled as average for each customer from the `customer_orders_tempp` and `runner_orders_temp` tables, excluding any orders with cancellations.

#### 5. What was the difference between the longest and shortest delivery times for all orders?

       SELECT
		MAX(duration) AS longest_delivery,
            MIN(duration) AS shortest_delivery,
            MAX(duration) - MIN(duration) as time_diff
      FROM runner_orders_temp;

![image](https://github.com/user-attachments/assets/24b011a7-8742-4d4a-a38c-199dfbc7da0f)

+ The SQL query calculates the longest delivery duration (`long_delivery`), shortest delivery duration (`shortest_delivery`), and the difference between the longest and shortest durations from the `runner_orders_temp` table.
+ The `MAX(duration)` function calculates the maximum value of the duration column from the `runner_orders_temp` table, representing the longest delivery duration.
+ The `MIN(duration)` function calculates the minimum value of the duration column from the `runner_orders_temp` table, representing the shortest delivery duration.
+ The `MAX(duration) - MIN(duration)` expression calculates the difference between the longest and shortest delivery durations.
+ As a result, the query presents the longest delivery duration as `long_delivery`, the shortest delivery duration as `shortest_delivery`, and the difference between the two durations as difference from the `runner_orders_temp` table.

#### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?


    SELECT 
		runner_id,
            order_id,
            ROUND(distance/duration*60,2) AS avg_speed
    FROM runner_orders_temp
    WHERE cancellation IS NULL
    ORDER BY 1 ASC;

![image](https://github.com/user-attachments/assets/d8cc48fd-4f85-49d6-8c1a-268b0241c4f5)

+ It retrieves `runner_id`, `order_id` and `avg_speed`.
+ The query filters the data using the WHERE clause, selecting only the rows where the cancellation column (cancellation) in the `runner_orders_temp` table is empty (i.e., no cancellation).
+ The ROUND(distance/duration*60,2) expression calculates the delivery duration in hours for each order.



#### 7. What is the successful delivery percentage for each runner?


    SELECT 
		runner_id,
            ROUND(COUNT(pickup_time)/COUNT(order_id)*100,2) AS delivery_pct
    FROM runner_orders_temp
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/32c18200-3247-4de2-bfa6-c352c11c883f)


+ It retrieves the `runner_id` and `ROUND(COUNT(pickup_time)/COUNT(order_id)*100,2)` to find the percentage of successful deliveries


### C. Ingredient Optimization 🍕


+ Created a table called numbers, it will be used to split columns values into individual rows


        CREATE TABLE numbers (
             num INT PRIMARY KEY
        );

        INSERT INTO numbers (num)
        VALUES (1), (2), (3), (4), (5), (6), (7), (8), (9), (10),
            (11), (12), (13), (14), (15), (16), (17), (18), (19), (20);

#### 1. What are the standard ingredients for each pizza?



    SELECT WEEK(registration_date,1) AS weeks,
    WITH final_table AS (
		SELECT 
			pizza_id,
			TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(toppings, ',', num), ',', -1)) AS topping_id,
			topping_name
		FROM
			(SELECT 
				pizza_id,
				toppings,
				LENGTH(toppings) - LENGTH(REPLACE(toppings, ',', '')) +1 AS toppings_nums,
				numbers.num
			FROM pizza_recipes
			JOIN numbers
			ON numbers.num <= LENGTH(toppings) - LENGTH(REPLACE(toppings, ',', '')) +1) a
		LEFT JOIN pizza_toppings
		ON TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(toppings, ',', num), ',', -1)) = pizza_toppings.topping_id)
    SELECT
		pizza_id,
        group_concat(topping_name ORDER BY topping_name SEPARATOR ', ') AS 
    toppings
    FROM final_table
    GROUP BY 1;

![image](https://github.com/user-attachments/assets/cd4032c2-d2cf-49a9-ad0a-b68effe1f957)


#### 2. What was the most commonly added extra?


	WITH final_table AS (
		SELECT 
			extras,
			TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(extras, ',', num),',', -1)) as extras_id
		FROM
			(SELECT 
				extras,
				LENGTH(extras) - LENGTH(REPLACE(extras,',','')) +1 AS no_of_extras,
				num
			FROM customer_orders_temp
			JOIN numbers
			ON  numbers.num <= LENGTH(extras) - LENGTH(REPLACE(extras,',','')) +1
			WHERE extras IS NOT NULL) a)
     SELECT 
        topping_name,
        COUNT(extras_id) AS extra_counts
    FROM final_table
    LEFT JOIN pizza_toppings p
    ON p.topping_id = final_table.extras_id
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 1;


![image](https://github.com/user-attachments/assets/1c1c30f7-fafc-4f3c-a6b0-b467b1da26ee)

#### 3. What was the most common exclusion?



     WITH final_table AS (
		SELECT 
			exclusions,
			TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(exclusions, ',', num), ',' , -1)) as exclusion_id
		FROM (
			SELECT 
				exclusions,
				LENGTH(exclusions) - LENGTH(REPLACE(exclusions, ',' ,''))+ 1 as no_of_exclusions,
				num
			FROM customer_orders_temp
			JOIN numbers
			ON LENGTH(exclusions) - LENGTH(REPLACE(exclusions, ',' ,''))+ 1 >= numbers.num
			WHERE exclusions IS NOT NULL) A)
    SELECT 
        topping_name,
        COUNT(exclusion_id) as exclusion_count
    FROM final_table
    LEFT JOIN pizza_toppings p 
    ON p.topping_id = final_table.exclusion_id
    GROUP BY 1
    ORDER BY 2 DESC
    LIMIT 1;

![image](https://github.com/user-attachments/assets/3ebf4da5-7592-47e0-a696-b45b8ab311b5)


#### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
+ Meat Lovers
+ Meat Lovers - Exclude Beef
+ Meat Lovers - Extra Bacon
+ Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers


       SET @ row_number := 0:
       WITH orders AS (
       SELECT
       @row_number := @row_number +1 as id,
		p.pizza_name,
		(SELECT GROUP_CONCAT(pt.topping_name SEPARATOR ', ')
		FROM pizza_toppings pt
		WHERE FIND_IN_SET(pt.topping_id, REPLACE(c.exclusions, ' ', '')) > 0
        ) AS exclusion,
        (SELECT GROUP_CONCAT(pt.topping_name SEPARATOR ', ')
		FROM pizza_toppings pt
		WHERE FIND_IN_SET(pt.topping_id, REPLACE(c.extras, ' ', '')) > 0
        ) AS extra,
        order_time
      FROM customer_orders_temp c
			LEFT JOIN pizza_names p
			ON p.pizza_id = c.pizza_id
      )
      SELECT
           id,
           CONCAT(
           pizza_name,
           CASE
               WHEN exclusion IS NULL THEN ''
               ELSE CONCAT(' - Exclude ', exclusion)
            END,
           CASE
               WHEN extra IS NULL THEN ''
               ELSE CONCAT(' - Extras ', extra)
            END
        ) AS order_items
      FROM orders;

![image](https://github.com/user-attachments/assets/5a2c3b54-62db-4039-bf99-7f0b99fe8c3d)


### Key Insights

+ Analyzing pizza orders and customer preferences can guide menu optimization.
+ Identifying top-performing runners ensures efficient delivery and customer satisfaction.
+ Understanding customer order patterns aids in targeted promotions and staffing.
+ Ingredient optimization reduces food waste and enhances inventory management.
+ Data cleaning ensures accurate analysis and informed decision-making.
Improving the customer experience fosters loyalty and business growth.
+ Data-driven decisions lead to better resource allocation and growth opportunities.
+ Runner Sign-Ups: Analyzing the number of runners signing up each week helps identify trends and patterns in runner recruitment and engagement.
+ Delivery Time Analysis: Calculating the average time it takes for runners to arrive at Pizza Runner HQ for order pickups can reveal efficiency levels and potential areas for improvement in the delivery process.
+ Order Preparation and Pizza Count: Exploring any relationship between the number of pizzas ordered and the time it takes to prepare them can assist in optimizing kitchen operations and delivery timelines.
Customer Average Distance: Understanding the average distance traveled for each customer's orders can help identify delivery patterns and optimize delivery routes.
+ Longest and Shortest Delivery Times: Analyzing the difference between the longest and shortest delivery durations can reveal variations in delivery efficiency and potential outliers.
+ Runner Average Speed: Calculating the average speed of each runner for each delivery provides insights into their performance and helps identify high-performing runners.
+ Successful Delivery Percentage: Determining the successful delivery percentage for each runner allows assessing their reliability and performance in completing orders.
+ Standard Ingredients for Each Pizza: Understanding the standard ingredients for each pizza helps identify the base recipe and essential toppings used for various pizza types.
+ Most Commonly Added Extra: Analyzing the most commonly added extra topping provides insights into customer preferences and popular customization choices.
+ Most Common Exclusion: Identifying the most common exclusion topping helps understand popular preferences for omitting specific ingredients.
Generating Order Items: Creating formatted order items for each record in the customer_orders table allows presenting detailed information about each order, including the pizza name, excluded toppings, and extra toppings.

