# Case Study #1 - Danny's Diner👨🏻‍🍳  
## Contents

#### Introduction
1. Problem Statement

2. Entity Relationship Diagram

3. Case Study Questions & Solutions

4. Key Insights

## Introduction
##### In early 2021, Danny follows his passion for Japanese food and opens "Danny's Diner," a charming restaurant offering sushi, curry, and ramen. However, lacking data analysis expertise, the restaurant struggles to leverage the basic data collected during its initial months to make informed business decisions. Danny's Diner seeks assistance in using this data effectively to keep the restaurant thriving.

## Problem Statement

##### Danny aims to utilize customer data to gain valuable insights into their visiting patterns, spending habits, and favorite menu items. By establishing a deeper connection with his customers, he can provide a more personalized experience for his loyal patrons.

He plans to use these insights to make informed decisions about expanding the existing customer loyalty program. Additionally, Danny seeks assistance in generating basic datasets for his team to inspect the data conveniently, without requiring SQL expertise.

Due to privacy concerns, he has shared a sample of his overall customer data, hoping it will be sufficient for you to create fully functional SQL queries to address his questions.

The case study revolves around three key datasets:

1. Sales

2. Menu

3. Members

## Case Study Questions & Solutions

#### 1. What is the total amount each customer spent at the restaurant?

    SELECT 
	    customer_id, 
        SUM(M.price)AS total_amount
    FROM SALES S
    LEFT JOIN menu M
    ON M.product_id = S.product_id
    GROUP BY customer_id
    ORDER BY 1;

+ The SQL query retrieves the `customer_id` and calculates the total amount spent (`total_amnt`) by each customer at the restaurant.
+ It combines data from the `sales` and `menu` tables based on matching `product_id`.
+ The results are grouped by `customer_id`.
+ The query then calculates the total sum of `price` for each group of sales records with the same `customer_id`.
+ Finally, the results are sorted in ascending order based on the `customer_id`.

#### 2. How many days has each customer visited the restaurant?
      SELECT
		customer_id,
            COUNT(DISTINCT order_date) AS days
     FROM SALES
     GROUP BY customer_id;

+ The SQL query selects the `customer_id` and counts the number of distinct order dates (`No_Days`) for each customer.
+ It retrieves data from the `sales table`.
+ The results are grouped by `customer_id`.
 The `COUNT(DISTINCT order_date`) function calculates the number of unique order dates for each customer.
+ Finally, the query presents the total number of unique order dates as `No_Days` for each customer.

#### 3. What was the first item from the menu purchased by each customer?

     WITH orders AS (
     SELECT 
           customer_id,
	       DENSE_RANK() OVER(PARTITION BY customer_id ORDER BY order_date) as rnk,
	       m.product_name
	FROM SALES s
	LEFT JOIN menu m
	ON m.product_id = s.product_id)
    
      SELECT 
		customer_id,
            product_name
      FROM orders
      WHERE rnk = 1;

+The SQL query uses a Common Table Expression (CTE) named `orders` to generate a temporary result set.
+ Within the CTE, it selects the `customer_id`, assigns a dense rank to each row based on the order date for each customer, and retrieves the corresponding `product_name` from the menu table.
+ The sales table is joined with the menu table on matching `product_id`.
+ The `DENSE_RANK()` function assigns a rank to each row within the partition of each `customer_id` based on the `order_date` in ascending order.
+ Each `customer_id` has its own partition and separate ranks based on the order dates of their purchases.
+ Next, the main query selects the `customer_id` and corresponding `product_name` from the CTE.
+ It filters the results and only includes rows where the rank `rnk` is equal to 1, which means the earliest purchase for each `customer_id`.
+ As a result, the query returns the first purchased product for each customer.

#### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?


       SELECT 
		m.product_name,
            COUNT(s.product_id) AS count
      FROM SALES s
      LEFT JOIN menu m
      ON m.product_id = s.product_id
      GROUP BY 1
      ORDER BY count DESC
      LIMIT 1;

+ The SQL query selects the `product_name` from the menu table and counts the number of times each product was ordered (`count`).
+ It retrieves data from the `Sales table` and joins it with the menu table based on matching `product_id`.
+ The results are grouped by `product_name`.
+ The COUNT(`S.product_id`) function calculates the number of occurrences of each `product_id` in the Sales table.
+ The query then presents the `product_name` and its corresponding count as `count` for each product.
+ Next, the results are sorted in descending order based on the `most_ordered` column, so the most ordered product appears first.
+ The `LIMIT 1` clause is used to restrict the result to only one row, effectively showing the most ordered product.

