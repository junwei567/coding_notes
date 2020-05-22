---
title: 'MySQL Learning Notes'
---

MySQL Learning Notes
===

[TOC]

## Engines
```sql
SHOW ENGINES

SHOW TABLE STATUS

CREATE TABLE test (id int) engine=MYISAM

SET default_storage_engine=MYISAM
```

## Commands
**Create Table** (multiple columns)
Table named users
id accepts integer values and username accepts text
```sql
CREATE TABLE users (id int primary key, username text NOT NULL)

CREATE TABLE products (product VARCHAR(50), available BOOL default false)

CREATE TABLE products (
id INT primary key AUTO_INCREMENT,
name VARCHAR(50),  
category enum('fruit','vegetable','dairy'),  
sell_by DATE,
sold BOOL,
moment TIMESTAMP,
quantity iNT,
weight numeric(6,3)
);
```

**Get Description of Table**
Replace users with name of table
```sql
DESC users 
```

**Insert Data**
```sql
INSERT INTO users (id, username) VALUES (1,"Bob")
```

**Show Table**
```sql
SELECT * FROM users
```

**Delete Table**
```sql
DROP TABLE users      -- removes the entire 'user' table

DELETE FROM users     -- deletes all entries from table
```

**Auto Incrementing**
id will start from one, can never be zero
```sql
CREATE TABLE users (id int primary key AUTO_INCREMENT, name text NOT NULL)

INSERT INTO users (name) VALUES ('Aaron')
```

**First Query**
USE the specific database for query
SELECT the specific columns
```sql
USE sql_store;       
SELECT               
    name, 
    unit_price,
    unit_price * 1.1 AS 'new price'   
FROM products        -- FROM this specific table
```

**Comparison operators**
```
Greater than or equal: >=
Lesser than or equal: <=
Equality: =, is
Not equal: !=, <>
```

**Operators**
```sql
BETWEEN ... AND
IN

WHERE last_name LIKE '%tch%'    -- any last_name that has 'tch' inside
                LIKE 'h____'    -- any word that has 4 letters after 'h'

WHERE last_name REGEXP 'tch'    -- same as '%tch%'
                       '^field' -- last_name must start with 'field'
                       'field$' -- last_name must end with 'field'
                       'field|mac' -- any customer whole last_name has 'field' or 'mac'
                       'field$|mac|^rose' -- can be combined
                       '[gim]e' -- last_name has ge,ie, or me
                       '[a-h]e' -- from a to h
           
```
**Order By**
```sql
SELECT *
FROM customers
ORDER BY state, first_name

-- or

ORDER BY id DESC   -- descending order
```

**GROUP BY**
```sql
SELECT gender, AVG(weight) FROM survey GROUP BY gender
```

**Distinct**
```sql
SELECT DISTINCT name FROM users

SELECT COUNT(DISTINCT name) from users
```

**Limit**
Always come at the end
Show 10 entries only
```sql
LIMIT 10
```
Skip first 6 rows, show 3 rows after that
```sql
LIMIT 6,3  
```

**Join with ON clause**
Inner Join
Same column in multiple table, prefix it with any of the 2 table!
```sql
SELECT order_id, first_name, last_name, orders.customer_id 
FROM orders  
JOIN customers ON orders.customer_id = customers.customer_id

e.g.
USE sql_store;
SELECT order_id, o.product_id, name, quantity, o.unit_price
FROM order_items o
JOIN products p
    ON o.product_id = p.product_id

e.g.
USE sql_invoicing;
SELECT 
	p.date,
    p.invoice_id,
    p.amount,
    c.name,
    pm.name
FROM payments p
JOIN clients c
	ON p.client_id = c.client_id
JOIN payment_methods pm
	ON pm.payment_method_id = p.payment_method
    
```
**Self Join**
```sql
USE sql_hr;
SELECT *
FROM employees e
JOIN employees m
    ON e.reports_to = m.employee_id
```
**Compound Join**
```sql
SELECT *
FROM order_items oi
JOIN order_item_notes oin
    ON oi.order_id = oin.order_id
    AND oi.product_id = oin.product_id
```
**Outer Join**
```sql
SELECT
    c.customer_id,
    c.first_name,
    o.order_id
FROM customers c      -- this is the left table
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
ORDER BY c.customer_id
```
**LEFT JOIN** 
Takes everything from the left table, so even customers who do not have customer_id in orders table will appear

