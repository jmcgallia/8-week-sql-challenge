# 8-week-sql-challenge
My solutions to the 8-week SQL challenge.

## Case study one
[Problem Statement](https://8weeksqlchallenge.com/case-study-1/)

### Question 1
What is the total amount each customer spent at the restaurant?

        SELECT s.customer_id, SUM(m.price) as total_spent
        FROM dannys_diner.sales s
        INNER JOIN dannys_diner.menu m
        ON s.product_id = m.product_id
        GROUP BY s.customer_id;
### Question 2
How many days has each customer visited the restaurant?

        SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
        FROM dannys_diner.sales
        GROUP BY customer_id;
### Question 3
What was the first item from the menu purchased by each customer?

        SELECT DISTINCT s.customer_id, m.product_name
        FROM dannys_diner.sales s
        INNER JOIN dannys_diner.menu m
        ON s.product_id = m.product_id
        WHERE s.order_date = (SELECT MIN(order_date)
                            FROM dannys_diner.sales
                            WHERE customer_id = s.customer_id)

OR 

