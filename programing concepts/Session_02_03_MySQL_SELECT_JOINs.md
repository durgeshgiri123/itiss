# Sessions 2 & 3 – MySQL: SELECT, WHERE, ORDER BY & JOINs
> **Syllabus:** 4T + 6L + 2SL | Module A: MySQL

---

## 1. SELECT Statement

```sql
SELECT * FROM employees;                          -- all columns
SELECT name, salary FROM employees;               -- specific columns
SELECT name AS "Employee Name" FROM employees;    -- alias
SELECT DISTINCT department FROM employees;        -- unique values only
```

> ⚠️ `SELECT *` is fine for practice but **avoided in production** — it fetches unnecessary data and breaks if columns change order.

---

## 2. WHERE Clause – Filtering Data

```sql
SELECT * FROM employees WHERE salary > 50000;
SELECT * FROM employees WHERE department = 'IT';
SELECT * FROM employees WHERE name LIKE 'A%';       -- starts with A
SELECT * FROM employees WHERE name LIKE '%kumar';   -- ends with kumar
SELECT * FROM employees WHERE name LIKE '_a%';      -- 2nd char is 'a'
SELECT * FROM employees WHERE salary BETWEEN 30000 AND 60000;
SELECT * FROM employees WHERE department IN ('IT', 'HR', 'Finance');
SELECT * FROM employees WHERE email IS NULL;
SELECT * FROM employees WHERE email IS NOT NULL;
```

### Logical Operators

```sql
WHERE salary > 40000 AND department = 'IT'
WHERE department = 'HR' OR department = 'Finance'
WHERE NOT department = 'Admin'
```

---

## 3. ORDER BY – Sorting

```sql
SELECT * FROM employees ORDER BY salary ASC;         -- ascending (default)
SELECT * FROM employees ORDER BY salary DESC;        -- descending
SELECT * FROM employees ORDER BY department ASC, salary DESC;  -- multi-column sort
SELECT * FROM employees ORDER BY 2;                  -- sort by 2nd column (positional)
```

> ⚠️ Without `ORDER BY`, MySQL does **NOT guarantee** any row order.

---

## 4. LIMIT & OFFSET (Pagination)

```sql
SELECT * FROM employees LIMIT 10;             -- first 10 rows
SELECT * FROM employees LIMIT 10 OFFSET 20;  -- rows 21–30 (page 3 of 10)
SELECT * FROM employees ORDER BY salary DESC LIMIT 1;  -- highest paid
```

---

## 5. JOINs – Combining Tables

### Types of JOINs

```
Table A: employees          Table B: departments
emp_id | dept_id            dept_id | dept_name
1      | 10                 10      | IT
2      | 20                 20      | HR
3      | 99  (no match)     30      | Finance (no match)
```

#### INNER JOIN — only matching rows
```sql
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.dept_id;
-- Returns: emp 1 (IT), emp 2 (HR) — emp 3 excluded, Finance excluded
```

#### LEFT JOIN — all left table rows + matching right
```sql
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
-- Returns: emp 1, emp 2, emp 3 (dept_name = NULL for emp 3)
```

#### RIGHT JOIN — all right table rows + matching left
```sql
SELECT e.name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
-- Returns: emp 1, emp 2, Finance (name = NULL)
```

#### FULL OUTER JOIN (MySQL doesn't support directly — use UNION)
```sql
SELECT e.name, d.dept_name FROM employees e
LEFT  JOIN departments d ON e.dept_id = d.dept_id
UNION
SELECT e.name, d.dept_name FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.dept_id;
```

#### CROSS JOIN — every combination (Cartesian product)
```sql
SELECT e.name, d.dept_name FROM employees e CROSS JOIN departments d;
-- 3 employees × 3 departments = 9 rows
```

#### SELF JOIN — table joins itself
```sql
SELECT a.name AS Employee, b.name AS Manager
FROM employees a
JOIN employees b ON a.manager_id = b.emp_id;
```

### JOIN Summary Table

