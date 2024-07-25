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
        GROUP BY customer_id;

