
# MySQL INNER / OUTER / CROSS / SELF JOIN — Complex Samples (with Answers & Explanations)

A hands-on, interview-ready set of **complex JOIN problems** using MySQL 8.x.  
Includes **schema**, **seed data**, **questions**, **answers (SQL)**, **expected result samples**, and **explanations + pitfalls**.

---

## 0) Schema Used (create once)

```sql
-- Regions
CREATE TABLE regions (
  region_id   INT PRIMARY KEY AUTO_INCREMENT,
  region_name VARCHAR(50) NOT NULL UNIQUE
);

-- Customers (Indian names as requested)
CREATE TABLE customers (
  customer_id INT PRIMARY KEY AUTO_INCREMENT,
  customer_name VARCHAR(100) NOT NULL,
  region_id INT,
  email VARCHAR(150) UNIQUE,
  created_at DATE NOT NULL,
  CONSTRAINT fk_cust_region
    FOREIGN KEY (region_id) REFERENCES regions(region_id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- Employees (self-referencing manager)
CREATE TABLE employees (
  emp_id INT PRIMARY KEY AUTO_INCREMENT,
  emp_name VARCHAR(100) NOT NULL,
  manager_id INT NULL,
  dept VARCHAR(50),
  salary DECIMAL(10,2),
  CONSTRAINT fk_mgr FOREIGN KEY (manager_id) REFERENCES employees(emp_id)
);

-- Products & Categories (1‑to‑many Products→Categories)
CREATE TABLE categories (
  category_id INT PRIMARY KEY AUTO_INCREMENT,
  category_name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE products (
  product_id INT PRIMARY KEY AUTO_INCREMENT,
  product_name VARCHAR(100) NOT NULL,
  category_id INT,
  unit_price DECIMAL(10,2) NOT NULL,
  active TINYINT(1) DEFAULT 1,
  CONSTRAINT fk_prod_cat FOREIGN KEY (category_id) REFERENCES categories(category_id)
);

-- Orders & Order Items (many‑to‑many Customers↔Products via order_items)
CREATE TABLE orders (
  order_id INT PRIMARY KEY AUTO_INCREMENT,
  customer_id INT NOT NULL,
  order_date DATE NOT NULL,
  status ENUM('NEW','PAID','SHIPPED','CANCELLED') DEFAULT 'NEW',
  CONSTRAINT fk_ord_cust FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

CREATE TABLE order_items (
  order_item_id INT PRIMARY KEY AUTO_INCREMENT,
  order_id INT NOT NULL,
  product_id INT NOT NULL,
  qty INT NOT NULL,
  unit_price DECIMAL(10,2) NOT NULL,
  CONSTRAINT fk_item_order FOREIGN KEY (order_id) REFERENCES orders(order_id),
  CONSTRAINT fk_item_prod  FOREIGN KEY (product_id) REFERENCES products(product_id)
);
```

### Seed Data

