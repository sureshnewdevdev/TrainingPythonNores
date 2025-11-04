
# MySQL Basics — Interview Questions & Answers (with Examples)

> A practical, beginner-friendly set of questions and answers that you can also use as a crash course.  
> All examples are MySQL 8.x compatible unless noted.

---

## Quick Reference Schema (used in examples)

```sql
CREATE TABLE departments (
  dept_id     INT PRIMARY KEY AUTO_INCREMENT,
  dept_name   VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE employees (
  emp_id      INT PRIMARY KEY AUTO_INCREMENT,
  first_name  VARCHAR(50)  NOT NULL,
  last_name   VARCHAR(50)  NOT NULL,
  email       VARCHAR(120) UNIQUE,
  salary      DECIMAL(10,2),
  hire_date   DATE NOT NULL,
  dept_id     INT,
  CONSTRAINT fk_emp_dept
    FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
    ON DELETE SET NULL ON UPDATE CASCADE
);
```

Seed data:
```sql
INSERT INTO departments (dept_name) VALUES ('HR'),('Finance'),('IT');

INSERT INTO employees (first_name,last_name,email,salary,hire_date,dept_id) VALUES
('Asha','Iyer','asha.iyer@example.com',65000,'2022-01-15',1),
('Rahul','Mehta','rahul.mehta@example.com',80000,'2021-10-03',3),
('Zara','Khan','zara.khan@example.com',72000,'2023-02-01',3),
('Vikram','Patil','vikram.patil@example.com',50000,'2024-06-21',2);
```
---

## 1) What is MySQL? How is it different from SQL?
**Answer:**  
- **SQL** is a language standard (Structured Query Language).  
- **MySQL** is a relational database management system (RDBMS) that **implements** SQL plus MySQL‑specific features.  
- MySQL supports multiple storage engines (e.g., InnoDB), replication, partitioning, etc.

---

## 2) What are common MySQL data types?
**Answer:**  
- **Numeric:** `INT`, `BIGINT`, `DECIMAL(p,s)`, `FLOAT`, `DOUBLE`  
- **String:** `CHAR(n)`, `VARCHAR(n)`, `TEXT`, `ENUM`, `SET`, `BLOB`  
- **Date/Time:** `DATE`, `DATETIME`, `TIMESTAMP`, `TIME`, `YEAR`  
- **JSON:** `JSON` type for structured documents.

**Example:** `salary DECIMAL(10,2)` stores up to 10 digits with 2 after the decimal.

---

## 3) How do you create, read, update, delete (CRUD) rows?
**Answer & Examples:**
```sql
-- Create
INSERT INTO employees (first_name,last_name,email,salary,hire_date,dept_id)
VALUES ('Meena','Roy','meena.roy@example.com',62000,'2023-09-01',1);

-- Read
SELECT emp_id, first_name, last_name FROM employees WHERE dept_id = 1;

-- Update
UPDATE employees SET salary = salary * 1.10 WHERE emp_id = 1;

-- Delete
DELETE FROM employees WHERE emp_id = 4;
```

---

## 4) What is the difference between `WHERE` and `HAVING`?
**Answer:**  
- `WHERE` filters **rows before** grouping/aggregation.  
- `HAVING` filters **groups after** `GROUP BY`.

**Example:**
```sql
-- Average salary per department, but only show groups with avg > 70000
SELECT d.dept_name, AVG(e.salary) AS avg_sal
FROM employees e JOIN departments d ON e.dept_id = d.dept_id
GROUP BY d.dept_name
HAVING AVG(e.salary) > 70000;
```

---

## 5) Explain different JOINs with examples.
**Answer:**  
- **INNER JOIN:** rows with matching keys in both tables.  
- **LEFT JOIN:** all rows from left table + matched rows from right; unmatched right = NULL.  
- **RIGHT JOIN:** vice versa.  
- **CROSS JOIN:** cartesian product.  

**Examples:**
```sql
-- INNER JOIN
SELECT e.first_name, d.dept_name
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id;

-- LEFT JOIN (includes employees with no department)
SELECT e.first_name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;
```

---

## 6) What is the difference between `UNION` and `UNION ALL`?
**Answer:**  
- `UNION` removes duplicates.  
- `UNION ALL` keeps duplicates (faster).

**Example:**
```sql
SELECT dept_name FROM departments
UNION
SELECT 'IT';        -- will remove duplicate 'IT' if already present

SELECT dept_name FROM departments
UNION ALL
SELECT 'IT';        -- will include 'IT' again
```

---

## 7) What are constraints? Name the common ones.
**Answer:**  
- **PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK, DEFAULT**.  
- They ensure data integrity.

