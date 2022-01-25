# 关系型数据库

## 关系型数据库

一种结构化数据库，其中数据以表格格式存储；通常支持使用 SQL 进行强大的查询。

## 非关系数据库

与关系数据库（SQL 数据库）相比，它是一种没有强加的表格结构的数据库。
非关系型数据库通常被称为 NoSQL 数据库。

## SQL

结构化查询语言（Structured Query Language）。例如在关系型数据库 Postgres 中， 使用派生自 SQL 的 PostgreSQL 来操作数据。

## SQL 数据库

任何支持 SQL 的数据库。该术语通常与“关系型数据库”同义使用，但实际上并非每个关系型数据库都支持 SQL。

## NoSQL 数据库

任何与 SQL 不兼容的数据库都称为 NoSQL。

## ACID 事务

一种具有四个重要属性的数据库事务：

- 原子性（Atomicity）：构成事务的操作要么全部成功，要么全部失败。没有中间状态。
- 一致性（Consistency）：事务不能使数据库进入无效状态。事务提交或回滚后，每条记录的规则仍然适用，所有未来的发起的事务都会看到前一次事务产生的效果。也叫强一致性。
- 隔离（Isolation）：多个事务同时执行，效果与按顺序执行一样。
- 持久性（Durability）：任何提交的事务都被写入非易失性存储。它不会因崩溃、断电或网络原因而导致事务失效。

## 数据库索引

一种特殊的辅助数据结构，可以令数据库更快地执行某些查询。索引通常只能用于引用结构化数据，例如存储在关系数据库中的数据。
在实际运用中，在数据库中的一个或多个列上创建索引，以大大加快经常运行的读取查询，但缺点是写入数据库的时间稍长，因为写入也必须重新计算相关的索引。

## 强一致性

强一致性通常是指 ACID 事务的一致性，而不是最终一致性。

## 最终一致性

与强一致性不同的一致性模型。在此模型中，读取可能会返回陈旧的系统视图。最终一致的数据存储将保证数据库的状态最终会反映一段时间内（可能是 10 秒或几分钟）内的写入。

## Postgres

使用称为 PostgreSQL 的 SQL 语言的关系数据库。提供 ACID 事务。

## Demo

以下示例基于 Postgres 数据库

```sql
CREATE TABLE payments (
    customer_name varchar(128),
    processed_at date,
    amount int
);

CREATE TABLE balances (
    username varchar(128),
    balance int
);

CREATE TABLE large_table (
    random_int int
);

INSERT INTO payments VALUES ('clement', '2019-12-15', 10);
INSERT INTO payments VALUES ('antoine', '2020-01-01', 100);
INSERT INTO payments VALUES ('clement','2020-01-02', 10) ;
INSERT INTO payments VALUES ('antoine', '2020-01-02', 100);
INSERT INTO payments VALUES ('antoine', '2020-01-03', 100);
INSERT INTO payments VALUES ('simon', '2020-02-05', 1000);
INSERT INTO payments VALUES ('antoine', '2020-02-01', 100);
INSERT INTO payments VALUES ('clement', '2020-02-03', 10);
INSERT INTO payments VALUES ('meghan', '2020-01-12', 80);
INSERT INTO payments VALUES ('meghan', '2020-01-13', 70);
INSERT INTO payments VALUES ('meghan', '2020-01-14', 90);
INSERT INTO payments VALUES ('alex', '2019-12-11', 10);
INSERT INTO payments VALUES ('clement', '2020-02-01', 10);
INSERT INTO payments VALUES ('marli', '2020-01-18', 10);
INSERT INTO payments VALUES ('alex', '2019-12-15', 10);
INSERT INTO payments VALUES ('marli', '2020-01-25', 10);
INSERT INTO payments VALUES ('marli', '2020-02-02', 10);

INSERT INTO balances VALUES ('antoine', 0);
INSERT INTO balances VALUES ('clement', 1000);

INSERT INTO large_table ( random_int )
SELECT round( random() * 1000000000 )
FROM generate_series(1, 50000000) s(i);
```

```sql
/*
Powerful Queries
*/

-- Sum the number of payments for each user.
SELECT customer_name, count(*)
FROM payments
GROUP BY customer_name
ORDER BY count DESC;

-- Sum the payment amounts for each month.
SELECT sum(amount), extract(year from processed_at) as year, extract(month from processed_at) as month
FROM payments
GROUP BY month, year
ORDER BY sum DESC;

-- Sum the payment amounts for each month for each user.
SELECT customer_name, sum(amount), extract(year from processed_at) as year, extract(month from processed_at) as month
FROM payments
GROUP BY customer_name, month, year
ORDER BY customer_name DESC;

-- Find the Largest single-user payments for each month.
SELECT max(amount), year, month
FROM (
  SELECT customer_name, sum(amount) as amount, extract(year from processed_at) as year, extract(month from processed_at) as month
  FROM payments
  GROUP BY customer_name, month, year
) AS monthly_sums
GROUP BY year, month;

/*
Transactions
*/

-- Transfer 100 from Clement to Antoine.
BEGIN TRANSACTION;
UPDATE balances SET balance = balance - 100 WHERE username = 'clement';
UPDATE balances SET balance = balance + 100 WHERE username = 'antoine';
COMMIT;

/*
Indexes
*/

-- Find the 10 largest ints.
SELECT * FROM large_table ORDER BY random_int DESC LIMIT 10;

-- Create an index on the ints in the table.
CREATE INDEX large_table_random_int_idx ON large_table (random_int);
```

Last Modified 2022-01-25