| JOIN Type | Returns |
|-----------|---------|
| INNER JOIN | Only matching rows in both tables |
| LEFT JOIN | All left rows + matching right (NULL if no match) |
| RIGHT JOIN | All right rows + matching left (NULL if no match) |
| FULL OUTER | All rows from both (NULL where no match) |
| CROSS JOIN | Cartesian product (m × n rows) |
| SELF JOIN | Table joined to itself |

---

## 6. Self-Learning – Database Security

### SQL Injection

SQL Injection is when an attacker **inserts malicious SQL** into an input field to manipulate the database.

```sql
-- Vulnerable query
SELECT * FROM users WHERE username = '$input';

-- Attacker enters: ' OR '1'='1
-- Resulting query:
SELECT * FROM users WHERE username = '' OR '1'='1';
-- Returns ALL users — authentication bypassed!
```

**Prevention:**
- Use **Prepared Statements / Parameterized Queries**
- Input validation and sanitization
- Principle of Least Privilege

### Users, Roles & Privileges

```sql
-- Grant privileges
GRANT SELECT ON company_db.* TO 'readonly_user'@'localhost';
GRANT ALL PRIVILEGES ON company_db.* TO 'admin'@'localhost';
GRANT SELECT, INSERT, UPDATE ON company_db.employees TO 'app_user'@'localhost';

-- Revoke privileges
REVOKE INSERT ON company_db.employees FROM 'app_user'@'localhost';

-- See grants for a user
SHOW GRANTS FOR 'readonly_user'@'localhost';

-- Apply changes immediately
FLUSH PRIVILEGES;
```

### Three Key Users (Best Practice)

| User | Privileges | Purpose |
|------|-----------|---------|
| `admin` | ALL PRIVILEGES | DBA tasks |
| `app_user` | SELECT, INSERT, UPDATE | Application backend |
| `readonly_user` | SELECT only | Reports, analytics |

**Principle of Least Privilege:** Give users ONLY the permissions they need and nothing more.

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between WHERE and HAVING?**
`WHERE` filters rows **before** grouping. `HAVING` filters **after** grouping (used with GROUP BY).

**Q2. What does LIKE '%' match?**
Everything — zero or more characters. `LIKE '_'` matches exactly one character.

**Q3. What is the output of INNER JOIN vs LEFT JOIN when there's no match?**
INNER JOIN excludes unmatched rows. LEFT JOIN keeps all left rows and fills right columns with NULL.

**Q4. Can you JOIN on multiple conditions?**
Yes: `JOIN table2 ON t1.col1 = t2.col1 AND t1.col2 = t2.col2`

**Q5. What is a SELF JOIN used for?**
Hierarchical data: employees with managers, folder structures, org charts.

**Q6. What is the difference between UNION and UNION ALL?**
`UNION` removes duplicate rows. `UNION ALL` keeps all rows including duplicates (faster).

**Q7. How does SQL Injection work? How to prevent it?**
Attacker injects SQL via user input. Prevention: prepared statements, input validation, least privilege.

**Q8. What does FLUSH PRIVILEGES do?**
Reloads the grant tables so privilege changes take effect immediately.

**Q9. What is a Cartesian product in JOINs?**
CROSS JOIN — every row from table A is combined with every row from table B. If A has 5 rows and B has 4, result has 20 rows.

**Q10. What happens when you ORDER BY a NULL value?**
By default, NULLs sort first in ASC order and last in DESC order in MySQL.

**Q11. What is the difference between IN and BETWEEN?**
`IN` checks membership in a discrete set. `BETWEEN` checks inclusive range (includes both endpoints).

**Q12. Which JOIN is used to simulate FULL OUTER JOIN in MySQL?**
`LEFT JOIN UNION RIGHT JOIN`

---

## 📌 Quick Revision Checklist
- [ ] SELECT with WHERE, LIKE, IN, BETWEEN, IS NULL
- [ ] AND / OR / NOT operators
- [ ] ORDER BY ASC/DESC, multi-column sort
- [ ] LIMIT and OFFSET for pagination
- [ ] INNER / LEFT / RIGHT / CROSS / SELF JOIN
- [ ] FULL OUTER JOIN workaround with UNION
- [ ] SQL Injection concept and prevention
- [ ] GRANT / REVOKE / SHOW GRANTS
- [ ] FLUSH PRIVILEGES
- [ ] Principle of Least Privilege
