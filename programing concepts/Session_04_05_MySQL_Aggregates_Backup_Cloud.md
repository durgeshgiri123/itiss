# Sessions 4 & 5 – Aggregates, Subqueries, Backup & Cloud MySQL
> **Syllabus:** 4T + 4L + 4SL | Module A: MySQL

---

## 1. Aggregate Functions

Aggregate functions operate on a set of rows and return a single value.

```sql
SELECT COUNT(*)           FROM employees;           -- total rows
SELECT COUNT(email)       FROM employees;           -- non-NULL emails only
SELECT SUM(salary)        FROM employees;
SELECT AVG(salary)        FROM employees;
SELECT MAX(salary)        FROM employees;
SELECT MIN(salary)        FROM employees;
SELECT SUM(salary), AVG(salary), MAX(salary) FROM employees WHERE department = 'IT';
```

> ⚠️ `COUNT(*)` counts ALL rows including NULLs. `COUNT(column)` skips NULL values.

---

## 2. GROUP BY

Groups rows that have the same values in specified columns; used with aggregate functions.

```sql
-- Count employees per department
SELECT department, COUNT(*) AS total
FROM employees
GROUP BY department;

-- Average salary per department
SELECT department, AVG(salary) AS avg_sal
FROM employees
GROUP BY department
ORDER BY avg_sal DESC;
```

> ⚠️ Every column in SELECT that is **not an aggregate** must appear in GROUP BY — otherwise MySQL may return unpredictable results (depends on `sql_mode`).

---

## 3. HAVING Clause

Filters **after** grouping (WHERE filters before grouping).

```sql
-- Departments with more than 5 employees
SELECT department, COUNT(*) AS total
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;

-- Departments where avg salary > 50000
SELECT department, AVG(salary) AS avg_sal
FROM employees
GROUP BY department
HAVING avg_sal > 50000;         -- can use alias in HAVING in MySQL
```

### WHERE vs HAVING

| | WHERE | HAVING |
|--|-------|--------|
| Filters | Individual rows | Grouped results |
| Used with aggregates? | ❌ | ✅ |
| Executes | Before GROUP BY | After GROUP BY |

---

## 4. Subqueries (Nested Queries)

A query inside another query.

```sql
-- Employees earning more than company average
SELECT name, salary FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Employees in the IT department (using subquery instead of JOIN)
SELECT name FROM employees
WHERE dept_id = (SELECT dept_id FROM departments WHERE dept_name = 'IT');

-- Employees with the highest salary in each department
SELECT name, department, salary FROM employees
WHERE salary = (
    SELECT MAX(salary) FROM employees e2
    WHERE e2.department = employees.department
);
```

### Correlated vs Non-Correlated Subquery

| Type | Description | Performance |
|------|------------|-------------|
| **Non-correlated** | Independent inner query; runs once | Faster |
| **Correlated** | Inner query references outer query; runs once per row | Slower |

```sql
-- Correlated subquery example
SELECT name FROM employees e1
WHERE salary > (SELECT AVG(salary) FROM employees e2 WHERE e2.department = e1.department);
```

---

## 5. Self-Learning – Backup & Restore

### Logical vs Physical Backup

| | Logical Backup | Physical Backup |
|--|---------------|----------------|
| Tool | `mysqldump` | File system copy |
| Output | SQL file | Binary data files |
| Portability | High (cross-version) | Low |
| Speed | Slower | Faster |
| Use Case | Small/medium DBs | Large production DBs |

### Backup Commands

```bash
# Backup single database
mysqldump -u root -p company_db > backup.sql

# Backup all databases
mysqldump -u root -p --all-databases > all_backup.sql

# Backup specific table
mysqldump -u root -p company_db employees > emp_backup.sql
```

### Restore Commands

```bash
# Restore database
mysql -u root -p company_db < backup.sql

# Create DB first if needed
mysql -u root -p -e "CREATE DATABASE company_db;"
mysql -u root -p company_db < backup.sql
```

