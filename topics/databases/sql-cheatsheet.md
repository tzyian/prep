
## Transaction Isolation Levels
- **READ UNCOMMITTED** → Dirty reads allowed (rarely supported).
- **READ COMMITTED** → No dirty reads, but non-repeatable reads possible.
- **REPEATABLE READ** → Prevents dirty & non-repeatable reads, phantom reads possible.
- **SERIALIZABLE** → Strictest, fully ACID, prevents all anomalies.

Dirty read: read uncommitted data
Phantom Read: read the same row in a single xact with different values, cos of interleaving commits
Non-repeatable read: collection of rows returned by a query is different cos of interleaving commits

## ACID
- **Atomicity** → All or nothing.
- **Consistency** → DB moves from one valid state to another.
- **Isolation** → Transactions don’t interfere with each other
- **Durability** → Once committed, changes persist even after crash.

## CAP
- **Consistency** → Every read sees the most recent write (single, up-to-date view of data).
- **Availability** → Every request receives a (non-error) response, even if it may not be the latest.
- **Partition Tolerance** → System continues to function despite arbitrary message loss or network splits.


## DDL – Data Definition Language

```sql
-- Create database
CREATE DATABASE db_name;

-- Create table
CREATE TABLE employees (
    emp_id      SERIAL PRIMARY KEY,
    name        VARCHAR(100) NOT NULL,
    salary      NUMERIC(10,2) DEFAULT 0,
    dept_id     INT,
    CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
);

-- Alter table
ALTER TABLE employees ADD COLUMN hire_date DATE DEFAULT CURRENT_DATE;
ALTER TABLE employees ALTER COLUMN salary SET DEFAULT 1000;
ALTER TABLE employees DROP COLUMN hire_date;
ALTER TABLE employees RENAME COLUMN name TO full_name;

-- Drop table
DROP TABLE employees;
DROP TABLE employees CASCADE;  -- remove dependents like views/constraints

-- Primary Key
ALTER TABLE employees ADD CONSTRAINT pk_emp PRIMARY KEY (emp_id);

-- Foreign Key
ALTER TABLE employees ADD CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id);

-- Unique
ALTER TABLE employees ADD CONSTRAINT unique_email UNIQUE (email);

-- Check
ALTER TABLE employees ADD CONSTRAINT check_salary CHECK (salary > 0);

-- Drop constraint
ALTER TABLE employees DROP CONSTRAINT fk_dept;

-- Deferred constraints
ALTER TABLE employees
    ADD CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES departments(dept_id)
    DEFERRABLE INITIALLY DEFERRED;

CREATE INDEX ON participation (emp_id, event_id);
```

## DML – Data Manipulation Language
```sql
-- Insert
INSERT INTO employees (name, salary, dept_id) VALUES ('Alice', 5000, 1);

-- Update
UPDATE employees SET salary = salary * 1.1 WHERE dept_id = 1;

-- Delete
DELETE FROM employees WHERE emp_id = 5;
TRUNCATE TABLE employees; -- faster delete all
```

## DQL - Data Query Language
```sql
-- Distinct
SELECT DISTINCT dept_id 
FROM employees
ORDER BY dept_id ASC

-- Conditional
SELECT name, 
CASE 
	WHEN salary > 5000 THEN 'High' 
	WHEN salary > 3000 THEN 'Mid'
	ELSE 'Low' END AS salary_level
FROM employees;

-- Null handling
-- COALESCE (return first non-NULL value)
SELECT name, COALESCE(email, 'no email') FROM employees;
-- NULLIF (return NULL if equal)
SELECT NULLIF(salary, 0) FROM employees;

-- Aggregates
SELECT dept_id, AVG(salary), COUNT(*) 
FROM employees
GROUP BY dept_id
HAVING AVG(salary) > 4000;
```