#### 5. Which item was the most popular for each customer?

     WITH orders AS (
		SELECT
				s.customer_id,
				m.product_name,
				COUNT(m.product_id) as counts,
				DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY COUNT(m.product_id) DESC) AS rnk
		FROM SALES S
		LEFT JOIN menu m
		ON m.product_id = s.product_id
		GROUP BY customer_id, 2)
     SELECT 
		customer_id,
		product_name,
        counts
     FROM orders
     WHERE rnk =  1;

+The SQL query uses a Common Table Expression (CTE) named CTE to generate a temporary result set.
+ Within the CTE, it selects the `customer_id`, `product_name`, and counts the number of times each product was ordered (`counts`) for each customer.
+ It retrieves data from the sales table and joins it with the menu table based on matching `product_id`.
+ The results are grouped by `customer_id` and `product_name` to get the count of orders for each product of each customer.
+ The COUNT(`M.product_id`) function calculates the number of occurrences of each `product_id` in the menu table.
+ The `DENSE_RANK()` function assigns a rank to each row within the partition of each `customer_id` based on the order count of products in descending order.
+ Each `customer_id` has its own partition and separate ranks based on the number of product orders.
+ Next, the main query selects the `customer_id`, `product_name`, and `counts` from the CTE.
+ It filters the results and only includes rows where the rank `rnk` is equal to 1, which means the most ordered product for each `customer_id`.
+ As a result, the query returns the `customer's ID`, the most ordered product, and the number of times it was ordered by that customer.

#### 6. Which item was purchased first by the customer after they became a member?

    WITH orders AS (
		SELECT 
		    s.customer_id,
		    menu.product_name,
		    DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date ASC) AS rnk
		FROM SALES s
		JOIN members m
		ON m.customer_id = s.customer_id
		LEFT JOIN menu
		ON menu.product_id = s.product_id
		WHERE s.order_date > m.join_date)
    SELECT 
		customer_id,
            product_name
    FROM orders
    WHERE rnk = 1;

+ The SQL query retrieves distinct rows for each unique `customer_id` with their corresponding `product_name` from the sales and menu tables.
+ It filters the data based on the condition that the `order_date` in the sales table is greater than the `join_date` of the customer in the members table.
+ The sales table is aliased as s, the members table is aliased as `m`.
+ The query performs inner joins between `sales` and `members` tables on matching `customer_id` and between sales and menu tables on matching `product_id`.
+ Only rows that meet the join condition and the `order_date` > `join_date` condition are considered in the result set.
+ The query selects the `customer_id` and corresponding `product_name` for each customer who has placed an order after their `join_date`.
+ The results are sorted in ascending order based on the `customer_id`.
+ The `DISTINCT ON (s.customer_id`) clause ensures that only the first occurrence of each `customer_id` is included in the result set.
+ As a result, the query returns a unique list of `customer_id` along with the first `product_name` they ordered after joining as a member.

#### 7. Which item was purchased just before the customer became a member?

    WITH orders AS (
		SELECT 
			s.customer_id,
			menu.product_name,
			DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rnk
		FROM SALES s
		JOIN members m
		ON m.customer_id = s.customer_id
		LEFT JOIN menu
		ON menu.product_id = s.product_id
		WHERE s.order_date < m.join_date)
    SELECT 
		customer_id,
            product_name
    FROM orders
    WHERE rnk = 1;

+ The SQL query retrieves distinct rows for each `customer_id` with their corresponding `product_name` from the `sales` and `menu` tables.
+ It filters the data based on the condition that the `order_date` in the sales table is less than the `join_date` of the customer in the members table.
+ The `sales` table is aliased as `s`, the `members` table is aliased as `m`.
+ The query performs `left joins` between `sales` and `members` tables on matching `customer_id` and between sales and menu tables on matching `product_id`.
+ Only rows that meet the join condition and the `order_date` < `join_date` condition are considered in the result set.
+ The query selects the `customer_id` and corresponding `product_name` for each customer who has placed an order before their `join_date`.
+ The results are sorted in ascending order based on the `customer_id`.
+ As a result, the query returns a list of `customer_id` along with the last`product_name` they ordered before joining as a member.

#### 8. What is the total items and amount spent for each member before they became a member?


    SELECT 
		s.customer_id,
            COUNT(s.product_id) AS total_items,
            SUM(m.price) AS total_amt
    FROM SALES s
    LEFT JOIN menu m
    ON m.product_id = s.product_id
    JOIN members 
    ON members.customer_id = s.customer_id
    WHERE members.join_date > s.order_date
    GROUP BY 1;