**Example:**
```sql
ALTER TABLE employees
ADD CONSTRAINT uq_emp_email UNIQUE (email);

ALTER TABLE employees
MODIFY salary DECIMAL(10,2) NOT NULL DEFAULT 0.00;
```

---

## 8) How do you handle NULLs?
**Answer:**  
- `NULL` represents “unknown/absent” value.  
- Use `IS NULL`/`IS NOT NULL` to test.  
- Aggregates (e.g., `AVG`) ignore NULLs.  
- Use `COALESCE(x, default)` to replace NULL at query time.

**Example:**
```sql
SELECT first_name, COALESCE(salary, 0) AS safe_salary FROM employees;
```

---

## 9) Difference between `DELETE`, `TRUNCATE`, and `DROP`?
**Answer:**  
- `DELETE`: removes selected rows; **DML**; can `WHERE`; can be rolled back (in a transaction).  
- `TRUNCATE`: removes **all rows**; **DDL**; fast; resets AUTO_INCREMENT; cannot filter.  
- `DROP`: deletes the **table object** itself (structure + data).

**Example:**
```sql
DELETE FROM employees WHERE dept_id = 2;
TRUNCATE TABLE employees;
DROP TABLE employees;
```

---

## 10) What is a Primary Key vs Unique Key?
**Answer:**  
- **Primary Key (PK):** uniquely identifies each row; **not NULL**; one per table.  
- **Unique Key:** enforces uniqueness; can allow **one NULL** (InnoDB in MySQL 8 treats multiple NULLs as multiple distinct NULLs for UNIQUE).

---

## 11) What are Foreign Keys? What about ON DELETE/UPDATE actions?
**Answer:**  
- A **Foreign Key (FK)** references a PK/UK in another table.  
- Actions: `RESTRICT`/`NO ACTION` (default), `CASCADE`, `SET NULL`, `SET DEFAULT` (not supported by InnoDB), `RESTRICT`.

**Example:** (from schema) `ON DELETE SET NULL ON UPDATE CASCADE`

---

## 12) What is normalization? Explain 1NF, 2NF, 3NF briefly.
**Answer:**  
- Organizing data to reduce redundancy and improve integrity.  
- **1NF:** atomic columns, no repeating groups.  
- **2NF:** 1NF + no partial dependency on part of a composite key.  
- **3NF:** 2NF + no transitive dependency on non-key attributes.

---

## 13) What is denormalization?
**Answer:**  
- Deliberately introducing redundancy for performance (e.g., precomputed totals, flattened tables). Useful in reporting/analytics workloads.

---

## 14) How to create and use indexes? Pros/cons?
**Answer:**  
- **Pros:** faster reads, efficient lookups/sorts/joins.  
- **Cons:** slower writes (INSERT/UPDATE/DELETE) and uses extra storage.

**Examples:**
```sql
-- Single-column index
CREATE INDEX idx_emp_lastname ON employees(last_name);

-- Composite index (order matters)
CREATE INDEX idx_emp_dept_salary ON employees(dept_id, salary);

-- Use EXPLAIN to see if index is used
EXPLAIN SELECT * FROM employees WHERE dept_id=3 AND salary>70000;
```

---

## 15) What is the default storage engine? Differences between InnoDB and MyISAM?
**Answer:**  
- **Default:** InnoDB (transactions, row-level locking, FK).  
- **MyISAM:** no transactions/FKs; table-level locking; lighter but generally legacy now.

---

## 16) What are ACID properties?
**Answer:**  
- **Atomicity, Consistency, Isolation, Durability** — guarantees for reliable transactions (InnoDB supports transactions).

---

## 17) How do you start and control transactions?
**Answer & Examples:**
```sql
-- Option A: implicit autocommit OFF for a session
SET autocommit = 0;
START TRANSACTION;
UPDATE employees SET salary = salary + 1000 WHERE dept_id = 3;
COMMIT;    -- or ROLLBACK;

-- Option B: keep autocommit ON but explicitly wrap
START TRANSACTION;
DELETE FROM employees WHERE dept_id = 2;
ROLLBACK;
```

---

## 18) What are isolation levels in MySQL?
**Answer:**  
- `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ` (default), `SERIALIZABLE`.  
- Higher isolation = fewer anomalies but more locking.

**Example:**
```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;
-- do work
COMMIT;
```

---

## 19) How does `LIMIT` work? What about pagination?
**Answer & Examples:**
```sql
-- Top N
SELECT * FROM employees ORDER BY salary DESC LIMIT 5;

-- Pagination: page 3 of size 10 (offset = 20)
SELECT * FROM employees ORDER BY emp_id LIMIT 10 OFFSET 20;
```