```sql
INSERT INTO regions (region_name) VALUES ('South'),('North'),('West'),('East');

INSERT INTO customers (customer_name,region_id,email,created_at) VALUES
('Arjun Nair', 1, 'arjun.nair@example.com', '2024-01-05'),
('Meena Rao',  1, 'meena.rao@example.com',  '2024-02-18'),
('Karthik Iyer',2, 'karthik.iyer@example.com','2024-03-10'),
('Zara Khan',  3, 'zara.khan@example.com',   '2024-04-22'),
('Vikram Patel',NULL,'vikram.patel@example.com','2024-05-01'); -- region unknown

INSERT INTO employees (emp_name, manager_id, dept, salary) VALUES
('Priya Singh', NULL, 'Sales', 90000.00),  -- CEO of Sales
('Rahul Mehta', 1,    'Sales', 60000.00),
('Sneha Das',   1,    'Sales', 62000.00),
('Dev Patel',   NULL, 'IT',    110000.00),
('Isha Gupta',  4,    'IT',     80000.00);

INSERT INTO categories (category_name) VALUES
('Beverages'),('Snacks'),('Stationery');

INSERT INTO products (product_name, category_id, unit_price, active) VALUES
('Masala Chai', 1, 120.00, 1),
('Filter Coffee',1, 150.00, 1),
('Banana Chips', 2,  80.00, 1),
('Trail Mix',    2, 200.00, 0),  -- inactive
('A4 Notebook',  3,  60.00, 1);

INSERT INTO orders (customer_id, order_date, status) VALUES
(1, '2024-06-01', 'PAID'),
(1, '2024-06-15', 'SHIPPED'),
(2, '2024-06-21', 'CANCELLED'),
(3, '2024-07-03', 'PAID'),
(4, '2024-07-10', 'NEW');

INSERT INTO order_items (order_id, product_id, qty, unit_price) VALUES
(1, 1, 2, 120.00), -- Masala Chai x2
(1, 3, 1,  80.00), -- Banana Chips x1
(2, 2, 1, 150.00), -- Filter Coffee x1
(3, 1, 3, 120.00),
(3, 5, 5,  60.00), -- A4 Notebook x5
(4, 3, 2,  80.00);
```

---

## 1) INNER JOIN — Top customers by total spent (paid or shipped)

**Question:** For each customer, compute **total amount** from orders with status `PAID` or `SHIPPED`. Show top 3.

**Answer (SQL):**
```sql
SELECT c.customer_id,
       c.customer_name,
       ROUND(SUM(oi.qty * oi.unit_price),2) AS total_spent
FROM customers c
JOIN orders o       ON o.customer_id = c.customer_id
JOIN order_items oi ON oi.order_id    = o.order_id
WHERE o.status IN ('PAID','SHIPPED')
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC
LIMIT 3;
```

**Expected Sample Output**
| customer_id | customer_name | total_spent |
|---|---|---|
| 3 | Karthik Iyer | 660.00 |
| 1 | Arjun Nair   | 320.00 |

**Explanation:**  
- Only `PAID/SHIPPED` orders contribute.  
- Pure **INNER JOIN** ensures customers without matching orders are excluded.  
- Multiply `qty * unit_price` at the line item level, then sum per customer.

**Pitfall:** Forgetting to filter by `status` can inflate totals. Use `WHERE o.status IN (...)` before grouping.

---

## 2) LEFT OUTER JOIN — Customers with/without orders

**Question:** List all customers with their **latest order date** (if any). Customers without orders should still appear.

**Answer (SQL):**
```sql
SELECT c.customer_id,
       c.customer_name,
       MAX(o.order_date) AS last_order_date
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY last_order_date DESC IS NULL, last_order_date DESC;
```

**Explanation:**  
- **LEFT JOIN** keeps all customers; unmatched `o.order_date` becomes NULL.  
- `ORDER BY last_order_date DESC IS NULL` pushes NULLs to the bottom (MySQL treats `TRUE=1`, `FALSE=0`).

**Pitfall:** Don’t put `o.order_date` conditions in `WHERE`, or you’ll turn it into an inner join. Put such conditions in the `ON` clause if needed.

---

## 3) RIGHT OUTER JOIN (rare) — Regions even if no customers

**Question:** Show all regions and count of customers in each, including regions with **zero customers**.

**Answer (SQL):**
```sql
SELECT r.region_name,
       COUNT(c.customer_id) AS customers_count
FROM customers c
RIGHT JOIN regions r ON r.region_id = c.region_id
GROUP BY r.region_id, r.region_name
ORDER BY r.region_name;
```

**Explanation:**  
- **RIGHT JOIN** ensures all `regions` are kept.  
- Any region without customers yields `COUNT(c.customer_id)=0` (since COUNT ignores NULLs).

**Tip:** You can do the same with `LEFT JOIN` by reversing table order: `regions r LEFT JOIN customers c ...`