+ The SQL query retrieves the customer_id along with the `total count` of items ordered (`total_item`) and the total amount spent (`total_amt`) by each customer.
+ It retrieves data from the sales table and joins it with the menu table based on matching `product_id`.
+ It also joins the sales table with the members table based on matching `customer_id`.
+ The results are filtered based on the condition that the `order_date` in the sales table is less than the `join_date` of the customer in the members table.
+ The `COUNT(S.product_id)` function calculates the number of occurrences of each `product_id` in the sales table, giving the total number of items ordered by each customer.
+ The `SUM(M.price)` function calculates the sum of the price from the menu table, providing the total amount spent by each customer.
+ Results are grouped by `customer_id` to get the totals for each customer.
+ The query then presents the customer_id, total_item, and total_amont for each customer who placed orders before joining as a member.
+ Finally, the results are sorted in ascending order based on the customer_id.

#### 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

    SELECT 
		s.customer_id,
            SUM(CASE WHEN product_name = 'sushi' THEN price * 2
			ELSE price END) *10 AS points
    FROM SALES s
    LEFT JOIN menu m
    ON m.product_id = s.product_id
    GROUP BY 1;

+ The SQL query retrieves the `customer_id` and calculates the total points (`points`) earned by each customer based on their purchases from the sales and menu tables.
+ It retrieves data from the `sales` table and joins it with the `menu` table based on matching `product_id`.
+ The query uses a `CASE` statement to differentiate between 'sushi' and other products.
+ If the product name is '`sushi`', the price is multiplied by 2 to give double points.
+ Otherwise, the regular price is considered.
+ The `SUM` function calculates the total points for each customer by adding up the points earned from their purchases.
+ The total points are then multiplied by 10 to give a scaled value.
+ Results are grouped by `customer_id` to get the total points for each customer.
+ The query then presents the `customer_id` and the scaled `total_points` for each customer based on their purchases.

#### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

    SELECT 
		s.customer_id,
            SUM(CASE WHEN s.order_date BETWEEN m.join_date and DATE_ADD(join_date, INTERVAL 6 DAY) OR product_name ='sushi'  
				THEN price*2
		    ELSE price END) * 10 AS points
    FROM SALES s
    JOIN members m
    ON m.customer_id = s.customer_id
    LEFT JOIN menu
    ON menu.product_id = s.product_id
    WHERE order_date <= '2021-01-31'
    GROUP BY 1;

+ It selects `customer_id`, `join_date`, `join_date` + INTERVAL '6 days'.
+ Next, the query selects the `customer_id` and calculates the total points (`points`) earned by each customer based on their purchases from the sales and menu tables.
+ The query uses a `CASE` statement to differentiate between '`sushi`' purchases and other products.
+ If the product name is '`sushi`' or the order date falls within the range of join_date to valid_date, the points are calculated as 2 times 10 times the price of the product.
+ Otherwise, for other products, the points are calculated as 10 times the `price` of the product.
+ The `SUM` function calculates the total points for each customer by adding up the points earned from their purchases.
+ Results are grouped by `customer_id` to get the total points for each customer.
+ The query then presents the `customer_id` and the calculated points for each customer based on their purchases.


### Key Insights

+ Customer Spending: The total amount spent by each customer at Danny's Diner varies widely. Some customers have spent significantly more than others, indicating potential high-value customers or loyal patrons.
Customer Visits: The number of days each customer visited the restaurant also varies, showing different patterns of customer engagement. Some customers visit frequently, while others may visit less often.
+ First Purchases: Understanding the first items purchased by each customer can help Danny identify popular entry items and potentially attract new customers.
+ Most Popular Item: The most purchased item on the menu is valuable information for Danny. He can use this insight to optimize inventory management and capitalize on the popularity of the item.
+ Personalized Recommendations: Knowing the most popular item for each customer allows Danny to make personalized menu suggestions, enhancing the dining experience for his customers.
+ Customer Loyalty: The data about purchases before and after joining the loyalty program helps Danny evaluate the effectiveness of the loyalty program and its impact on customer behavior.
Bonus Points for New Members: By offering 2x points to new members during their first week, Danny incentivizes more spending, encouraging members to engage more actively with the program.
+ Member Points: The points earned by each member can be used to assess their loyalty and potentially offer targeted rewards and promotions.
+ Data Visualization: Creating visualizations based on the data and insights can further aid Danny in understanding trends and making data-driven decisions.
+ Customer Segmentation: By analyzing customer spending habits, Danny can segment his customer base and tailor marketing strategies accordingly.
Expanding Membership: Danny can use the insights from this data to refine his loyalty program and attract new members, leveraging the success of the program.
+ Inventory Management: Knowing the most popular and least popular items can help Danny optimize his inventory, reduce wastage, and maximize profits.
+ Menu Optimization: Danny can use the data to evaluate the performance of different menu items and consider introducing new dishes based on customer preferences.
+ Customer Engagement: Analyzing customer behavior can help Danny understand what keeps customers coming back and help him create more engaging experiences.
+ Long-Term Growth: By leveraging data analysis, Danny can make informed decisions that contribute to the long-term growth and success of Danny's Diner.


