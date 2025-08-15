
# Top 1% SQL Developer Handbook (Extended Version)

This file expands the basic SQL cheat sheet into a **professional-grade reference** for advanced SQL developers.

---

## 1. Performance Tuning Principles

### a) Filter Early
Always reduce rows **as soon as possible** using `WHERE` or indexed columns.
```sql
-- Bad: filtering after aggregation
SELECT status, COUNT(*)
FROM orders
GROUP BY status
HAVING status = 'PAID';

-- Good: filter before aggregation
SELECT status, COUNT(*)
FROM orders
WHERE status = 'PAID'
GROUP BY status;
```

### b) Avoid Functions on Indexed Columns
```sql
-- Bad: prevents index usage
WHERE DATE(order_date) >= '2025-05-01'

-- Good: keeps index usable
WHERE order_date >= '2025-05-01'
```

### c) Limit Data with SELECT
```sql
-- Bad: pulls unnecessary columns
SELECT * FROM orders

-- Good: select only what you need
SELECT order_id, status FROM orders
```

---

## 2. Indexing Strategies

### a) Single vs Composite Indexes
- **Single-column index**: Good for filtering on one column.
- **Composite index**: Good when filtering/sorting/grouping on multiple columns **in the same query**.

```sql
CREATE INDEX idx_orders_date_status ON orders(order_date, status);
```
> Rule: Index columns in order of **selectivity** (most filtering power first).

### b) Covering Index
An index that **contains all needed columns** for a query, avoiding table lookups.
```sql
CREATE INDEX idx_cover ON orders(order_date, status) INCLUDE (total_amount);
```

### c) Partial Index
Stores only filtered rows.
```sql
CREATE INDEX idx_high_value_orders
ON orders(order_date)
WHERE total_amount > 5000;
```

---

## 3. Reading Execution Plans

### Steps to Read:
1. **Identify access method**: Seq Scan (full table) vs Index Scan (fast lookup).
2. **Check filter pushdown**: Are WHERE clauses applied early?
3. **Look for expensive operations**: Nested Loop on large datasets, Hash Join memory usage.
4. **Review estimated vs actual rows**: Large mismatches → update statistics.

Example (PostgreSQL):
```
Seq Scan on orders  (cost=0.00..4450.00 rows=100 width=12)
  Filter: (total_amount > 5000)
```
> If Seq Scan is slow → create appropriate index.

---

## 4. Real-World Query Patterns

### a) Gaps and Islands
Find continuous sequences.
```sql
WITH numbered AS (
    SELECT user_id, activity_date,
           activity_date - INTERVAL '1 day' * ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY activity_date) AS grp
    FROM user_activity
)
SELECT user_id, MIN(activity_date) AS start_date, MAX(activity_date) AS end_date
FROM numbered
GROUP BY user_id, grp;
```

### b) Recursive Hierarchy
```sql
WITH RECURSIVE employee_tree AS (
    SELECT emp_id, emp_name, manager_id
    FROM employees
    WHERE emp_id = 10
    UNION ALL
    SELECT e.emp_id, e.emp_name, e.manager_id
    FROM employees e
    INNER JOIN employee_tree et ON e.manager_id = et.emp_id
)
SELECT * FROM employee_tree;
```

### c) Rolling Averages
```sql
SELECT stock_id, price_date,
       AVG(price) OVER (PARTITION BY stock_id ORDER BY price_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS avg_7d
FROM stock_prices;
```

---

## 5. Concurrency & Locking

### Isolation Levels
- **READ COMMITTED** – Default, avoids dirty reads.
- **REPEATABLE READ** – Prevents non-repeatable reads.
- **SERIALIZABLE** – Full consistency, slowest.

### Prevent Deadlocks
- Always lock tables in the same order in all transactions.

### Idempotency for APIs
```sql
INSERT INTO payments (payment_id, user_id, amount)
VALUES ('TX123', 42, 500)
ON CONFLICT (payment_id) DO NOTHING; -- PostgreSQL
```

---

## 6. Big Data Handling

### a) Partitioning
```sql
CREATE TABLE orders_2025_01 PARTITION OF orders
FOR VALUES FROM ('2025-01-01') TO ('2025-02-01');
```
> Reduces scan size for time-based queries.

### b) Materialized Views
```sql
CREATE MATERIALIZED VIEW monthly_sales AS
SELECT DATE_TRUNC('month', order_date) AS month, SUM(total_amount) AS revenue
FROM orders
GROUP BY month;
```
Refresh periodically:
```sql
REFRESH MATERIALIZED VIEW monthly_sales;
```

---

## 7. Checklist Before Query Optimization
- ✅ Are indexes covering the query?
- ✅ Is filtering done before grouping?
- ✅ Are you avoiding functions on indexed columns?
- ✅ Are statistics up to date?
- ✅ Have you checked the execution plan?

---
