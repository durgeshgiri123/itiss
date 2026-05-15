# Session 1 – MySQL Basics: Databases, Tables & Schemas
> **Syllabus:** 2T + 2L + 2SL | Module A: MySQL

---

## 1. Core Concepts

### What is a Database?
A **database** is an organized collection of structured data stored electronically. A **DBMS (Database Management System)** manages it. MySQL is a **Relational DBMS (RDBMS)** — data is stored in related tables.

| Term | Meaning |
|------|---------|
| **Database** | Container holding all tables and objects |
| **Table** | 2D structure with rows (records) and columns (fields) |
| **Schema** | Blueprint/structure of a database (in MySQL, schema = database) |
| **Record/Row** | A single data entry |
| **Field/Column** | A single attribute/property |

---

## 2. Creating & Dropping Databases

```sql
-- Create
CREATE DATABASE company_db;
CREATE DATABASE IF NOT EXISTS company_db;   -- no error if exists

-- Use / switch to a database
USE company_db;

-- List all databases
SHOW DATABASES;

-- Drop (PERMANENT — no undo!)
DROP DATABASE company_db;
DROP DATABASE IF EXISTS company_db;         -- no error if missing
```

> ⚠️ **`DROP DATABASE` deletes ALL tables and data permanently. No UNDO by default.**

---

## 3. Creating Tables

```sql
CREATE TABLE employees (
    emp_id    INT           NOT NULL AUTO_INCREMENT,
    name      VARCHAR(100)  NOT NULL,
    email     VARCHAR(150)  UNIQUE,
    salary    DECIMAL(10,2) DEFAULT 0.00,
    joined_on DATE,
    PRIMARY KEY (emp_id)
);
```

### Common Data Types

| Type | Use Case | Tricky Note |
|------|----------|-------------|
| `INT` | Whole numbers | Range ±2.1 billion |
| `VARCHAR(n)` | Variable-length text | Stores only used bytes + 1-2 length bytes |
| `CHAR(n)` | Fixed-length text | Always pads to n bytes; faster for fixed data |
| `TEXT` | Long strings | Cannot have DEFAULT value |
| `DECIMAL(p,s)` | Exact decimals (money) | p=total digits, s=decimal places |
| `FLOAT/DOUBLE` | Approximate decimals | NOT safe for currency! |
| `DATE` | `YYYY-MM-DD` | |
| `DATETIME` | `YYYY-MM-DD HH:MM:SS` | |
| `BOOLEAN` | true/false | Stored as `TINYINT(1)` internally |

---

## 4. Altering & Deleting Tables

```sql
ALTER TABLE employees ADD COLUMN department VARCHAR(50);
ALTER TABLE employees MODIFY COLUMN salary FLOAT;
ALTER TABLE employees RENAME COLUMN email TO work_email;
ALTER TABLE employees DROP COLUMN department;

RENAME TABLE employees TO staff;

TRUNCATE TABLE employees;    -- delete all rows, keep structure
DROP TABLE employees;        -- delete table entirely
```

### TRUNCATE vs DELETE vs DROP

| Command | Removes Rows | Removes Table | Resets AUTO_INCREMENT | Rollback? |
|---------|:-----------:|:------------:|:---------------------:|:---------:|
| `DELETE` | ✅ selective | ❌ | ❌ | ✅ in transaction |
| `TRUNCATE` | ✅ all | ❌ | ✅ | ❌ |
| `DROP` | ✅ all | ✅ | ✅ | ❌ |

---

## 5. MySQL Users

```sql
CREATE USER 'john'@'localhost' IDENTIFIED BY 'Pass@123';
CREATE USER 'john'@'%' IDENTIFIED BY 'Pass@123';  -- from any host

SELECT user, host FROM mysql.user;   -- list users

DROP USER 'john'@'localhost';
```

---

## 6. Self-Learning – ER Diagrams

### Key Terms

| Term | Definition |
|------|-----------|
| **Entity** | Real-world object (Student, Course) |
| **Attribute** | Property of entity (name, age) |
| **Primary Key** | Uniquely identifies each row |
| **Foreign Key** | References PK of another table |
| **Relationship** | 1:1, 1:N, M:N association |

### ER → MySQL Example

```sql
CREATE TABLE student (
    student_id INT PRIMARY KEY AUTO_INCREMENT,
    name       VARCHAR(100),
    age        INT
);

CREATE TABLE course (
    course_id INT PRIMARY KEY AUTO_INCREMENT,
    title     VARCHAR(200)
);

-- M:N resolved via junction table
CREATE TABLE enrolls (
    student_id  INT,
    course_id   INT,
    enroll_date DATE,
    PRIMARY KEY (student_id, course_id),   -- composite PK
    FOREIGN KEY (student_id) REFERENCES student(student_id) ON DELETE CASCADE,
    FOREIGN KEY (course_id)  REFERENCES course(course_id)   ON DELETE CASCADE
);
```

---

## 🎯 Tricky Exam Questions & Answers

**Q1. What is the difference between CHAR(10) and VARCHAR(10)?**
`CHAR(10)` always uses 10 bytes (pads with spaces). `VARCHAR(10)` uses only needed bytes + 1-2 length bytes. `CHAR` is faster for fixed-length data; `VARCHAR` saves storage.

**Q2. Can a table have more than one PRIMARY KEY?**
No. Only ONE primary key per table, but it can be a **composite key** (multiple columns combined).

**Q3. Difference between DROP TABLE and TRUNCATE TABLE?**
`DROP` removes structure + data. `TRUNCATE` removes only data, keeps structure, resets AUTO_INCREMENT, and cannot be rolled back.

**Q4. When does AUTO_INCREMENT reset?**
On `TRUNCATE`. Can also be manually set: `ALTER TABLE t AUTO_INCREMENT = 1;`

**Q5. Can a FOREIGN KEY reference a non-PRIMARY KEY column?**
Yes — any column with a `UNIQUE` constraint can be referenced by a FK.

**Q6. Are schema and database the same in MySQL?**
Yes — in MySQL, `CREATE SCHEMA` = `CREATE DATABASE`. They are synonymous. (Not true in PostgreSQL where schema is a namespace inside a database.)

**Q7. What does ON DELETE CASCADE mean?**
If a parent row is deleted, all related child rows are automatically deleted too.

**Q8. What is referential integrity?**
Ensures a FK value in the child table must exist in the parent table. MySQL enforces this via FOREIGN KEY constraints.

**Q9. What happens when you INSERT without specifying AUTO_INCREMENT column?**
MySQL auto-assigns the next available integer.

**Q10. Can NULL be a PRIMARY KEY value?**
No. PRIMARY KEY implies NOT NULL + UNIQUE.

---

## 📌 Quick Revision Checklist
- [ ] `CREATE DATABASE IF NOT EXISTS` / `DROP DATABASE IF EXISTS`
- [ ] `USE db_name` before table operations
- [ ] CHAR vs VARCHAR vs TEXT
- [ ] DECIMAL vs FLOAT (money → always DECIMAL)
- [ ] PRIMARY KEY vs FOREIGN KEY
- [ ] Composite Primary Key syntax
- [ ] DROP vs TRUNCATE vs DELETE comparison table
- [ ] ON DELETE CASCADE vs ON DELETE RESTRICT
- [ ] ER Diagram → SQL conversion (M:N needs junction table)