---

## 20) What’s the order of SQL execution (logical processing order)?
**Answer:**  
`FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`

---

## 21) What are views? Pros/cons?
**Answer:**  
- A **view** is a stored query.  
- **Pros:** abstraction, security, reuse.  
- **Cons:** complex updates, performance depends on underlying tables.

**Example:**
```sql
CREATE OR REPLACE VIEW v_it_employees AS
SELECT e.emp_id, e.first_name, e.last_name, d.dept_name
FROM employees e JOIN departments d ON e.dept_id = d.dept_id
WHERE d.dept_name = 'IT';

SELECT * FROM v_it_employees;
```

---

## 22) What are stored procedures? Show a simple example.
**Answer:**  
- Stored procedures are compiled routines stored in the DB for reuse, encapsulating logic.

**Example:**
```sql
DELIMITER $$
CREATE PROCEDURE GiveRaise(IN p_dept INT, IN p_percent DECIMAL(5,2))
BEGIN
  UPDATE employees
  SET salary = salary * (1 + p_percent/100)
  WHERE dept_id = p_dept;
END $$
DELIMITER ;

CALL GiveRaise(3, 5.0);
```

---

## 23) What are triggers? Common use case?
**Answer:**  
- Triggers run automatically **before/after** INSERT/UPDATE/DELETE for auditing, validation, derived columns.

**Example:**
```sql
CREATE TABLE emp_audit (
  audit_id INT PRIMARY KEY AUTO_INCREMENT,
  emp_id INT, old_salary DECIMAL(10,2), new_salary DECIMAL(10,2),
  changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

DELIMITER $$
CREATE TRIGGER trg_salary_audit
BEFORE UPDATE ON employees
FOR EACH ROW
BEGIN
  IF NEW.salary <> OLD.salary THEN
    INSERT INTO emp_audit(emp_id, old_salary, new_salary)
    VALUES (OLD.emp_id, OLD.salary, NEW.salary);
  END IF;
END $$
DELIMITER ;
```

---

## 24) How do you secure a MySQL instance? Users & privileges?
**Answer:**  
- Create least-privileged users, set strong passwords, use SSL, restrict network access, and audit.

**Example:**
```sql
CREATE USER 'report_user'@'%' IDENTIFIED BY 'StrongP@ss!';
GRANT SELECT ON yourdb.* TO 'report_user'@'%';
FLUSH PRIVILEGES;
```

---

## 25) How do you back up and restore?
**Answer:**  
- Use `mysqldump` or physical backup tools (e.g., MySQL Enterprise Backup, Percona XtraBackup).

**Examples (CLI):**
```bash
# Backup
mysqldump -u root -p yourdb > yourdb_$(date +%F).sql

# Restore
mysql -u root -p yourdb < yourdb_2025-11-04.sql
```

---

## 26) Explain `EXPLAIN` (query plan). What do you look for?
**Answer:**  
- `EXPLAIN` shows how MySQL executes a query (access type, possible/used keys, rows, filtered).  
- Prefer `const`, `eq_ref`, `ref`, `range` over `ALL` (full scan).  
- Ensure appropriate indexes for join/filter/sort columns.

**Example:**
```sql
EXPLAIN SELECT * FROM employees WHERE dept_id = 3 AND salary > 70000;
```

---

## 27) How do you find and handle duplicates?
**Answer & Examples:**
```sql
-- Find duplicate emails
SELECT email, COUNT(*) c
FROM employees
GROUP BY email
HAVING COUNT(*) > 1;

-- Delete duplicates (keep the lowest emp_id)
DELETE e1 FROM employees e1
JOIN employees e2
  ON e1.email = e2.email AND e1.emp_id > e2.emp_id;
```

---

## 28) Common string/date functions?
**Answer & Examples:**
```sql
-- String
SELECT UPPER(first_name), CONCAT(first_name,' ',last_name) AS full_name FROM employees;

-- Date
SELECT YEAR(hire_date) AS hired_year, MONTHNAME(hire_date) AS hired_month FROM employees;

-- Conditional
SELECT emp_id, IF(salary > 70000, 'High', 'Normal') AS band FROM employees;
```

---

## 29) Difference between `CHAR` and `VARCHAR`?
**Answer:**  
- `CHAR(n)`: fixed-length; pads with spaces; faster for uniform values (e.g., country codes).  
- `VARCHAR(n)`: variable-length; space-efficient for varying sizes (e.g., names).

---