---

## 4) FULL OUTER JOIN (MySQL workaround) — Customers vs Regions

**Question:** Return all **customers with region names**, plus **regions with no customers**. MySQL lacks `FULL OUTER JOIN`, so use `UNION ALL` + `WHERE` filters.

**Answer (SQL):**
```sql
-- Part A: regions with/without customers (left)
SELECT r.region_id, r.region_name, c.customer_id, c.customer_name
FROM regions r
LEFT JOIN customers c ON c.region_id = r.region_id

UNION ALL

-- Part B: customers with no matching region (right anti join)
SELECT r.region_id, r.region_name, c.customer_id, c.customer_name
FROM customers c
LEFT JOIN regions r ON r.region_id = c.region_id
WHERE r.region_id IS NULL;
```

**Explanation:**  
- First query: keeps all regions; customers may be NULL.  
- Second query: captures “dangling” customers with `NULL` region (e.g., `Vikram Patel`).  
- `UNION ALL` preserves all rows (no accidental de-dup).

**Pitfall:** Using `UNION` (without ALL) might drop duplicates unintentionally and adds extra sort work.

---

## 5) CROSS JOIN — Generate a price list per region (cartesian + filter)

**Question:** Create all combinations of active products × regions, but show only products with price **> 100**.

**Answer (SQL):**
```sql
SELECT r.region_name, p.product_name, p.unit_price
FROM regions r
CROSS JOIN products p
WHERE p.active = 1
  AND p.unit_price > 100
ORDER BY r.region_name, p.product_name;
```

**Explanation:**  
- **CROSS JOIN** creates a cartesian product; then `WHERE` filters.  
- Useful for **scaffolding** reference data (price lists, calendars).

**Pitfall:** Cartesian products explode row counts. Always add filters/limits where appropriate.

---

## 6) SELF JOIN — Employee → Manager chain

**Question:** List employees with their manager’s name. Show “(No Manager)” when NULL.

**Answer (SQL):**
```sql
SELECT e.emp_id,
       e.emp_name,
       COALESCE(m.emp_name, '(No Manager)') AS manager_name,
       e.dept, e.salary
FROM employees e
LEFT JOIN employees m ON m.emp_id = e.manager_id
ORDER BY e.dept, manager_name, e.emp_name;
```

**Explanation:**  
- Table `employees` joined to itself via `manager_id`.  
- **LEFT JOIN** ensures top-of-hierarchy managers still appear (manager is NULL).

---

## 7) Anti-Join (LEFT JOIN … IS NULL) — Customers with **no orders**

**Question:** Find customers who **never placed any order**.

**Answer (SQL):**
```sql
SELECT c.customer_id, c.customer_name
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.order_id IS NULL;
```

**Explanation:**  
- Classic **anti-join** pattern in MySQL.  
- After `LEFT JOIN`, unmatched rows have `o.order_id = NULL`.

**Pitfall:** Don’t use `NOT IN (SELECT ...)` with possible NULLs; prefer `NOT EXISTS` or the left‑anti join pattern.

---

## 8) Semi-Join (EXISTS) — Customers who bought from a specific category

**Question:** Return customers who bought **at least one “Beverages”** product.

**Answer (SQL):**
```sql
SELECT DISTINCT c.customer_id, c.customer_name
FROM customers c
WHERE EXISTS (
  SELECT 1
  FROM orders o
  JOIN order_items oi ON oi.order_id = o.order_id
  JOIN products p     ON p.product_id = oi.product_id
  JOIN categories cat ON cat.category_id = p.category_id
  WHERE o.customer_id = c.customer_id
    AND cat.category_name = 'Beverages'
);
```

**Explanation:**  
- **EXISTS** returns early when a match is found (often efficient).  
- `DISTINCT` ensures each customer appears only once.

---

## 9) Multi-table JOIN + Aggregation — Category revenue, top 2 by revenue

