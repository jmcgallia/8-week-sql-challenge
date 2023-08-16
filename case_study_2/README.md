## Case study two - Part A
[Problem Statement](https://8weeksqlchallenge.com/case-study-2/)

In this case study, the data is imperfect. Some columns could be broken into two, and the format of the data is not consistent in many rows. So before doing anything else, I am making new tables with cleaned and reformatted data to make my solutions more strightforward.

Clean customer_orders:

WITH customer_orders AS (
	SELECT order_id, customer_id, pizza_id, 
  		CASE 
  			WHEN exclusions = 'NaN' OR exclusions = '' OR exclusions is NULL THEN NULL
  			ELSE exclusions
  		END AS exclusions,
  		CASE
  			WHEN extras = 'NaN' OR extras = '' OR extras IS NULL THEN NULL
  			ELSE extras
  		END AS extras
  	FROM pizza_runner.customer_orders
),

runner_orders AS (
	SELECT order_id, runner_id, pickup_time, TRIM(REPLACE(distance, 'km', '')) AS distance,
  	
  	FROM pizza_runner.runner_orders
)
  	  

SELECT * FROM runner_orders;

### Question 1
How many pizzas were ordered?

```
SELECT COUNT(order_id)
FROM pizza_runner.customer_orders;
```

### Question 2 
How many unique customer orders were made?

```
SELECT COUNT(DISTINCT order_id)
FROM pizza_runner.customer_orders;
```

### Question 3
How many successful orders were delivered by each runner?

```
WITH cleaned_runner_orders AS
(SELECT order_id, runner_id, pickup_time, distance, duration,
 CASE 
 	WHEN cancellation ILIKE '%cancel%' THEN 'CANCELLED'
 	ELSE 'COMPLETE'
 END AS cancelled,
 CASE 
 	WHEN cancellation ILIKE '%customer%' THEN 'CUSTOMER'
 	WHEN cancellation ILIKE '%restaurant%' THEN 'RESTAURANT'
 	ELSE 'none'
 END AS canceller
 FROM pizza_runner.runner_orders)
 
 SELECT runner_id, COUNT(*)
 FROM cleaned_runner_orders
 WHERE cancelled = 'COMPLETE'
 GROUP BY runner_id;
```

I wrote a query to clean the data first. Hopefully, this query will be useful in later problems.

### Question 4

```
SELECT pizza_name, COUNT(*)
FROM pizza_runner.customer_orders co
INNER JOIN pizza_runner.pizza_names USING (pizza_id)
INNER JOIN cleaned_runner_orders USING (order_id)
WHERE cancelled = 'COMPLETE'
GROUP BY pizza_name;		
```

My solution uses cleaned_runner_orders from question 3. I found out about the USING keyword, which makes the code less verbose.


