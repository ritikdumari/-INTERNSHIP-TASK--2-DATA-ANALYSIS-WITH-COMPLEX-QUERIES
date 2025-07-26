#  SQL Report: Sales Trends and Patterns

**Internship Task**  
**Objective**: Using SQL queries to analyze trends and patterns in sales data.

---

## Database Tables Used

Assumed tables from a retail-like database:

- `orders(order_id, customer_id, order_date, status)`
- `order_items(order_item_id, order_id, product_id, quantity, subtotal, price)`
- `products(product_id, product_name, category_id)`
- `categories(category_id, category_name)`

---

## 1. Monthly Sales Analysis

```sql
SELECT
  DATE_FORMAT(order_date, '%Y-%m') AS month,
  SUM(order_items.subtotal) AS total_sales
FROM orders
JOIN order_items ON orders.order_id = order_items.order_id
WHERE orders.status = 'COMPLETE'
GROUP BY month
ORDER BY month;

 2. Top 5 Products by Revenue

SELECT
  products.product_name,
  SUM(order_items.subtotal) AS total_revenue
FROM order_items
JOIN products ON order_items.product_id = products.product_id
JOIN orders ON order_items.order_id = orders.order_id
WHERE orders.status = 'COMPLETE'
GROUP BY products.product_name
ORDER BY total_revenue DESC
LIMIT 5;

 3. Sales by Product Category

SELECT
  categories.category_name,
  SUM(order_items.subtotal) AS category_sales
FROM order_items
JOIN products ON order_items.product_id = products.product_id
JOIN categories ON products.category_id = categories.category_id
JOIN orders ON order_items.order_id = orders.order_id
WHERE orders.status = 'COMPLETE'
GROUP BY categories.category_name
ORDER BY category_sales DESC;

 4. Daily Order Count
SELECT
  order_date,
  COUNT(order_id) AS total_orders
FROM orders
WHERE status = 'COMPLETE'
GROUP BY order_date
ORDER BY order_date;

5. Average Order Value (AOV) by Month

SELECT
  DATE_FORMAT(order_date, '%Y-%m') AS month,
  ROUND(SUM(order_items.subtotal)/COUNT(DISTINCT orders.order_id), 2) AS avg_order_value
FROM orders
JOIN order_items ON orders.order_id = order_items.order_id
WHERE orders.status = 'COMPLETE'
GROUP BY month
ORDER BY month;