**Question:** Compute category-level **revenue** (sum of `qty*price`) from **paid/shipped** orders and show **top 2 categories**.

**Answer (SQL):**
```sql
WITH cat_rev AS (
  SELECT cat.category_name,
         SUM(oi.qty * oi.unit_price) AS revenue
  FROM categories cat
  JOIN products   p  ON p.category_id = cat.category_id
  JOIN order_items oi ON oi.product_id = p.product_id
  JOIN orders     o  ON o.order_id = oi.order_id
  WHERE o.status IN ('PAID','SHIPPED')
  GROUP BY cat.category_name
)
SELECT category_name, revenue
FROM cat_rev
ORDER BY revenue DESC
LIMIT 2;
```

**Explanation:**  
- Multiple INNER JOINs restrict to valid chains (category→product→order_items→orders).  
- `WITH` CTE used for clarity (MySQL 8+).

---

## 10) Window Function + JOIN — Rank customers by spend within region

**Question:** For each **region**, rank customers by **total spend** (paid/shipped). Show **top 2 per region**.

**Answer (SQL):**
```sql
WITH spend AS (
  SELECT r.region_name,
         c.customer_id,
         c.customer_name,
         SUM(oi.qty * oi.unit_price) AS total_spent
  FROM customers c
  JOIN regions r     ON r.region_id = c.region_id
  JOIN orders o      ON o.customer_id = c.customer_id
  JOIN order_items oi ON oi.order_id = o.order_id
  WHERE o.status IN ('PAID','SHIPPED')
  GROUP BY r.region_name, c.customer_id, c.customer_name
)
SELECT *
FROM (
  SELECT s.*,
         ROW_NUMBER() OVER (PARTITION BY region_name ORDER BY total_spent DESC) AS rn
  FROM spend s
) x
WHERE rn <= 2
ORDER BY region_name, total_spent DESC;
```

**Explanation:**  
- `ROW_NUMBER()` partitions by region and orders by spend.  
- Useful for “Top N per group” patterns.

---

## 11) Many‑to‑Many traversal — Customers who bought from **multiple categories**

**Question:** List customers who purchased products from **≥ 2 distinct categories**.

**Answer (SQL):**
```sql
SELECT c.customer_id, c.customer_name,
       COUNT(DISTINCT cat.category_id) AS distinct_categories
FROM customers c
JOIN orders o       ON o.customer_id = c.customer_id
JOIN order_items oi ON oi.order_id    = o.order_id
JOIN products p     ON p.product_id   = oi.product_id
JOIN categories cat ON cat.category_id = p.category_id
WHERE o.status IN ('PAID','SHIPPED','NEW')  -- include NEW if desired
GROUP BY c.customer_id, c.customer_name
HAVING COUNT(DISTINCT cat.category_id) >= 2;
```

**Explanation:**  
- Count **distinct** categories per customer; filter with `HAVING` after grouping.

---

## 12) LEFT JOIN with filter in ON vs WHERE — include zero‑quantity future edge cases

**Question:** Show orders and item extended price. Keep orders even if items are **missing** or later filtered (e.g., inactive product).

**Answer (SQL):**
```sql
SELECT o.order_id,
       o.status,
       p.product_name,
       oi.qty,
       (oi.qty * oi.unit_price) AS line_total
FROM orders o
LEFT JOIN order_items oi
  ON oi.order_id = o.order_id
LEFT JOIN products p
  ON p.product_id = oi.product_id
 AND p.active = 1            -- filter in ON to keep order even if product inactive
ORDER BY o.order_id, p.product_name;
```

**Explanation:**  
- Placing `p.active = 1` in the **ON** clause ensures unmatched rows remain (NULL product), preserving orders.  
- If this condition were in the **WHERE** clause, it would filter rows post-join and behave like an INNER JOIN.

---

## 13) RIGHT/LEFT JOIN Symmetry — Regions with sales and without

**Question:** Show all regions with **total revenue**, including regions with **no sales**.