e.g.
```sql
SELECT
    c.customer_id,
    c.first_name,
    o.order_id,
    sh.name AS shipper
FROM customers c      
LEFT JOIN orders o
    ON c.customer_id = o.customer_id
LEFT JOIN shippers sh
    ON o.shipper_id = sh.shipper_id
ORDER BY c.customer_id
```
**Self outer join**
```sql
USE sql_hr;
SELECT 
	e.employee_id,
    e.first_name,
    m.first_name AS manager
FROM employees e
LEFT JOIN employees m
	ON e.reports_to = m.employee_id
```
**USING clause**
Only works when col name is exactly same across different tables
```sql
USE sql_store;
SELECT
	o.order_id,
    c.first_name,
    sh.name AS shipper
FROM orders o
JOIN customers c
	USING (customer_id)
LEFT JOIN shippers sh
	USING (shipper_id)
```
**Natural Join**
Join based on the common names
```sql
SELECT
    o.order_id,
    c.first_name
FROM orders o
NATURAL JOIN customers c
```
**Cross Join**
Join every record in first table with every record in second table
```sql
SELECT 
    c.first_name AS customer,
    p.name AS product
FROM customers c
CROSS JOIN products p
ORDER BY c.first_name

-- OR

SELECT 
    c.first_name AS customer,
    p.name AS product
FROM customers c, products p
ORDER BY c.first_name
```
**UNION**
Join data from multiple queries
Both queries have to have same no. of columns since they join into same columns
```sql
SELECT 
    order_id,
    order_date,
    'Active' AS status     -- string literal
FROM orders
WHERE order_date >= '2019-01-01'
UNION
SELECT 
    order_id,
    order_date,
    'Archived' AS status
FROM orders
WHERE order_date < '2019-01-01'

e.g.
SELECT
	customer_id,
    first_name,
    points,
    'Bronze' AS type
FROM customers
WHERE points < 2000
UNION
SELECT
	customer_id,
    first_name,
    points,
    'Silver' AS type
FROM customers
WHERE points BETWEEN 2000 AND 3000 
UNION
SELECT
	customer_id,
    first_name,
    points,
    'Gold' AS type
FROM customers
WHERE points > 3000

ORDER BY first_name

```

**INSERT** 
Insert single row
```sql
INSERT INTO customers (
    first_name, 
    last_name, 
    birth_date, 
    address, 
    city, 
    state)
VALUES (
    'John', 
    'Smith', 
    '1995-01-01', 
    'address', 
    'city', 
    'SG',),
    (
    ...      -- adding more than 1 row
    )
```

Inserting Hierarchical Rows
```sql
INSERT INTO orders (customer_id, order_date, status)
VALUES (1,'1-1-2020', 1);
    
INSERT INTO order_items 
VALUES
    (LAST_INSERT_ID(), 1, 1, 2.95),
    (LAST_INSERT_ID(), 2, 1, 3.95)
```

**Copy data from another table**
```sql
CREATE TABLE orders_archived;

INSERT INTO orders_archived
SELECT * 
FROM orders 
WHERE order_date < '2020-01-01'

e.g.
USE sql_invoicing;

CREATE TABLE invoices_archived AS
SELECT
    i.invoice_id,
    i.number,
    c.name AS client,
    i.invoice_total,
    i.payment_total,
    i.invoice_date,
    i.payment_date,
    i.due_date
    
FROM invoices i
JOIN clients c
    USING (client_id)
WHERE payment_date IS NOT NULL
```
**Updating data**
```sql
UPDATE invoices
SET payment_total = 10, payment_date = '2019-03-01'
WHERE invoice_id = 1 

e.g. 
UPDATE invoices
SET 
    payment_total = invoice_total * 0.5,
    payment_date = due_date
WHERE client_id = 
    (SELECT client_id
    FROM clients
    WHERE name = 'John')
    
e.g.
UPDATE invoices
SET comments = 'priority'
WHERE customer_id IN
    (SELECT customer_id
    FROM customers
    WHERE points > 3000)
```
MySQL workbench does not allow updating multiple rows of data
unless you uncheck SAFE update

```sql
SET sql_safe_updates=0
```

**DELETE**
```sql
DELETE FROM invoices
WHERE client_id = (
    SELECT *
    FROM clients
    WHERE name = 'John'
)
```

## Types
Common **Text** types are
```sql
VARCHAR(50)    -- variable
CHAR(10)       -- fixed
```

**Number Types**
```sql
INT

FLOAT          -- 4 bytes
DOUBLE         -- 8 bytes 
DECIMAL(4,2)   -- 4 digits, 2 decimals (123.45 will not fit since theres 5 digits!)
```

**Others**
```sql
Boolean: BOOL

Binary Large Object: BLOB
```

Enumerate
```sql
CREATE TABLE foods(
food VARCHAR(50) NOT NULL,
temperature ENUM('hot','cold') DEFAULT 'cold',
flavour ENUM('sweer','savoury') DEFAUL 'sweet'
);
```

## Aggregate Functions
**Count**
Get the number of rows in a table
```sql
SELECT COUNT(*) FROM users

SELECT COUNT(name) FROM users -- will not count names with null
```
**Others**
```sql
SELECT AVG(age) FROM users

-- MIN,MAX,SUM
```

## Time
```sql
SELECT now();

SELECT year(now()), time(now()), date(now());
```

**Time Stamps**
```sql
CREATE TABLE products (
product VARCHAR(50), 
sold_at TIMESTAMP default NOW(), 
received DATETIME
);
```


## Design Mode

VARCHAR(50) = variable character, can store up to 50 characters
PK = Primary Key, will be unique for every row
NN = Not null, if not checked, this col can be null
AI = Auto increment