## Joins
```sql
-- Inner join (keep only columns on both)
SELECT e.name, d.dept_name
FROM employees e, departments d
WHERE e.dept_id = d.dept_id;

-- Left Outer Join
SELECT e.name, d.dept_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.dept_id;

-- Full Outer Join
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.dept_id;

-- simulated Full Outer
-- INNER JOIN
	SELECT e.emp_id, e.name, d.dept_name
	FROM employees e, departments d
	WHERE e.dept_id = d.dept_id
UNION -- LEFT OUTER JOIN
	SELECT e.emp_id, e.name, NULL
	FROM employees e
	WHERE NOT EXISTS (
	  SELECT 1 FROM departments d WHERE e.dept_id = d.dept_id
	)
UNION -- RIGHT OUTER JOIN
	SELECT NULL, NULL, d.dept_name
	FROM departments d
	WHERE NOT EXISTS (
	  SELECT 1 FROM employees e WHERE e.dept_id = d.dept_id
	);
```
## Views and Triggers
```sql
-- View
CREATE VIEW high_salary_emps AS
SELECT name, salary FROM employees WHERE salary > 5000;

-- Trigger
CREATE TRIGGER salary_check
BEFORE INSERT OR UPDATE ON employees
FOR EACH ROW
WHEN (NEW.salary < 0)
BEGIN
    RAISE EXCEPTION 'Salary cannot be negative';
END;

-- Deferred trigger (Postgres style)
CREATE CONSTRAINT TRIGGER dept_check
AFTER INSERT ON employees
DEFERRABLE INITIALLY DEFERRED
FOR EACH ROW EXECUTE FUNCTION check_department();

-- DEFRRABLE INITIALLY DEFERRED (after transaction)
-- DEFERRABLE INITIALLY IMMEDIATE (after each statement)
-- NOT DEFERRABLE (default)

-- Simple function
CREATE FUNCTION get_bonus(s NUMERIC) RETURNS NUMERIC AS $$
BEGIN
    RETURN s * 0.10;
END;
$$ LANGUAGE plpgsql;

SELECT name, get_bonus(salary) FROM employees;
```

## Transactions
```sql
-- Start transaction
BEGIN;

-- Example: Transfer money
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- Commit changes
COMMIT;

-- Rollback changes
ROLLBACK;

-- Savepoints (rollback to a point inside a transaction)
SAVEPOINT before_update;
UPDATE accounts SET balance = balance - 500 WHERE id = 3;
ROLLBACK TO SAVEPOINT before_update;

-- Set transaction isolation level
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
``` 

# Common Questions

1. Employees in Top 10% of All Salaries
```sql
SELECT *
FROM employees
WHERE salary >= (
    SELECT PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary) 
    FROM employees
);
```

2. Top 3 Salaries Per Department (with Names)
```sql
SELECT *
FROM (
    SELECT e.*,
           ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rn
    FROM employees e
) t
WHERE rn <= 3;
```

3. 3rd highest salary
```sql
-- 3rd highest salary overall
SELECT salary
FROM (
  SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) rnk
  FROM employees
) t
WHERE rnk = 3;
```

4. For Each Dept: Count of Employees in Top 10% of salaries, and Median Salary of those
```sql
WITH dept_p90 AS (
  SELECT dept_id,
         PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary) AS p90_cutoff
  FROM employees
  GROUP BY dept_id
),
top10 AS (
  SELECT e.dept_id, e.salary
  FROM employees e
  JOIN dept_p90 d USING (dept_id)
  WHERE e.salary >= d.p90_cutoff
)
-- median of the top 10
SELECT t.dept_id,
       COUNT(*) AS top_10_count,
       PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY t.salary) AS median_top10_salary
FROM top10 t
GROUP BY t.dept_id
ORDER BY t.dept_id ASC;
```

5. Employees Participating 2–3 Times in Every Event
```sql
-- employee(emp_id INT)
-- participation(emp_id INT, event_id INT)
-- events(event_id INT)

WITH emp_event_counts AS (
    SELECT emp_id, event_id, COUNT(*) AS cnt
    FROM participation
    GROUP BY emp_id, event_id
)
SELECT emp_id
FROM emp_event_counts
GROUP BY emp_id
HAVING 
	-- Employee must have the same number of events as the total events
	COUNT(DISTINCT event_id) = (SELECT COUNT(*) FROM events)
	-- And all counts must be between 2 and 3
	AND MIN(cnt) >= 2
	AND MAX(cnt) <= 3
```

