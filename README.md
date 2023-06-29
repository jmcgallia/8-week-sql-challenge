# 8-week-sql-challenge
My solutions to the 8-week SQL challenge.

<details>
<summary><h2>Case Study One</h2></summary>
  <a href="https://8weeksqlchallenge.com/case-study-1/">Problem statement</a><br>
  These are my solutions for case study one:
  <ol>
    <li>Problem 1
      <code>
        SELECT s.customer_id, SUM(m.price) as total_spent
        FROM dannys_diner.sales s
        INNER JOIN dannys_diner.menu m
        ON s.product_id = m.product_id
        GROUP BY s.customer_id;
      </code>
      </li>
    <li>Problem 2
      <code>
        SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
        FROM dannys_diner.sales
        GROUP BY customer_id;
      </code>
    </li>
    <li>Problem 3</li>
  </ol>
</details>