**Answer (SQL):**
```sql
SELECT r.region_name,
       COALESCE(SUM(oi.qty * oi.unit_price), 0) AS revenue
FROM regions r
LEFT JOIN customers c ON c.region_id = r.region_id
LEFT JOIN orders    o ON o.customer_id = c.customer_id AND o.status IN ('PAID','SHIPPED')
LEFT JOIN order_items oi ON oi.order_id = o.order_id
GROUP BY r.region_name
ORDER BY r.region_name;
```

**Explanation:**  
- Chain **LEFT JOINs** from the preserving side (`regions`).  
- Use `COALESCE` to turn NULL sums into 0.

---

## 14) Detect “orphan” facts — Order items pointing to inactive or missing product

**Question:** List order items whose product is **inactive** or **missing**.

**Answer (SQL):**
```sql
SELECT oi.order_item_id, oi.order_id, oi.product_id, oi.qty, oi.unit_price,
       p.product_name, p.active
FROM order_items oi
LEFT JOIN products p ON p.product_id = oi.product_id
WHERE p.product_id IS NULL OR p.active = 0;
```

**Explanation:**  
- **LEFT JOIN** + `IS NULL` finds missing product refs.  
- Include `active=0` to flag logically deleted items.

---

## 15) Join + DISTINCT vs GROUP BY — unique buyers per category

**Question:** For each category, how many **unique customers** bought items?

**Answer (SQL):**
```sql
SELECT cat.category_name,
       COUNT(DISTINCT o.customer_id) AS unique_buyers
FROM categories cat
JOIN products   p  ON p.category_id = cat.category_id
JOIN order_items oi ON oi.product_id = p.product_id
JOIN orders     o  ON o.order_id = oi.order_id
WHERE o.status IN ('PAID','SHIPPED')
GROUP BY cat.category_name
ORDER BY unique_buyers DESC, cat.category_name;
```

**Explanation:**  
- `COUNT(DISTINCT ...)` at category granularity avoids overcounting multiple items/orders of same customer.

---

## Indexing Tips for JOINs

- Index all **foreign keys**: `customers(region_id)`, `orders(customer_id)`, `order_items(order_id)`, `order_items(product_id)`, `products(category_id)`, etc.  
- For frequent filters: `(status)`, `(active)`, `(order_date)`.  
- Composite index order should match the most selective leftmost columns used in `WHERE`/`JOIN` (e.g., `(customer_id, status)` if both are common).  
- Validate with `EXPLAIN`.

```sql
CREATE INDEX ix_orders_customer_status ON orders(customer_id, status);
CREATE INDEX ix_items_order_product    ON order_items(order_id, product_id);
```

---

## Quick Practice Tasks

1. **Last 30 days revenue per region (include zeros)** using LEFT joins + date filter in `ON`.  
2. **Top product per category by revenue** using window functions.  
3. **Customers who ordered only Snacks but never Beverages** (semi-join + anti-join pattern).  
4. **Find potential duplicates** in customers by email domain grouping.  
5. **Monthly cohort**: first order month per customer, then join to all orders to compute retention.

---

### Cheat Sheet — When to use which JOIN?

- **INNER JOIN**: require matches in both.  
- **LEFT OUTER JOIN**: keep all from left and add matches from right.  
- **RIGHT OUTER JOIN**: symmetric to LEFT; less common.  
- **FULL OUTER JOIN**: emulate via `UNION ALL` of left join + right anti-join.  
- **CROSS JOIN**: all combinations; use sparingly.  
- **SELF JOIN**: hierarchical/graph relationships within same table.  
- **SEMI JOIN**: `EXISTS` / `IN` to test existence.  
- **ANTI JOIN**: `LEFT JOIN ... WHERE right.id IS NULL`.

---

**You’re interview-ready.** Run the schema and samples in a local MySQL or Docker container, then tweak filters and windows for mastery.