## 30) How to change a table structure safely?
**Answer & Examples:**
```sql
-- Add a column
ALTER TABLE employees ADD COLUMN phone VARCHAR(20);

-- Modify datatype
ALTER TABLE employees MODIFY salary DECIMAL(12,2) NOT NULL;

-- Rename column (MySQL 8+)
ALTER TABLE employees RENAME COLUMN phone TO mobile;

-- Drop column
ALTER TABLE employees DROP COLUMN mobile;
```

---

## 31) What is a composite index? When is the column order important?
**Answer:**  
- Index on multiple columns. Order matters because leftmost prefix is used.  
- Example: `(dept_id, salary)` can aid filters like `dept_id = ? AND salary > ?`, also `dept_id = ?`; **not** `salary > ?` alone.

---

## 32) What is a covering index?
**Answer:**  
- When an index contains all columns needed by the query, MySQL can satisfy it **from the index alone** (no table lookup). Improves performance.

---

## 33) How to prevent SQL injection in application code?
**Answer:**  
- Use **prepared statements / parameterized queries**, no string concatenation with untrusted input.

**Example (pseudo):**
```python
cursor.execute("SELECT * FROM employees WHERE email = %s", (user_email,))
```

---

## 34) What is the difference between `COUNT(*)` and `COUNT(col)`?
**Answer:**  
- `COUNT(*)` counts rows including NULLs.  
- `COUNT(col)` counts **non-NULL** values of `col`.

---

## 35) How do you handle case-insensitive searches?
**Answer:**  
- Use a case-insensitive collation (default often `utf8mb4_0900_ai_ci`) or apply `LOWER()` on both sides (less index-friendly).

**Example:**
```sql
SELECT * FROM employees WHERE email = 'ASHA.IYER@EXAMPLE.COM';
-- with case-insensitive collation, this matches 'asha.iyer@example.com'
```

---

## 36) What is `INFORMATION_SCHEMA` and `performance_schema`?
**Answer:**  
- **INFORMATION_SCHEMA:** metadata about tables, columns, constraints, etc.  
- **performance_schema:** low-level instrumentation for performance metrics/events.

**Example:**
```sql
SELECT table_name, table_rows
FROM information_schema.tables
WHERE table_schema = 'yourdb';
```

---

## 37) How do you write a correlated subquery?
**Answer & Example:**
```sql
-- Employees whose salary is above the department average
SELECT e.*
FROM employees e
WHERE salary > (
  SELECT AVG(salary) FROM employees WHERE dept_id = e.dept_id
);
```

---

## 38) What is `DISTINCT`? When to use it?
**Answer:**  
- Removes duplicates from result set. Use only when needed (can be expensive).

**Example:**
```sql
SELECT DISTINCT dept_id FROM employees;
```

---

## 39) Explain `GROUP BY` with multiple columns.
**Answer & Example:**
```sql
SELECT dept_id, YEAR(hire_date) AS yr, COUNT(*) AS hires
FROM employees
GROUP BY dept_id, YEAR(hire_date)
ORDER BY dept_id, yr;
```

---

## 40) How to export/import CSV with MySQL?
**Answer & Examples:**
```sql
-- Export (from client/CLI: use \T in MySQL Shell or OUTFILE on server with secure-file-priv)
SELECT emp_id, first_name, last_name, salary
FROM employees
INTO OUTFILE '/var/lib/mysql-files/employees.csv'
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n';

-- Import
LOAD DATA INFILE '/var/lib/mysql-files/employees.csv'
INTO TABLE employees
FIELDS TERMINATED BY ',' ENCLOSED BY '"'
LINES TERMINATED BY '\n'
(emp_id, first_name, last_name, salary);
```

> Note: `secure-file-priv` must allow the directory; you need FILE privileges.

---

## Bonus: Common Interview Mini-Tasks

### A) Top 2 earners in each department
```sql
SELECT *
FROM (
  SELECT e.*, 
         ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
  FROM employees e
) t
WHERE rn <= 2;
```

### B) Find departments with no employees
```sql
SELECT d.dept_name
FROM departments d
LEFT JOIN employees e ON e.dept_id = d.dept_id
WHERE e.dept_id IS NULL;
```

### C) Salary band bucketing
```sql
SELECT 
  CASE 
    WHEN salary < 60000 THEN 'Low'
    WHEN salary BETWEEN 60000 AND 80000 THEN 'Mid'
    ELSE 'High'
  END AS band,
  COUNT(*) AS cnt
FROM employees
GROUP BY band;
```

---

## Final Tips
- Always **EXPLAIN** slow queries, create **targeted indexes**, and avoid `SELECT *` in production.  
- Wrap related changes in **transactions**.  
- Prefer **InnoDB**, keep **consistent datatypes** for join columns, and monitor with performance schema.

---

**Good luck!** Practice these queries on a local MySQL instance or Docker image for hands-on confidence.
