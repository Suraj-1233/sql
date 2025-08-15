
# SQL Commands Cheat Sheet with Explanations

This cheat sheet contains commonly used SQL commands along with explanations.

## 1. Database Operations

### CREATE DATABASE
```sql
CREATE DATABASE my_database;
```
Creates a new database named `my_database`.

### DROP DATABASE
```sql
DROP DATABASE my_database;
```
Deletes the database and all its contents permanently.

### USE DATABASE
```sql
USE my_database;
```
Selects a database to run queries against (MySQL).

---

## 2. Table Operations

### CREATE TABLE
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    salary DECIMAL(10, 2),
    hire_date DATE
);
```
Creates a new table named `employees` with specific columns and data types.

### DROP TABLE
```sql
DROP TABLE employees;
```
Deletes the table and its data permanently.

### ALTER TABLE
```sql
ALTER TABLE employees ADD COLUMN department VARCHAR(50);
```
Adds a new column named `department` to the `employees` table.

### RENAME TABLE
```sql
ALTER TABLE employees RENAME TO staff;
```
Renames the `employees` table to `staff`.

---

## 3. Data Operations

### INSERT
```sql
INSERT INTO employees (id, name, salary, hire_date)
VALUES (1, 'John Doe', 50000.00, '2025-01-10');
```
Adds a new row to the table.

### UPDATE
```sql
UPDATE employees
SET salary = 55000.00
WHERE id = 1;
```
Updates existing rows in the table based on a condition.

### DELETE
```sql
DELETE FROM employees WHERE id = 1;
```
Deletes rows from the table based on a condition.

---

## 4. Querying Data

### SELECT
```sql
SELECT * FROM employees;
```
Retrieves all columns from the `employees` table.

### WHERE
```sql
SELECT * FROM employees WHERE salary > 50000;
```
Filters rows based on conditions.

### ORDER BY
```sql
SELECT * FROM employees ORDER BY salary DESC;
```
Sorts results by salary in descending order.

### GROUP BY
```sql
SELECT department, COUNT(*) FROM employees GROUP BY department;
```
Groups rows by department and counts employees in each.

### HAVING
```sql
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 60000;
```
Filters grouped results based on aggregate conditions.

---

## 5. Joins

### INNER JOIN
```sql
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d
ON e.department_id = d.id;
```
Retrieves only matching rows from both tables.

### LEFT JOIN
```sql
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.id;
```
Retrieves all employees with matching department info, including employees with no department.

### RIGHT JOIN
```sql
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d
ON e.department_id = d.id;
```
Retrieves all departments, including those without employees.

### FULL OUTER JOIN
```sql
SELECT e.name, d.department_name
FROM employees e
FULL OUTER JOIN departments d
ON e.department_id = d.id;
```
Retrieves all rows from both tables, matching where possible.

---

## 6. Indexes

### CREATE INDEX
```sql
CREATE INDEX idx_salary ON employees(salary);
```
Speeds up lookups on the `salary` column.

### DROP INDEX
```sql
DROP INDEX idx_salary;
```
Removes an index.

---

## 7. Views

### CREATE VIEW
```sql
CREATE VIEW high_paid_employees AS
SELECT * FROM employees WHERE salary > 70000;
```
Creates a virtual table (`view`) of high-paid employees.

### DROP VIEW
```sql
DROP VIEW high_paid_employees;
```
Deletes a view.

---

## 8. Transactions

### START TRANSACTION / COMMIT / ROLLBACK
```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```
Groups multiple statements as one unit of work.  
Use `ROLLBACK;` to undo changes before committing.

---

## 9. Useful Functions

### Aggregate Functions
- `COUNT(column)` – Counts non-null values.
- `SUM(column)` – Adds values.
- `AVG(column)` – Calculates average.
- `MIN(column)` – Finds smallest value.
- `MAX(column)` – Finds largest value.

### Date Functions
- `CURRENT_DATE` – Today's date.
- `NOW()` – Current timestamp.
- `DATE_ADD(date, INTERVAL X DAY)` – Adds days to date.
- `DATE_SUB(date, INTERVAL X DAY)` – Subtracts days.

---

## 10. Advanced Topics

### Common Table Expressions (CTEs)
```sql
WITH recent_orders AS (
    SELECT * FROM orders WHERE order_date >= CURRENT_DATE - INTERVAL '30 days'
)
SELECT * FROM recent_orders WHERE total_amount > 5000;
```
Temporary result set for use in a query.

### Window Functions
```sql
SELECT name, salary, RANK() OVER (ORDER BY salary DESC) as rank
FROM employees;
```
Adds ranking without grouping.

---