### Recovery Scenario
Production database crashes at midnight. Steps:
1. Identify last good backup timestamp
2. Restore from latest `mysqldump` backup
3. Apply binary logs (binlog) to recover transactions after backup
4. Verify data integrity
5. Restart application

---

## 6. Self-Learning – Cloud Databases

### On-Premises vs Cloud MySQL

| | On-Premises | Cloud (Amazon RDS) |
|--|-------------|-------------------|
| Setup | Manual | Automated |
| Scaling | Manual hardware | Click to scale |
| Backups | Manual | Automated |
| High Availability | Complex setup | Built-in Multi-AZ |
| Cost | CapEx (upfront) | OpEx (pay-as-you-go) |
| Maintenance | DBA manages | AWS managed |

### Scaling Types

| Type | How | Best For |
|------|-----|---------|
| **Vertical Scaling** | Bigger machine (more CPU/RAM) | Simple; single server |
| **Horizontal Scaling** | More machines (read replicas) | High read traffic |

### Indexing & Performance

```sql
-- Create index
CREATE INDEX idx_salary ON employees(salary);

-- Create composite index
CREATE INDEX idx_dept_sal ON employees(department, salary);

-- Show indexes on a table
SHOW INDEX FROM employees;

-- Drop index
DROP INDEX idx_salary ON employees;
```

**How indexes work:** Like a book's index — MySQL uses a B-Tree to quickly locate rows without scanning every row (full table scan).

> ⚠️ Indexes speed up **reads** but slow down **writes** (INSERT/UPDATE/DELETE) because the index must be updated too.

### Slow Query Log

```sql
-- Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 2;   -- log queries taking > 2 seconds

-- Find slow queries
EXPLAIN SELECT * FROM employees WHERE salary > 50000;
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between COUNT(*) and COUNT(column)?**
`COUNT(*)` counts all rows including NULLs. `COUNT(column)` counts only non-NULL values in that column.

**Q2. Can you use WHERE with aggregate functions?**
No — use `HAVING` for filtering aggregated results.

**Q3. What is a correlated subquery? Why is it slow?**
A subquery that references columns from the outer query. It re-executes for each row of the outer query, making it O(n) times slower.

**Q4. What is the difference between logical and physical backup?**
Logical backup (mysqldump) exports SQL statements — portable but slower. Physical backup copies raw data files — faster but less portable.

**Q5. What is a full table scan and when does it happen?**
MySQL reads every row in the table when there's no usable index. Happens with no index, LIKE '%value' (leading wildcard), or queries on non-indexed columns.

**Q6. What is the difference between vertical and horizontal scaling?**
Vertical = bigger server. Horizontal = more servers (read replicas, sharding).

**Q7. What does EXPLAIN do in MySQL?**
Shows the query execution plan — whether indexes are used, estimated rows scanned, join types. Crucial for optimizing slow queries.

**Q8. Can a HAVING clause work without GROUP BY?**
Yes — it treats the entire result set as one group.

**Q9. What is a composite index? When should you use it?**
An index on multiple columns. Use when queries frequently filter on multiple columns together. Order matters: `(a, b)` index helps `WHERE a = ?` and `WHERE a = ? AND b = ?` but NOT `WHERE b = ?` alone.

**Q10. What is binary log (binlog) used for?**
Records every change to the database. Used for point-in-time recovery and replication.

**Q11. What will the following return? `SELECT department, COUNT(*) FROM employees;` without GROUP BY**
In strict SQL mode it's an error. In MySQL's default mode, it returns ONE row with a random department value and total count — undefined behavior.

---

## 📌 Quick Revision Checklist
- [ ] COUNT(*) vs COUNT(col) — NULL handling
- [ ] SUM, AVG, MAX, MIN
- [ ] GROUP BY + HAVING combo
- [ ] WHERE vs HAVING comparison table
- [ ] Correlated vs non-correlated subquery
- [ ] mysqldump backup and restore commands
- [ ] Logical vs Physical backup
- [ ] Vertical vs Horizontal scaling
- [ ] Index: creation, types, trade-offs
- [ ] EXPLAIN for query optimization
- [ ] On-Prem vs Amazon RDS comparison
