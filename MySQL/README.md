---
title: 'MySQL Learning Notes'
---

MySQL Learning Notes
===

   * [MySQL Learning Notes](#mysql-learning-notes)
      * [Engines](#engines)
      * [Editting Table](#editting-table)
      * [Commands](#commands)
      * [Types](#types)
      * [Aggregate Functions](#aggregate-functions)
      * [Time](#time)
      * [Design Mode](#design-mode)


## Engines
```sql
SHOW ENGINES

SHOW TABLE STATUS

CREATE TABLE test (id int) engine=MYISAM

SET default_storage_engine=MYISAM
```

## Editting Table
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

**Add Column**
```sql
ALTER TABLE person 
ADD COLUMN email VARCHAR(50) NOT NULL
```

**Add Foreign Key**
```sql
ALTER TABLE book
ADD CONSTRAINT fk_library FOREIGN KEY (library) REFERENCES library(id)
```

**Add Index**
A database index is a data structure that improves the speed of operations in a table. While creating index, it should be taken into consideration which all columns will be used to make SQL queries and create one or more indexes on those columns.
Note that users cannot see the indexes.
```sql
ALTER TABLE music
ADD INDEX idx_band(band)
```

**Create User**
```sql
CREATE user 'JohnDoe'@'localhost' IDENTIFIED BY 'password'
GRANT SELECT ON database1.* TO JohnDoe@localhost
```

## Making Queries

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

IN (...)      -- similar to equality
e.g. 
SELECT * FROM person WHERE id 
IN (SELECT id FROM person WHERE id > 2) -- subquery

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
WHERE id > 5
ORDER BY state, first_name

-- or

ORDER BY id DESC   -- descending order
```

**Group By**
```sql
SELECT gender, AVG(weight) FROM survey GROUP BY gender
```

**WHERE** and **HAVING**
WHERE is used to filer rows before grouping
HAVING is used to exclude records **after** grouping
```sql
SELECT count(*), country 
FROM survey 
GROUP BY country 
HAVING count(*) > 3 
ORDER BY count(*);
```

**Distinct**
Avoiding Duplicates
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

-- Multiple Joins
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
JOIN employees m     -- same table, different alias
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
**Left Join** 
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
**Union**
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
```
e.g.
```sql
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

**Insert** 
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

**Delete**
```sql
DELETE FROM invoices
WHERE client_id = (
    SELECT *
    FROM clients
    WHERE name = 'John'
)
```

**Inline Views**
This is an inefficient way of making queries
```sql
SELECT AVG(respondents) 
FROM (SELECT country,COUNT(*) as respondents FROM survey GROUP BY country) AS totals
-- making a temporary table for a table
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

## Functions
```sql
USE TEST;
SELECT * FROM books;
-- concatenate
SELECT CONCAT('Title: ',title) from books;
-- uppercase
SELECT UCASE('john');
-- take first 3 characters on the left
SELECT LEFT('Singapore', 3);
-- trim empty spaces on both sides
SELECT TRIM('   boy   ');
```
**Control Flow**
```sql
-- if first statement is true, then output 'Hello', else output 'Goodbye'
SELECT IF (true,'Hello','Goodbye');

SELECT IF(part_id IS NOT NULL,part_id, catalog_id) FROM parts;

WHERE IF(part_id IS NOT NULL, part_id, catalog_id) = 1234
-- if part_id is null, output catalog_id
SELECT IFNULL(part_id, catalog_id) AS identifier FROM parts;
```

**Casting**
```sql
SELECT cast('2020-02-28' AS CHAR);
-- casting integer to char to concat 2 strings
SELECT CONCAT('No. of books: ', cast(count(*) AS CHAR)) FROM books;
```


## User Defined Functions
Basic Syntax
```sql
DELIMITER $$

CREATE FUNCTION function_name(
    param1,
    param2,…
)
RETURNS datatype
[NOT] DETERMINISTIC
BEGIN
 -- statements
END $$

DELIMITER ;
```

```sql
DELIMITER $$

CREATE FUNCTION CustomerLevel(
	credit DECIMAL(10,2)
) 
RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
    DECLARE customerLevel VARCHAR(20);

    IF credit > 50000 THEN
		SET customerLevel = 'PLATINUM';
    ELSEIF (credit >= 50000 AND 
			credit <= 10000) THEN
        SET customerLevel = 'GOLD';
    ELSEIF credit < 10000 THEN
        SET customerLevel = 'SILVER';
    END IF;
	-- return the customer level
	RETURN (customerLevel);
END$$
DELIMITER ;
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

## Date and Time
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
**Current Date**
```sql
-- current date add 28 days
SELECT CURDATE() + INTERVAL 28 DAY;
-- date minus 2 months
SELECT DATE_SUB('2020-04-28', INTERVAL 2 MONTH);
-- getting the name of that day, Tuesday
SELECT DAYNAME('2020-04-28')
-- getting the difference in dates, in days
SELECT DAYDIFF(CURDATE(),'2020-04-28');
-- days to date
SELECT FROM_DAYS(366) -- 0001-01-01
```
**String to Date**
```sql
-- convert date with slashes to conventional date in MySQL
SELECT STR_TO_DATE('28/04/2020','%d/%m/%Y')
-- convert date to Tuesday 25th February 2020
SELECT DATE_FORMAT('2020-02-25','%W %D %M %Y')
```

## Design Mode

VARCHAR(50) = variable character, can store up to 50 characters
PK = Primary Key, will be unique for every row
NN = Not null, if not checked, this col can be null
AI = Auto increment

**Foreign Key**
```sql
CREATE TABLE person(id INT primary key auto_increment,
name VARCHAR(50),
address_id INT,
FOREIGN KEY (address_id) REFERENCES address(id))
-- address_id references another table 'address'
```

**Cascade**
When a record is deleted from the parent table, the child records with match id will also be deleted.
```sql
ON DELETE CASCADE
ON UPDATE CASCADE
```

## Locks and Transaction
**Lock Table**
```sql
LOCK TABLES table1 [READ | WRITE]
UNLOCK TABLES
```
**Manual Commit**
```sql
SET autocommit=0;
INSERT INTO books (name) values ('book1');
commit;  -- book1 will only be added with this command

rollback; -- rollback the previous commit
```
**Transaction**
Instead of toggling autocommit, we can simply start a transaction
```sql
START TRANSACTION;
SELECT * FROM books;
UPDATE books SET name = 'book2' WHERE id = 2;
COMMIT;
```
**Select ... For Update**
Locks the row when it is being updated
```sql
START TRANSACTION;
SET @withdraw = 500;
SET @account = 1;

SELECT balance FROM accounts WHERE id=@account FOR UPDATE;
UPDATE accounts SET balance = balace - @withdraw WHERE id=@account;
COMMIT;
```

**Row Locking**
Row locking uses indexes
```sql
SELECT @@session.tx_isolation; -- default

SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE
```

**Save Points**
```sql
START TRANSACTION
INSERT INTO books (name) VALUES ('book1');
SELECT * FROM books;
DELETE FROM books WHERE id=4;
SAVEPOINT test1;
UPDATE books SET name = 'The Animal Book' where id = 1;
ROLLBACK TO test1;
ROLLBACK -- make sure to finish the transaction
```

## Using Variables
```sql
SET @user = 'JohnDoe';
SELECT @user;

SET @min_value = 1;
SELECT * FROM sales WHERE transaction_value > @ min_value;
```
```sql
SELECT @total := SUM(transaction_value) from sales;
INSERT INTO sales_history (recorded, total) VALUES (now(), @total);
```
**Fixing Select-Updates with Table Locks**
This prevents sales from getting updated while sales history is being updated. 
```sql
LOCK TABLES sales READ, sales_history WRITE;
SELECT @total := SUM(transaction_value) FROM sales;
INSERT INTO sales_history (recorded, total) values (now(), @total);
UNLOCK TABLES;
```


## Challenge

We have a table which gives us a list of seats in a cinema, and tells us which seats are taken/not taken. How can we find out a pair of seats that are free side by side?

```sql
SELECT 
    s1.id as 'Seat One ID',
    s2.id as 'Seat Two ID'
FROM
    seats s1
JOIN
    seats s2
ON s1.id + 1 = s2.id  -- this will step up the rows by one
AND s1.free = true AND s2.free = true
```



