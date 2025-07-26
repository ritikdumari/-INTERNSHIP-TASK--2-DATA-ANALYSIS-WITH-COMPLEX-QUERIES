# Advanced SQL Analysis  
## Using Window Functions, Subqueries, and CTEs

---

### Sample Data

**sales**

| sale_id | emp_id | amount | sale_date  |
|---------|--------|--------|------------|
| 1       | 101    | 500    | 2024-01-01 |
| 2       | 101    | 300    | 2024-01-10 |
| 3       | 102    | 700    | 2024-01-15 |
| 4       | 103    | 200    | 2024-01-20 |
| 5       | 101    | 400    | 2024-01-22 |

**employees**

| emp_id | emp_name | dept_id |
|--------|----------|---------|
| 101    | Alice    | 10      |
| 102    | Bob      | 20      |
| 103    | Charlie  | 10      |

**departments**

| dept_id | dept_name     |
|---------|---------------|
| 10      | HR            |
| 20      | Engineering   |
| 30      | Marketing     |

---

### 1. Window Function - ROW_NUMBER()

```sql
SELECT 
  emp_id,
  amount,
  sale_date,
  ROW_NUMBER() OVER (PARTITION BY emp_id ORDER BY sale_date DESC) AS rn
FROM sales;
| emp\_id | amount | sale\_date | rn |
| ------- | ------ | ---------- | -- |
| 101     | 400    | 2024-01-22 | 1  |
| 101     | 300    | 2024-01-10 | 2  |
| 101     | 500    | 2024-01-01 | 3  |
| 102     | 700    | 2024-01-15 | 1  |
| 103     | 200    | 2024-01-20 | 1  |


2. Subquery - Employees with Total Sales > 1000
sql
Copy code
SELECT emp_id, total_sales
FROM (
  SELECT emp_id, SUM(amount) AS total_sales
  FROM sales
  GROUP BY emp_id
) AS sub
WHERE total_sales > 1000;


Output:

emp_id	total_sales
101	1200


3. CTE - Best Sale by Each Employee
WITH ranked_sales AS (
  SELECT 
    emp_id,
    amount,
    sale_date,
    ROW_NUMBER() OVER (PARTITION BY emp_id ORDER BY amount DESC) AS rn
  FROM sales
)
SELECT emp_id, amount, sale_date
FROM ranked_sales
WHERE rn = 1;


output
| emp\_id | amount | sale\_date |
| ------- | ------ | ---------- |
| 101     | 500    | 2024-01-01 |
| 102     | 700    | 2024-01-15 |
| 103     | 200    | 2024-01-20 |



4. CTE with JOIN - Department-wise Total Sales

WITH total_sales AS (
  SELECT emp_id, SUM(amount) AS total_sales
  FROM sales
  GROUP BY emp_id
)
SELECT e.emp_name, d.dept_name, t.total_sales
FROM total_sales t
JOIN employees e ON t.emp_id = e.emp_id
JOIN departments d ON e.dept_id = d.dept_id;

output
| emp\_name | dept\_name  | total\_sales |
| --------- | ----------- | ------------ |
| Alice     | HR          | 1200         |
| Bob       | Engineering | 700          |
| Charlie   | HR          | 200          |




