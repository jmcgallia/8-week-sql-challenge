# 8-week-sql-challenge
My solutions to the 8-week SQL challenge.

## Case study one
[Problem Statement](https://8weeksqlchallenge.com/case-study-1/)

### Question 1
What is the total amount each customer spent at the restaurant? <br>
       
```
SELECT s.customer_id, SUM(m.price) as total_spent
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
ON s.product_id = m.product_id
GROUP BY s.customer_id;
```

### Question 2
How many days has each customer visited the restaurant? <br>

```
SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
FROM dannys_diner.sales
GROUP BY customer_id;
```

### Question 3
What was the first item from the menu purchased by each customer? <br>
**Solution notes**: It is possible that someone has 2+ 'first items,' since we only have the 'day ordered'
for our data and someone can order more than one thing on a given day. So, the results table may have 1+ rows for each customer, each row showing one of the first foods that they ordered (on the first day that they ordered.)

```
SELECT DISTINCT s.customer_id, m.product_name
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
ON s.product_id = m.product_id
WHERE s.order_date = (SELECT MIN(order_date)
                      FROM dannys_diner.sales
                      WHERE customer_id = s.customer_id)
```
In this one, I use a correlated subquery to find out when the order_date of a sale is equal to the MIN order_date found in a subquery (by comparing customer id's.)

OR 
```
WITH date_ranked_by_customer AS
(SELECT s.customer_id, 
 		RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date),
 		m.product_name
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
ON s.product_id = m.product_id)

SELECT DISTINCT customer_id, product_name
FROM date_ranked_by_customer
WHERE rank = 1;
```
In this one, I got to practice window functions which I had just learned, and happened to read about the WITH keyword, which was useful when it comes to organizing my code.

### Question 4

```
SELECT m.product_name, COUNT(m.product_name) as times_sold
FROM dannys_diner.sales s 
INNER JOIN dannys_diner.menu m
ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY times_sold DESC
LIMIT 1;
```

I grouped by product_name, summarizing each group with COUNT as an aggregate function. Then, get the biggest number with ORDER BY and LIMIT. This seems simple enough to me, but it could also be done with subqueries or the WITH statement.

