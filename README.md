# 8-week-sql-challenge
My solutions to the 8-week SQL challenge. <br>
**Note:** The author gives permission to use this project as a portfolio project on his webpage. <br>

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
What is the most purchased item on the menu and how many times was it purchased by all customers? <br>

```
SELECT m.product_name, COUNT(m.product_name) as times_sold
FROM dannys_diner.sales s 
INNER JOIN dannys_diner.menu m
ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY times_sold DESC
LIMIT 1;
```

I grouped by product_name, summarizing each group with COUNT as an aggregate function. Then, get the biggest number with ORDER BY and LIMIT. This seems simple enough to me, but it could also be done with subqueries or the WITH statement so that you double-up on aggregate functions.

### Question 5
Which item was the most popular for each customer? <br>

```
WITH item_ranks AS 
(SELECT customer_id, product_id,
		COUNT(product_id) AS item_count,
        RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(product_id) DESC) AS item_rank
FROM dannys_diner.sales
GROUP BY customer_id, product_id)

SELECT s.customer_id, m.product_name
FROM item_ranks s
INNER JOIN dannys_diner.menu m
ON s.product_id = m.product_id
WHERE s.item_rank = 1
ORDER BY customer_id ASC;
```

In this one, I first got the amount of each product bought by each customer. Then, I partitioned by customer to see the rank of each item for each customer. In the second part of the query, I filter to where rank = 1 and join with the menu, which has the name of the item rather than product_id.

### Question 6
Which item was purchased first by the customer after they became a member? <br>

```
WITH order_by_date_difference AS
(SELECT s.customer_id, m.product_name,
	RANK() OVER(PARTITION BY s.customer_id 
                ORDER BY mems.join_date -  s.order_date DESC)
                AS time_difference
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m ON s.product_id = m.product_id
INNER JOIN dannys_diner.members mems ON s.customer_id = mems.customer_id
WHERE mems.join_date -  s.order_date <= 0)

SELECT customer_id, product_name
FROM order_by_date_difference
WHERE time_difference = 1;
```

I realized if you get join_date - order_date, a negative result will indicate an order after becoming a member. If the result is 0, the customer made an order and became a member on the same day. So, we want the greatest int from [-infinity,0] 

I realize now that I could have done this with some easier comparisons, for example I could have ordered by join_date DESC in my window function and then done WHERE join_date < order_date.

### Question 7 
Which item was purchased just before the customer became a member? <br>

```
WITH next_eaten AS
(SELECT s.customer_id, m.product_name,
	RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date ASC)
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m ON s.product_id = m.product_id
INNER JOIN dannys_diner.members mems ON s.customer_id = mems.customer_id
WHERE mems.join_date < s.order_date)

SELECT customer_id, product_name 
FROM next_eaten
WHERE rank=1;
```

This one was quite similar to #6, so I took the opportunity to do my comparisons in a more clear way which was easier to understand.

### Question 8