6. Salary running total per dept
```sql
SELECT emp_id, dept_id, salary,
       SUM(salary) OVER (PARTITION BY dept_id ORDER BY emp_id) AS running_total
FROM employees;
-- order by to get 1, 2, 3 rather than 3, 3, 3
```

7. Gaps and Islands
```sql
-- Missing employee IDs
SELECT e1.emp_id + 1 AS missing_id
FROM employees e1
LEFT JOIN employees e2 ON e1.emp_id + 1 = e2.emp_id
WHERE e2.emp_id IS NULL;

-- Consecutive streak of salaries increasing
SELECT emp_id, salary,
       salary - ROW_NUMBER() OVER (ORDER BY salary) AS grp
FROM employees
GROUP BY emp_id, salary, grp;
```

8. Running Totals & Moving Averages
```sql
-- Running total
SELECT emp_id, salary,
       SUM(salary) OVER (ORDER BY emp_id) AS running_total
FROM employees;

-- 3-month moving average salary
SELECT emp_id, hire_date, salary,
       AVG(salary) OVER (
           ORDER BY hire_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ) AS moving_avg
FROM employees;
```

```sql
-- Year, current sales, last year sales
SELECT year,
       cur_sales,
       LAG(cur_sales) OVER (ORDER BY year ASC) AS last_year_sales
FROM (
    SELECT year, SUM(cost) AS cur_sales
    FROM orders
    GROUP BY year
) t
ORDER BY year ASC;
```

## Equivalent SQL Functions

Return the bib and the name of the different riders who have a NULL result for results
```sql
-- LEFT OUTER JOIN
SELECT r.bib , r.name
FROM riders r 
LEFT OUTER JOIN results_individual i 
ON r.bib = i.rider
WHERE i.rider IS NULL;

-- EXCEPT
SELECT r.bib, r.name
FROM riders r
EXCEPT
SELECT i.rider, r1.name
FROM riders r1, results_individual i
WHERE r1.bib = i.rider;

-- NOT IN
SELECT r.bib, r.name
FROM riders r
WHERE r.bib NOT IN (
	SELECT i.rider 
	FROM results_individual i
);

-- <> ALL
SELECT r.bib , r.name
FROM riders r
WHERE r.bib <> ALL (
	SELECT i.rider 
	FROM results_individual i
);

-- NOT EXISTS
SELECT r.bib , r.name
FROM riders r
WHERE NOT EXISTS (
	SELECT 1 
	FROM results_individual i
	WHERE i.rider = r.bib
);

```

## Window Functions
```sql
ROW_NUMBER()   OVER (PARTITION BY dept ORDER BY salary DESC) -- unique rank
RANK()         OVER (PARTITION BY dept ORDER BY salary DESC) -- same rank for identical, skipping numbers
DENSE_RANK()   OVER (PARTITION BY dept ORDER BY salary DESC) -- same rank for identical, with next consecutive row
NTILE(4)       OVER (ORDER BY salary) -- quartiles
LEAD(salary)   OVER (ORDER BY salary) -- next row
LAG(salary)    OVER (ORDER BY salary) -- previous row
```


### Pivot

| year | region | amount |
| ---- | ------ | ------ |
| 2023 | East   | 1000   |
| 2023 | West   | 1500   |
| 2023 | North  | 1200   |
| 2024 | East   | 1100   |
| 2024 | West   | 1600   |
| 2024 | North  | 1300   |

| year | East | West | North |
| ---- | ---- | ---- | ----- |
| 2023 | 1000 | 1500 | 1200  |
| 2024 | 1100 | 1600 | 1300  |

```sql
SELECT
  year,
  SUM(CASE WHEN region = 'East'  THEN amount END) AS east,
  SUM(CASE WHEN region = 'West'  THEN amount END) AS west,
  SUM(CASE WHEN region = 'North' THEN amount END) AS north
FROM sales
GROUP BY year
ORDER BY year;
```
