---
title: SQL
date: 2021-04-24 13:10:25
author: myqlrs
categories:
  - SQL
tags:
  - SQL
  - 笔记
keywords:
  - SQL
---

# SQL

## 一、基本概念

### 1. 数据库术语

- `数据库（database）` - 保存有组织的数据的容器（通常是一个文件或一组文件）。
- `数据表（table）` - 某种特定类型数据的结构化清单。
- `模式（schema）` - 关于数据库和表的布局及特性的信息。模式定义了数据在表中如何存储，包含存储什么样的数据，数据如何分解，各部分信息如何命名等信息。数据库和表都有模式。
- `列（column）` - 表中的一个字段。所有表都是由一个或多个列组成的。
- `行（row）` - 表中的一个记录。
- `主键（primary key）` - 一列（或一组列），其值能够唯一标识表中每一行。

### 2. SQL语法

<img src="https://gitee.com/myqlrs/cloudimage/raw/master/image/image-20210307211740379.png" alt="image-20210307211740379" style="zoom:80%;" />

**SQL 语法结构包括：**

- **`子句`** - 是语句和查询的组成成分。（在某些情况下，这些都是可选的。）
- **`表达式`** - 可以产生任何标量值，或由列和行的数据库表
- **`谓词`** - 给需要评估的 SQL 三值逻辑（3VL）（true/false/unknown）或布尔真值指定条件，并限制语句和查询的效果，或改变程序流程。
- **`查询`** - 基于特定条件检索数据。这是 SQL 的一个重要组成部分。
- **`语句`** - 可以持久地影响纲要和数据，也可以控制数据库事务、程序流程、连接、会话或诊断。

**SQL 语法要点**

- **SQL 语句不区分大小写**，但是数据库表名、列名和值是否区分，依赖于具体的 DBMS 以及配置。

例如：`SELECT` 与 `select` 、`Select` 是相同的。

- **多条 SQL 语句必须以分号（`;`）分隔**。
- 处理 SQL 语句时，**所有空格都被忽略**。SQL 语句可以写成一行，也可以分写为多行。

```mysql
-- 一行 SQL 语句
UPDATE user SET username='robot', password='robot' WHERE username = 'root';

-- 多行 SQL 语句
UPDATE user
SET username='robot', password='robot'
WHERE username = 'root';
```

- SQL 支持三种注释

```mysql
## 注释1
-- 注释2
/* 注释3 */
```

### 3. SQL 分类

**(1) 数据定义语言（DDL）**

数据定义语言（Data Definition Language，DDL）是 SQL 语言集中负责数据结构定义与数据库对象定义的语言。

DDL 的主要功能是**定义数据库对象**。

DDL 的核心指令是 `CREATE`、`ALTER`、`DROP`。

**(2) 数据操纵语言（DML）**

数据操纵语言（Data Manipulation Language, DML）是用于数据库操作，对数据库其中的对象和数据运行访问工作的编程语句。

DML 的主要功能是 **访问数据**，因此其语法都是以**读写数据库**为主。

DML 的核心指令是 `INSERT`、`UPDATE`、`DELETE`、`SELECT`。这四个指令合称 CRUD(Create, Read, Update, Delete)，即增删改查。

**(3) 事务控制语言（TCL）**

事务控制语言 (Transaction Control Language, TCL) 用于**管理数据库中的事务**。这些用于管理由 DML 语句所做的更改。它还允许将语句分组为逻辑事务。

TCL 的核心指令是 `COMMIT`、`ROLLBACK`。

**(4) 数据控制语言（DCL）**

数据控制语言 (Data Control Language, DCL) 是一种可对数据访问权进行控制的指令，它可以控制特定用户账户对数据表、查看表、预存程序、用户自定义函数等数据库对象的控制权。

DCL 的核心指令是 `GRANT`、`REVOKE`。

DCL 以**控制用户的访问权限**为主，因此其指令作法并不复杂，可利用 DCL 控制的权限有：`CONNECT`、`SELECT`、`INSERT`、`UPDATE`、`DELETE`、`EXECUTE`、`USAGE`、`REFERENCES`。

根据不同的 DBMS 以及不同的安全性实体，其支持的权限控制也有所不同。

（以下为 DML 语句用法）

## 二、增删改查

> 增删改查，又称为 CRUD，数据库基本操作中的基本操作。

### 1. 插入数据

> - `INSERT INTO` 语句用于向表中插入新记录。

**插入完整的行**

```mysql
INSERT INTO user
VALUES (10, 'root', 'root', 'xxxx@163.com');
```

**插入行的一部分**

```mysql
INSERT INTO user(username, password, email)
VALUES ('admin', 'admin', 'xxxx@163.com');
```

**插入查询出来的数据**

```mysql
INSERT INTO user(username)
SELECT name
FROM account;
```

### 2. 更新数据

> - `UPDATE` 语句用于更新表中的记录。

```mysql
UPDATE user
SET username='robot', password='robot'
WHERE username = 'root';
```

### 3. 删除数据

> - `DELETE` 语句用于删除表中的记录。
> - `TRUNCATE TABLE` 可以清空表，也就是删除所有行。

**删除表中的指定数据**

```mysql
DELETE FROM user
WHERE username = 'robot';
```

**清空表中的数据**

```mysql
TRUNCATE TABLE user;
```

### 4. 查询数据

> - `SELECT` 语句用于从数据库中查询数据。
>
> - `DISTINCT` 用于返回唯一不同的值。它作用于所有列，也就是说所有列的值都相同才算相同。
>
> - `LIMIT` 限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。
>
> - - `ASC` ：升序（默认）
>   - `DESC` ：降序

**查询单列**

```mysql
SELECT prod_name
FROM products;
```

**查询多列**

```mysql
SELECT prod_id, prod_name, prod_price
FROM products;
```

**查询所有列**

```mysql
SELECT *
FROM products;
```

**查询不同的值**

```mysql
SELECT DISTINCT
vend_id FROM products;
```

**限制查询结果**

```mysql
-- 返回前 5 行
SELECT * FROM mytable LIMIT 5;
SELECT * FROM mytable LIMIT 0, 5;
-- 返回第 3 ~ 5 行
SELECT * FROM mytable LIMIT 2, 3;
```

## 三、子查询

> 子查询是嵌套在较大查询中的 SQL 查询。子查询也称为**内部查询**或**内部选择**，而包含子查询的语句也称为**外部查询**或**外部选择**。

- 子查询可以嵌套在 `SELECT`，`INSERT`，`UPDATE` 或 `DELETE` 语句内或另一个子查询中。

- 子查询通常会在另一个 `SELECT` 语句的 `WHERE` 子句中添加。

- 您可以使用比较运算符，如 `>`，`<`，或 `=`。比较运算符也可以是多行运算符，如 `IN`，`ANY` 或 `ALL`。

- 子查询必须被圆括号 `()` 括起来。

- 内部查询首先在其父查询之前执行，以便可以将内部查询的结果传递给外部查询。执行过程可以参考下图：

<img src="https://gitee.com/myqlrs/cloudimage/raw/master/image/image-20210524235707797.png" alt="image-20210524235707797" style="zoom:80%;" />

**子查询的子查询**

```mysql
SELECT cust_name, cust_contact
FROM customers
WHERE cust_id IN (SELECT cust_id
                  FROM orders
                  WHERE order_num IN (SELECT order_num
                                      FROM orderitems
                                      WHERE prod_id = 'RGAN01'));
```

### 1. WHERE

- `WHERE` 子句用于过滤记录，即缩小访问数据的范围。
- `WHERE` 后跟一个返回 `true` 或 `false` 的条件。
- `WHERE` 可以与 `SELECT`，`UPDATE` 和 `DELETE` 一起使用。
- 可以在 `WHERE` 子句中使用的操作符

| 运算符  | 描述                                                   |
| :------ | :----------------------------------------------------- |
| =       | 等于                                                   |
| <>      | 不等于。注释：在 SQL 的一些版本中，该操作符可被写成 != |
| >       | 大于                                                   |
| <       | 小于                                                   |
| >=      | 大于等于                                               |
| <=      | 小于等于                                               |
| BETWEEN | 在某个范围内                                           |
| LIKE    | 搜索某种模式                                           |
| IN      | 指定针对某个列的多个可能值                             |

**`SELECT` 语句中的 `WHERE` 子句**

```mysql
SELECT * FROM Customers
WHERE cust_name = 'Kids Place';
```

**`UPDATE` 语句中的 `WHERE` 子句**

```mysql
UPDATE Customers
SET cust_name = 'Jack Jones'
WHERE cust_name = 'Kids Place';
```

**`DELETE` 语句中的 `WHERE` 子句**

```mysql
DELETE FROM Customers
WHERE cust_name = 'Kids Place';
```

### 2. IN 和 BETWEEN

- `IN` 操作符在 `WHERE` 子句中使用，作用是在指定的几个特定值中任选一个值。
- `BETWEEN` 操作符在 `WHERE` 子句中使用，作用是选取介于某个范围内的值。

**IN 示例**

```mysql
SELECT *
FROM products
WHERE vend_id IN ('DLL01', 'BRS01');
```

**BETWEEN 示例**

```mysql
SELECT *
FROM products
WHERE prod_price BETWEEN 3 AND 5;
```

### 3. AND、OR、NOT

- `AND`、`OR`、`NOT` 是用于对过滤条件的逻辑处理指令。
- `AND` 优先级高于 `OR`，为了明确处理顺序，可以使用 `()`。
- `AND` 操作符表示左右条件都要满足。
- `OR` 操作符表示左右条件满足任意一个即可。
- `NOT` 操作符用于否定一个条件。

**AND 示例**

```mysql
SELECT prod_id, prod_name, prod_price
FROM products
WHERE vend_id = 'DLL01' AND prod_price <= 4;
```

**OR 示例**

```mysql
SELECT prod_id, prod_name, prod_price
FROM products
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01';
```

**NOT 示例**

```mysql
SELECT *
FROM products
WHERE prod_price NOT BETWEEN 3 AND 5;
```

### 4. LIKE

- `LIKE` 操作符在 `WHERE` 子句中使用，作用是确定字符串是否匹配模式。
- 只有字段是文本值时才使用 `LIKE`。
- `LIKE` 支持两个通配符匹配选项：`%` 和 `_`。
- 不要滥用通配符，通配符位于开头处匹配会非常慢。
- `%` 表示任何字符出现任意次数。
- `_` 表示任何字符出现一次。

**% 示例**

```mysql
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '%bean bag%';
```

**_ 示例**

```mysql
SELECT prod_id, prod_name, prod_price
FROM products
WHERE prod_name LIKE '__ inch teddy bear';
```

## 四、连接和组合

### 1. 连接（JOIN）

> - 如果一个 `JOIN` 至少有一个公共字段并且它们之间存在关系，则该 `JOIN` 可以在两个或多个表上工作。
>
> - 连接用于连接多个表，使用 `JOIN` 关键字，并且条件语句使用 `ON` 而不是 `WHERE`。
>
> - `JOIN` 保持基表（结构和数据）不变。
>
> - `JOIN` 有两种连接类型：内连接和外连接。
>
> - 内连接又称等值连接，使用 INNER `JOIN` 关键字。在没有条件语句的情况下返回笛卡尔积。
>
> - - 自连接可以看成内连接的一种，只是连接的表是自身而已。
>
> - 自然连接是把同名列通过 = 测试连接起来的，同名列可以有多个。
>
> - 内连接 vs 自然连接
>
> - - 内连接提供连接的列，而自然连接自动连接所有同名列。
>
> - 外连接返回一个表中的所有行，并且仅返回来自次表中满足连接条件的那些行，即两个表中的列是相等的。外连接分为左外连接、右外连接、全外连接（Mysql 不支持）。
>
> - - 左外连接就是保留左表没有关联的行。
>   - 右外连接就是保留右表没有关联的行。
>
> - 连接 vs 子查询
>
> - - 连接可以替换子查询，并且比子查询的效率一般会更快。

<img src="https://gitee.com/myqlrs/cloudimage/raw/master/image/image-20210524235819095.png" alt="image-20210524235819095" style="zoom:80%;" />

**（1）内连接（INNER JOIN）**

```mysql
SELECT vend_name, prod_name, prod_price
FROM vendors INNER JOIN products
ON vendors.vend_id = products.vend_id;
```

**（2）自连接**

```mysql
SELECT c1.cust_id, c1.cust_name, c1.cust_contact
FROM customers c1, customers c2WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones';
```

**（3）自然连接（NATURAL JOIN）**

```mysql
SELECT *
FROM Products
NATURAL JOIN Customers;
```

**（4）左连接（LEFT JOIN）**

```mysql
SELECT customers.cust_id, orders.order_num
FROM customers LEFT JOIN orders
ON customers.cust_id = orders.cust_id;
```

**（5）右连接（RIGHT JOIN）**

```mysql
SELECT customers.cust_id, orders.order_num
FROM customers RIGHT JOIN orders
ON customers.cust_id = orders.cust_id;
```

### 2. 组合（UNION）

- `UNION` 运算符将两个或更多查询的结果组合起来，并生成一个结果集，其中包含来自 `UNION` 中参与查询的提取行。

- `UNION` 基本规则

- - 所有查询的列数和列顺序必须相同。
  - 每个查询中涉及表的列的数据类型必须相同或兼容。
  - 通常返回的列名取自第一个查询。

- 默认会去除相同行，如果需要保留相同行，使用 `UNION ALL`。

- 只能包含一个 `ORDER BY` 子句，并且必须位于语句的最后。

- 应用场景

- - 在一个查询中从不同的表返回结构数据。
  - 对一个表执行多个查询，按一个查询返回数据。

**组合查询**

```mysql
SELECT cust_name, cust_contact, cust_email
FROM customers
WHERE cust_state IN ('IL', 'IN', 'MI')
UNION
SELECT cust_name, cust_contact, cust_email
FROM customers
WHERE cust_name = 'Fun4All';
```

### 3. JOIN vs UNION

- JOIN vs UNION

- - `JOIN` 中连接表的列可能不同，但在 `UNION` 中，所有查询的列数和列顺序必须相同。
  - `UNION` 将查询之后的行放在一起（垂直放置），但 `JOIN` 将查询之后的列放在一起（水平放置），即它构成一个笛卡尔积。

## 五、MySQL函数

> 🔔 注意：不同数据库的函数往往各不相同，因此不可移植。本节主要以 Mysql 的函数为例。

**（1）文本处理**

| 函数                 | 说明                   |
| :------------------- | :--------------------- |
| `LEFT()`、`RIGHT()`  | 左边或者右边的字符     |
| `LOWER()`、`UPPER()` | 转换为小写或者大写     |
| `LTRIM()`、`RTIM()`  | 去除左边或者右边的空格 |
| `LENGTH()`           | 长度                   |
| `SOUNDEX()`          | 转换为语音值           |

其中， **SOUNDEX()** 可以将一个字符串转换为描述其语音表示的字母数字模式。

```mysql
SELECT *
FROM mytable
WHERE SOUNDEX(col1) = SOUNDEX('apple')
```

**（2）日期和时间处理**

- 日期格式：`YYYY-MM-DD`
- 时间格式：`HH:MM:SS`

| 函 数           | 说 明                          |
| :-------------- | :----------------------------- |
| `AddDate()`     | 增加一个日期（天、周等）       |
| `AddTime()`     | 增加一个时间（时、分等）       |
| `CurDate()`     | 返回当前日期                   |
| `CurTime()`     | 返回当前时间                   |
| `Date()`        | 返回日期时间的日期部分         |
| `DateDiff()`    | 计算两个日期之差               |
| `Date_Add()`    | 高度灵活的日期运算函数         |
| `Date_Format()` | 返回一个格式化的日期或时间串   |
| `Day()`         | 返回一个日期的天数部分         |
| `DayOfWeek()`   | 对于一个日期，返回对应的星期几 |
| `Hour()`        | 返回一个时间的小时部分         |
| `Minute()`      | 返回一个时间的分钟部分         |
| `Month()`       | 返回一个日期的月份部分         |
| `Now()`         | 返回当前日期和时间             |
| `Second()`      | 返回一个时间的秒部分           |
| `Time()`        | 返回一个日期时间的时间部分     |
| `Year()`        | 返回一个日期的年份部分         |

```mysql
mysql> SELECT NOW();
2018-4-14 20:25:11
```

**（3）数值处理**

| 函数   | 说明   |
| :----- | :----- |
| SIN()  | 正弦   |
| COS()  | 余弦   |
| TAN()  | 正切   |
| ABS()  | 绝对值 |
| SQRT() | 平方根 |
| MOD()  | 余数   |
| EXP()  | 指数   |
| PI()   | 圆周率 |
| RAND() | 随机数 |

**（4）汇总**

| 函 数     | 说 明            |
| :-------- | :--------------- |
| `AVG()`   | 返回某列的平均值 |
| `COUNT()` | 返回某列的行数   |
| `MAX()`   | 返回某列的最大值 |
| `MIN()`   | 返回某列的最小值 |
| `SUM()`   | 返回某列值之和   |

`AVG()` 会忽略 NULL 行。

使用 DISTINCT 可以让汇总函数值汇总不同的值。

```mysql
SELECT AVG(DISTINCT col1) AS avg_col
FROM mytable
```

## 六、排序和分组

**（1）ORDER BY**

- `ORDER BY` 用于对结果集进行排序。

- - `ASC` ：升序（默认）
  - `DESC` ：降序

- 可以按多个列进行排序，并且为每个列指定不同的排序方式

**指定多个列的排序方向**

```mysql
SELECT * FROM products
ORDER BY prod_price DESC, prod_name ASC;
```

**（2）GROUP BY**

- `GROUP BY` 子句将记录分组到汇总行中。
- `GROUP BY` 为每个组返回一个记录。
- `GROUP BY` 通常还涉及聚合：COUNT，MAX，SUM，AVG 等。
- `GROUP BY` 可以按一列或多列进行分组。
- `GROUP BY` 按分组字段进行排序后，`ORDER BY` 可以以汇总字段来进行排序。

**分组**

```mysql
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name;
```

**分组后排序**

```mysql
SELECT cust_name, COUNT(cust_address) AS addr_num
FROM Customers GROUP BY cust_name
ORDER BY cust_name DESC;
```

**（3）HAVING**

- `HAVING` 用于对汇总的 `GROUP BY` 结果进行过滤。

- `HAVING` 要求存在一个 `GROUP BY` 子句。

- `WHERE` 和 `HAVING` 可以在相同的查询中。

- `HAVING` vs `WHERE`

- - `WHERE` 和 `HAVING` 都是用于过滤。
  - `HAVING` 适用于汇总的组记录；而 WHERE 适用于单个记录。

**使用 WHERE 和 HAVING 过滤数据**

```mysql
SELECT cust_name, COUNT(*) AS num
FROM Customers
WHERE cust_email IS NOT NULL
GROUP BY cust_name
HAVING COUNT(*) >= 1;
```

（以下为 DDL 语句用法）

## 七、数据定义

> DDL 的主要功能是定义数据库对象（如：数据库、数据表、视图、索引等）。

### 1. 数据库（DATABASE）

创建数据库

```mysql
CREATE DATABASE test;
```

删除数据库

```mysql
DROP DATABASE test;
```

选择数据库

```mysql
USE test;
```

### 2. 数据表（TABLE）

**（1）创建数据表**

普通创建

```mysql
CREATE TABLE user (
  id int(10) unsigned NOT NULL COMMENT 'Id',
  username varchar(64) NOT NULL DEFAULT 'default' COMMENT '用户名',
  password varchar(64) NOT NULL DEFAULT 'default' COMMENT '密码',
  email varchar(64) NOT NULL DEFAULT 'default' COMMENT '邮箱'
) COMMENT='用户表';
```

根据已有的表创建新表

```mysql
CREATE TABLE vip_user AS
SELECT * FROM user;
```

**（2）删除数据表**

```mysql
DROP TABLE user;
```

**（3）修改数据表**

添加列

```mysql
ALTER TABLE user
ADD age int(3);
```

删除列

```mysql
ALTER TABLE user
DROP COLUMN age;
```

修改列

```mysql
ALTER TABLE `user`
MODIFY COLUMN age tinyint;
```

添加主键

```mysql
ALTER TABLE user
ADD PRIMARY KEY (id);
```

删除主键

```mysql
ALTER TABLE user
DROP PRIMARY KEY;
```

### 3. 视图（VIEW）

> - 定义
>
> - - 视图是基于 SQL 语句的结果集的可视化的表。
>   - 视图是虚拟的表，本身不包含数据，也就不能对其进行索引操作。对视图的操作和对普通表的操作一样。
>
> - 作用
>
> - - 简化复杂的 SQL 操作，比如复杂的联结；
>   - 只使用实际表的一部分数据；
>   - 通过只给用户访问视图的权限，保证数据的安全性；
>   - 更改数据格式和表示。

**（1）创建视图**

```mysql
CREATE VIEW top_10_user_view AS
SELECT id, username
FROM user
WHERE id < 10;
```

**（2）删除视图**

```mysql
DROP VIEW top_10_user_view;
```

### 4. 索引（INDEX）

> - 作用：
>
> - - 通过索引可以更加快速高效地查询数据。
>   - 用户无法看到索引，它们只能被用来加速查询。
>
> - 注意：
>
> - - 更新一个包含索引的表需要比更新一个没有索引的表花费更多的时间，这是由于索引本身也需要更新。因此，理想的做法是仅仅在常常被搜索的列（以及表）上面创建索引。
>
> - 唯一索引：
>
> - - 唯一索引表明此索引的每一个索引值只对应唯一的数据记录。

创建索引

```mysql
CREATE INDEX user_index
ON user (id);
```

创建唯一索引

```mysql
CREATE UNIQUE INDEX user_index
ON user (id);
```

删除索引

```mysql
ALTER TABLE user
DROP INDEX user_index;
```

### 5. 约束

> SQL 约束用于规定表中的数据规则。

- 如果存在违反约束的数据行为，行为会被约束终止。

- 约束可以在创建表时规定（通过 CREATE TABLE 语句），或者在表创建之后规定（通过 ALTER TABLE 语句）。

- 约束类型

- - `NOT NULL` - 指示某列不能存储 NULL 值。
  - `UNIQUE` - 保证某列的每行必须有唯一的值。
  - `PRIMARY KEY` - NOT NULL 和 UNIQUE 的结合。确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录。
  - `FOREIGN KEY` - 保证一个表中的数据匹配另一个表中的值的参照完整性。
  - `CHECK` - 保证列中的值符合指定的条件。
  - `DEFAULT` - 规定没有给列赋值时的默认值。

创建表时使用约束条件：

```mysql
CREATE TABLE Users (
  Id INT(10) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '自增Id',
  Username VARCHAR(64) NOT NULL UNIQUE DEFAULT 'default' COMMENT '用户名',
  Password VARCHAR(64) NOT NULL DEFAULT 'default' COMMENT '密码',
  Email VARCHAR(64) NOT NULL DEFAULT 'default' COMMENT '邮箱地址',
  Enabled TINYINT(4) DEFAULT NULL COMMENT '是否有效',
  PRIMARY KEY (Id)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COMMENT='用户表';
```

（以下为 TCL 语句用法）

## 八、事务处理

> - 不能回退 SELECT 语句，回退 SELECT 语句也没意义；也不能回退 CREATE 和 DROP 语句。
>
> - **MySQL 默认是隐式提交**，每执行一条语句就把这条语句当成一个事务然后进行提交。当出现 `START TRANSACTION` 语句时，会关闭隐式提交；当 `COMMIT` 或 `ROLLBACK` 语句执行后，事务会自动关闭，重新恢复隐式提交。
>
> - 通过 `set autocommit=0` 可以取消自动提交，直到 `set autocommit=1` 才会提交；autocommit 标记是针对每个连接而不是针对服务器的。
>
> - 指令
>
> - - `START TRANSACTION` - 指令用于标记事务的起始点。
>   - `SAVEPOINT` - 指令用于创建保留点。
>   - `ROLLBACK TO` - 指令用于回滚到指定的保留点；如果没有设置保留点，则回退到 `START TRANSACTION` 语句处。
>   - `COMMIT` - 提交事务。

```mysql
-- 开始事务
START TRANSACTION;

-- 插入操作 AINSERT INTO `user`
VALUES (1, 'root1', 'root1', 'xxxx@163.com');

-- 创建保留点 updateA
SAVEPOINT updateA;

-- 插入操作 B
INSERT INTO `user`
VALUES (2, 'root2', 'root2', 'xxxx@163.com');

-- 回滚到保留点 updateA
ROLLBACK TO updateA;

-- 提交事务，只有操作 A 生效
COMMIT;
```

（以下为 DCL 语句用法）

## 九、权限控制

> - GRANT 和 REVOKE 可在几个层次上控制访问权限：
>
> - - 整个服务器，使用 GRANT ALL 和 REVOKE ALL；
>   - 整个数据库，使用 ON database.*；
>   - 特定的表，使用 ON database.table；
>   - 特定的列；
>   - 特定的存储过程。
>
> - 新创建的账户没有任何权限。
>
> - 账户用 username@host 的形式定义，username@% 使用的是默认主机名。
>
> - MySQL 的账户信息保存在 mysql 这个数据库中。
>
>   ```mysql
>   USE mysql;
>   SELECT user FROM user;
>   复制代码
>   ```

创建账户

```mysql
CREATE USER myuser IDENTIFIED BY 'mypassword';
```

修改账户名

```mysql
UPDATE user SET user='newuser' WHERE user='myuser';
FLUSH PRIVILEGES;
```

删除账户

```mysql
DROP USER myuser;
```

查看权限

```mysql
SHOW GRANTS FOR myuser;
```

授予权限

```mysql
GRANT SELECT, INSERT ON *.* TO myuser;
```

删除权限

```mysql
REVOKE SELECT, INSERT ON *.* FROM myuser;
```

更改密码

```mysql
SET PASSWORD FOR myuser = 'mypass';
```

## 十、存储过程

> - 存储过程可以看成是对一系列 SQL 操作的批处理；
>
> - 使用存储过程的好处
>
> - - 代码封装，保证了一定的安全性；
>   - 代码复用；
>   - 由于是预先编译，因此具有很高的性能。
>
> - 创建存储过程
>
> - - 命令行中创建存储过程需要自定义分隔符，因为命令行是以 `;` 为结束符，而存储过程中也包含了分号，因此会错误把这部分分号当成是结束符，造成语法错误。
>   - 包含 in、out 和 inout 三种参数。
>   - 给变量赋值都需要用 select into 语句。
>   - 每次只能给一个变量赋值，不支持集合的操作。

**1. 创建存储过程**

```mysql
DROP PROCEDURE IF EXISTS `proc_adder`;
DELIMITER ;;
CREATE DEFINER=`root`@`localhost` PROCEDURE `proc_adder`(IN a int, IN b int, OUT sum int)
BEGIN
    DECLARE c int;
    if a is null then set a = 0;
    end if;

    if b is null then set b = 0;
    end if;

    set sum  = a + b;
END
;;
DELIMITER ;
```

**2. 使用存储过程**

```mysql
set @b=5;
call proc_adder(2,@b,@s);
select @s as sum;
```

## 十一、游标

> - 游标（cursor）是一个存储在 DBMS 服务器上的数据库查询，它不是一条 SELECT 语句，而是被该语句检索出来的结果集。
>
> - 在存储过程中使用游标可以对一个结果集进行移动遍历。
>
> - 游标主要用于交互式应用，其中用户需要对数据集中的任意行进行浏览和修改。
>
> - 使用游标的四个步骤：
>
> - - 声明游标，这个过程没有实际检索出数据；
>   - 打开游标；
>   - 取出数据；
>   - 关闭游标；

```mysql
DELIMITER $
CREATE  PROCEDURE getTotal()
BEGIN
    DECLARE total INT;
    -- 创建接收游标数据的变量
    DECLARE sid INT;
    DECLARE sname VARCHAR(10);
    -- 创建总数变量
    DECLARE sage INT;
    -- 创建结束标志变量    DECLARE done INT DEFAULT false;
    -- 创建游标
    DECLARE cur CURSOR FOR SELECT id,name,age from cursor_table where age>30;
    -- 指定游标循环结束时的返回值
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = true;
    SET total = 0;
    OPEN cur;
    FETCH cur INTO sid, sname, sage;
    WHILE(NOT done)
    DO
        SET total = total + 1;
        FETCH cur INTO sid, sname, sage;
    END WHILE;

    CLOSE cur;
    SELECT total;
END $
DELIMITER ;

-- 调用存储过程
call getTotal();
```

## 十二、触发器

> 触发器是一种与表操作有关的数据库对象，当触发器所在表上出现指定事件时，将调用该对象，即表的操作事件触发表上的触发器的执行。

可以使用触发器来进行审计跟踪，把修改记录到另外一张表中。

MySQL 不允许在触发器中使用 CALL 语句 ，也就是不能调用存储过程。

**`BEGIN` 和 `END`**

当触发器的触发条件满足时，将会执行 `BEGIN` 和 `END` 之间的触发器执行动作。

> 🔔 注意：在 MySQL 中，分号 `;` 是语句结束的标识符，遇到分号表示该段语句已经结束，MySQL 可以开始执行了。因此，解释器遇到触发器执行动作中的分号后就开始执行，然后会报错，因为没有找到和 BEGIN 匹配的 END。
>
> 这时就会用到 `DELIMITER` 命令（DELIMITER 是定界符，分隔符的意思）。它是一条命令，不需要语句结束标识，语法为：`DELIMITER new_delemiter`。`new_delemiter` 可以设为 1 个或多个长度的符号，默认的是分号 `;`，我们可以把它修改为其他符号，如 `$` - `DELIMITER $` 。在这之后的语句，以分号结束，解释器不会有什么反应，只有遇到了 `$`，才认为是语句结束。注意，使用完之后，我们还应该记得把它给修改回来。

**`NEW` 和 `OLD`**

- MySQL 中定义了 `NEW` 和 `OLD` 关键字，用来表示触发器的所在表中，触发了触发器的那一行数据。
- 在 `INSERT` 型触发器中，`NEW` 用来表示将要（`BEFORE`）或已经（`AFTER`）插入的新数据；
- 在 `UPDATE` 型触发器中，`OLD` 用来表示将要或已经被修改的原数据，`NEW` 用来表示将要或已经修改为的新数据；
- 在 `DELETE` 型触发器中，`OLD` 用来表示将要或已经被删除的原数据；
- 使用方法： `NEW.columnName` （columnName 为相应数据表某一列名）

**1. 创建触发器**

> 提示：为了理解触发器的要点，有必要先了解一下创建触发器的指令。

`CREATE TRIGGER` 指令用于创建触发器。

语法：

```mysql
CREATE TRIGGER trigger_name
trigger_time
trigger_event
ON table_name
FOR EACH ROW
BEGIN
  trigger_statements
END;
```

说明：

- trigger_name：触发器名
- trigger_time: 触发器的触发时机。取值为 `BEFORE` 或 `AFTER`。
- trigger_event: 触发器的监听事件。取值为 `INSERT`、`UPDATE` 或 `DELETE`。
- table_name: 触发器的监听目标。指定在哪张表上建立触发器。
- FOR EACH ROW: 行级监视，Mysql 固定写法，其他 DBMS 不同。
- trigger_statements: 触发器执行动作。是一条或多条 SQL 语句的列表，列表内的每条语句都必须用分号 `;` 来结尾。

示例：

```mysql
DELIMITER $
CREATE TRIGGER `trigger_insert_user`
AFTER INSERT ON `user`
FOR EACH ROW
BEGIN
    INSERT INTO `user_history`(user_id, operate_type, operate_time)
    VALUES (NEW.id, 'add a user',  now());
END $
DELIMITER ;
```

**2. 查看触发器**

```sql
SHOW TRIGGERS;
```

**3. 删除触发器**

```mysql
DROP TRIGGER IF EXISTS trigger_insert_user;
```

## 十三、MySql面试题

### 1.索引

**1. 什么是索引?**

索引是一种数据结构,可以帮助我们快速的进行数据的查找.

**2. 索引是个什么样的数据结构呢?**

索引的数据结构和具体存储引擎的实现有关, 在MySQL中使用较多的索引有Hash索引,B+树索引等,而我们经常使用的InnoDB存储引擎的默认索引实现为:B+树索引.

**3. Hash索引和B+树所有有什么区别或者说优劣呢?**

首先要知道Hash索引和B+树索引的底层实现原理:

hash索引底层就是hash表,进行查找时,调用一次hash函数就可以获取到相应的键值,之后进行回表查询获得实际数据.B+树底层实现是多路平衡查找树.对于每一次的查询都是从根节点出发,查找到叶子节点方可以获得所查键值,然后根据查询判断是否需要回表查询数据.



那么可以看出他们有以下的不同:

- hash索引进行等值查询更快(一般情况下),但是却无法进行范围查询.

因为在hash索引中经过hash函数建立索引之后,索引的顺序与原顺序无法保持一致,不能支持范围查询.而B+树的的所有节点皆遵循(左节点小于父节点,右节点大于父节点,多叉树也类似),天然支持范围.

- hash索引不支持使用索引进行排序,原理同上.
- hash索引不支持模糊查询以及多列索引的最左前缀匹配.原理也是因为hash函数的不可预测.**AAAA**和**AAAAB**的索引没有相关性.
- hash索引任何时候都避免不了回表查询数据,而B+树在符合某些条件(聚簇索引,覆盖索引等)的时候可以只通过索引完成查询.
- hash索引虽然在等值查询上较快,但是不稳定.性能不可预测,当某个键值存在大量重复的时候,发生hash碰撞,此时效率可能极差.而B+树的查询效率比较稳定,对于所有的查询都是从根节点到叶子节点,且树的高度较低.

因此,在大多数情况下,直接选择B+树索引可以获得稳定且较好的查询速度.而不需要使用hash索引.

**4. 上面提到了B+树在满足聚簇索引和覆盖索引的时候不需要回表查询数据,什么是聚簇索引?**

在B+树的索引中,叶子节点可能存储了当前的key值,也可能存储了当前的key值以及整行的数据,这就是聚簇索引和非聚簇索引. 在InnoDB中,只有主键索引是聚簇索引,如果没有主键,则挑选一个唯一键建立聚簇索引.如果没有唯一键,则隐式的生成一个键来建立聚簇索引.

当查询使用聚簇索引时,在对应的叶子节点,可以获取到整行数据,因此不用再次进行回表查询.

**5. 非聚簇索引一定会回表查询吗?**

不一定,这涉及到查询语句所要求的字段是否全部命中了索引,如果全部命中了索引,那么就不必再进行回表查询.

举个简单的例子,假设我们在员工表的年龄上建立了索引,那么当进行`select age from employee where age < 20`的查询时,在索引的叶子节点上,已经包含了age信息,不会再次进行回表查询.

**6. 在建立索引的时候,都有哪些需要考虑的因素呢?**

建立索引的时候一般要考虑到字段的使用频率,经常作为条件进行查询的字段比较适合.如果需要建立联合索引的话,还需要考虑联合索引中的顺序.此外也要考虑其他方面,比如防止过多的所有对表造成太大的压力.这些都和实际的表结构以及查询方式有关.

**7. 联合索引是什么?为什么需要注意联合索引中的顺序?**

MySQL可以使用多个字段同时建立一个索引,叫做联合索引.在联合索引中,如果想要命中索引,需要按照建立索引时的字段顺序挨个使用,否则无法命中索引.

具体原因为:

MySQL使用索引时需要索引有序,假设现在建立了"name,age,school"的联合索引,那么索引的排序为: 先按照name排序,如果name相同,则按照age排序,如果age的值也相等,则按照school进行排序.

当进行查询时,此时索引仅仅按照name严格有序,因此必须首先使用name字段进行等值查询,之后对于匹配到的列而言,其按照age字段严格有序,此时可以使用age字段用做索引查找,,,以此类推.因此在建立联合索引的时候应该注意索引列的顺序,一般情况下,将查询需求频繁或者字段选择性高的列放在前面.此外可以根据特例的查询或者表结构进行单独的调整.

**8. 创建的索引有没有被使用到?或者说怎么才可以知道这条语句运行很慢的原因?**

MySQL提供了explain命令来查看语句的执行计划,MySQL在执行某个语句之前,会将该语句过一遍查询优化器,之后会拿到对语句的分析,也就是执行计划,其中包含了许多信息.可以通过其中和索引有关的信息来分析是否命中了索引,例如possilbe_key,key,key_len等字段,分别说明了此语句可能会使用的索引,实际使用的索引以及使用的索引长度.

**9. 那么在哪些情况下会发生针对该列创建了索引但是在查询的时候并没有使用呢?**

- 使用不等于查询,
- 列参与了数学运算或者函数
- 在字符串like时左边是通配符.类似于'%aaa'.
- 当mysql分析全表扫描比使用索引快的时候不使用索引.
- 当使用联合索引,前面一个条件为范围查询,后面的即使符合最左前缀原则,也无法使用索引.

以上情况,MySQL无法使用索引.

### **2. 事务相关**

**1. 什么是事务?**

理解什么是事务最经典的就是转账的栗子,相信大家也都了解,这里就不再说一遍了.

事务是一系列的操作,他们要符合ACID特性.最常见的理解就是:事务中的操作要么全部成功,要么全部失败.但是只是这样还不够的.

**2. ACID是什么?可以详细说一下吗?**

A=Atomicity

原子性,就是上面说的,要么全部成功,要么全部失败.不可能只执行一部分操作.

C=Consistency

系统(数据库)总是从一个一致性的状态转移到另一个一致性的状态,不会存在中间状态.

I=Isolation

隔离性: 通常来说:一个事务在完全提交之前,对其他事务是不可见的.注意前面的通常来说加了红色,意味着有例外情况.

D=Durability

持久性,一旦事务提交,那么就永远是这样子了,哪怕系统崩溃也不会影响到这个事务的结果.

**3. 同时有多个事务在进行会怎么样呢?**

多事务的并发进行一般会造成以下几个问题:

- 脏读: A事务读取到了B事务未提交的内容,而B事务后面进行了回滚.
- 不可重复读: 当设置A事务只能读取B事务已经提交的部分,会造成在A事务内的两次查询,结果竟然不一样,因为在此期间B事务进行了提交操作.
- 幻读: A事务读取了一个范围的内容,而同时B事务在此期间插入了一条数据.造成"幻觉".

**4. 怎么解决这些问题呢?MySQL的事务隔离级别了解吗?**

MySQL的四种隔离级别如下:

- 未提交读(READ UNCOMMITTED)

这就是上面所说的例外情况了,这个隔离级别下,其他事务可以看到本事务没有提交的部分修改.因此会造成脏读的问题(读取到了其他事务未提交的部分,而之后该事务进行了回滚).

这个级别的性能没有足够大的优势,但是又有很多的问题,因此很少使用.

- 已提交读(READ COMMITTED)

其他事务只能读取到本事务已经提交的部分.这个隔离级别有 不可重复读的问题,在同一个事务内的两次读取,拿到的结果竟然不一样,因为另外一个事务对数据进行了修改.

- REPEATABLE READ(可重复读)

可重复读隔离级别解决了上面不可重复读的问题(看名字也知道),但是仍然有一个新问题,就是 幻读,当你读取id> 10 的数据行时,对涉及到的所有行加上了读锁,此时例外一个事务新插入了一条id=11的数据,因为是新插入的,所以不会触发上面的锁的排斥,那么进行本事务进行下一次的查询时会发现有一条id=11的数据,而上次的查询操作并没有获取到,再进行插入就会有主键冲突的问题.

- SERIALIZABLE(可串行化)

这是最高的隔离级别,可以解决上面提到的所有问题,因为他强制将所以的操作串行执行,这会导致并发性能极速下降,因此也不是很常用.

**5. Innodb使用的是哪种隔离级别呢?**

InnoDB默认使用的是可重复读隔离级别.

**6. 对MySQL的锁了解吗?**

当数据库有并发事务的时候,可能会产生数据的不一致,这时候需要一些机制来保证访问的次序,锁机制就是这样的一个机制.

就像酒店的房间,如果大家随意进出,就会出现多人抢夺同一个房间的情况,而在房间上装上锁,申请到钥匙的人才可以入住并且将房间锁起来,其他人只有等他使用完毕才可以再次使用.

**7. MySQL都有哪些锁呢?像上面那样子进行锁定岂不是有点阻碍并发效率了?**

从锁的类别上来讲,有共享锁和排他锁.

共享锁: 又叫做读锁. 当用户要进行数据的读取时,对数据加上共享锁.共享锁可以同时加上多个.

排他锁: 又叫做写锁. 当用户要进行数据的写入时,对数据加上排他锁.排他锁只可以加一个,他和其他的排他锁,共享锁都相斥.

用上面的例子来说就是用户的行为有两种,一种是来看房,多个用户一起看房是可以接受的. 一种是真正的入住一晚,在这期间,无论是想入住的还是想看房的都不可以.

锁的粒度取决于具体的存储引擎,InnoDB实现了行级锁,页级锁,表级锁.

他们的加锁开销从大大小,并发能力也是从大到小.

### **3. 表结构设计**

**1. 为什么要尽量设定一个主键?**

主键是数据库确保数据行在整张表唯一性的保障,即使业务上本张表没有主键,也建议添加一个自增长的ID列作为主键.设定了主键之后,在后续的删改查的时候可能更加快速以及确保操作数据范围安全.

**2. 主键使用自增ID还是UUID?**

推荐使用自增ID,不要使用UUID.

因为在InnoDB存储引擎中,主键索引是作为聚簇索引存在的,也就是说,主键索引的B+树叶子节点上存储了主键索引以及全部的数据(按照顺序),如果主键索引是自增ID,那么只需要不断向后排列即可,如果是UUID,由于到来的ID与原来的大小不确定,会造成非常多的数据插入,数据移动,然后导致产生很多的内存碎片,进而造成插入性能的下降.

总之,在数据量大一些的情况下,用自增主键性能会好一些.

*图片来源于《高性能MySQL》: 其中默认后缀为使用自增ID,**`_uuid`为使用UUID为主键的测试,测试了插入100w行和300w行的性能.*
<img src="https://gitee.com/myqlrs/cloudimage/raw/master/image/image-20210526135028654.png" alt="image-20210526135028654" style="zoom:80%;" />
关于主键是聚簇索引,如果没有主键,InnoDB会选择一个唯一键来作为聚簇索引,如果没有唯一键,会生成一个隐式的主键.

> If you define a PRIMARY KEY on your table, InnoDB uses it as the clustered index.

> If you do not define a PRIMARY KEY for your table, MySQL picks the first UNIQUE index that has only NOT NULL columns as the primary key and InnoDB uses it as the clustered index.

**3. 字段为什么要求定义为not null?**

MySQL官网这样介绍:

> NULL columns require additional space in the rowto record whether their values are NULL. For MyISAM tables, each NULL columntakes one bit extra, rounded up to the nearest byte.

null值会占用更多的字节,且会在程序中造成很多与预期不符的情况.

**4. 如果要存储用户的密码散列,应该使用什么字段进行存储?**

密码散列,盐,用户身份证号等固定长度的字符串应该使用char而不是varchar来存储,这样可以节省空间且提高检索效率.

### **4. 存储引擎相关**

**1. MySQL支持哪些存储引擎?**

MySQL支持多种存储引擎,比如InnoDB,MyISAM,Memory,Archive等等.在大多数的情况下,直接选择使用InnoDB引擎都是最合适的,InnoDB也是MySQL的默认存储引擎.

1. InnoDB和MyISAM有什么区别?

- InnoDB支持事物，而MyISAM不支持事物
- InnoDB支持行级锁，而MyISAM支持表级锁
- InnoDB支持MVCC, 而MyISAM不支持
- InnoDB支持外键，而MyISAM不支持
- InnoDB不支持全文索引，而MyISAM支持。

### **5. 零散问题**

**1. MySQL中的varchar和char有什么区别.**

char是一个定长字段,假如申请了`char(10)`的空间,那么无论实际存储多少内容.该字段都占用10个字符,而varchar是变长的,也就是说申请的只是最大长度,占用的空间为实际字符长度+1,最后一个字符存储使用了多长的空间.

在检索效率上来讲,char > varchar,因此在使用中,如果确定某个字段的值的长度,可以使用char,否则应该尽量使用varchar.例如存储用户MD5加密后的密码,则应该使用char.

**2. varchar(10)和int(10)代表什么含义?**

varchar的10代表了申请的空间长度,也是可以存储的数据的最大长度,而int的10只是代表了展示的长度,不足10位以0填充.也就是说,int(1)和int(10)所能存储的数字大小以及占用的空间都是相同的,只是在展示时按照长度展示.

**3. MySQL的binlog有几种录入格式?分别有什么区别?**

有三种格式,statement,row和mixed.

- statement模式下,记录单元为语句.即每一个sql造成的影响会记录.由于sql的执行是有上下文的,因此在保存的时候需要保存相关的信息,同时还有一些使用了函数之类的语句无法被记录复制.
- row级别下,记录单元为每一行的改动,基本是可以全部记下来但是由于很多操作,会导致大量行的改动(比如alter table),因此这种模式的文件保存的信息太多,日志量太大.
- mixed. 一种折中的方案,普通操作使用statement记录,当无法使用statement的时候使用row.

此外,新版的MySQL中对row级别也做了一些优化,当表结构发生变化的时候,会记录语句而不是逐行记录.

**4. 超大分页怎么处理?**

超大的分页一般从两个方向上来解决.

- 数据库层面,这也是我们主要集中关注的(虽然收效没那么大),类似于`select * from table where age > 20 limit 1000000,10`这种查询其实也是有可以优化的余地的. 这条语句需要load1000000数据然后基本上全部丢弃,只取10条当然比较慢. 当时我们可以修改为`select * from table where id in (select id from table where age > 20 limit 1000000,10)`.这样虽然也load了一百万的数据,但是由于索引覆盖,要查询的所有字段都在索引中,所以速度会很快. 同时如果ID连续的好,我们还可以`select * from table where id > 1000000 limit 10`,效率也是不错的,优化的可能性有许多种,但是核心思想都一样,就是减少load的数据.
- 从需求的角度减少这种请求....主要是不做类似的需求(直接跳转到几百万页之后的具体某一页.只允许逐页查看或者按照给定的路线走,这样可预测,可缓存)以及防止ID泄漏且连续被人恶意攻击.

解决超大分页,其实主要是靠缓存,可预测性的提前查到内容,缓存至redis等k-V数据库中,直接返回即可.

在阿里巴巴《Java开发手册》中,对超大分页的解决办法是类似于上面提到的第一种.

【推荐】利用延迟关联或者子查询优化超多分页场景。
说明: MysQL并不是跳过offset行,而是取offset+N行,然后返回放弃前offset行,返回N行,那当offset特别大的时候,效率就非常的低下,要么控制返回的总页数,要么对超过特定阈值的页数进行SQL改写。
正例:先快速定位需要获取的id段,然后再关联:
`SELECT a.* FROM表1 a,(select id from 表1 where 条件 LIMIT 100000,20) b where a.id=bid`

**5. 关心过业务系统里面的sql耗时吗?统计过慢查询吗?对慢查询都怎么优化过?**

在业务系统中,除了使用主键进行的查询,其他的我都会在测试库上测试其耗时,慢查询的统计主要由运维在做,会定期将业务中的慢查询反馈给我们.

慢查询的优化首先要搞明白慢的原因是什么? 是查询条件没有命中索引?是load了不需要的数据列?还是数据量太大?

所以优化也是针对这三个方向来的,

- 首先分析语句,看看是否load了额外的数据,可能是查询了多余的行并且抛弃掉了,可能是加载了许多结果中并不需要的列,对语句进行分析以及重写.
- 分析语句的执行计划,然后获得其使用索引的情况,之后修改语句或者修改索引,使得语句可以尽可能的命中索引.
- 如果对语句的优化已经无法进行,可以考虑表中的数据量是否太大,如果是的话可以进行横向或者纵向的分表.

**6. 上面提到横向分表和纵向分表,可以分别举一个适合他们的例子吗?**

横向分表是按行分表.假设我们有一张用户表,主键是自增ID且同时是用户的ID.数据量较大,有1亿多条,那么此时放在一张表里的查询效果就不太理想.我们可以根据主键ID进行分表,无论是按尾号分,或者按ID的区间分都是可以的. 假设按照尾号0-99分为100个表,那么每张表中的数据就仅有100w.这时的查询效率无疑是可以满足要求的.

纵向分表是按列分表.假设我们现在有一张文章表.包含字段`id-摘要-内容`.而系统中的展示形式是刷新出一个列表,列表中仅包含标题和摘要,当用户点击某篇文章进入详情时才需要正文内容.此时,如果数据量大,将内容这个很大且不经常使用的列放在一起会拖慢原表的查询速度.我们可以将上面的表分为两张.`id-摘要`,`id-内容`.当用户点击详情,那主键再来取一次内容即可.而增加的存储量只是很小的主键字段.代价很小.

当然,分表其实和业务的关联度很高,在分表之前一定要做好调研以及benchmark.不要按照自己的猜想盲目操作.

**7. 什么是存储过程？有哪些优缺点？**

存储过程是一些预编译的SQL语句。1、更加直白的理解：存储过程可以说是一个记录集，它是由一些T-SQL语句组成的代码块，这些T-SQL语句代码像一个方法一样实现一些功能（对单表或多表的增删改查），然后再给这个代码块取一个名字，在用到这个功能的时候调用他就行了。2、存储过程是一个预编译的代码块，执行效率比较高,一个存储过程替代大量T_SQL语句 ，可以降低网络通信量，提高通信速率,可以一定程度上确保数据安全

但是,在互联网项目中,其实是不太推荐存储过程的,比较出名的就是阿里的《Java开发手册》中禁止使用存储过程,我个人的理解是,在互联网项目中,迭代太快,项目的生命周期也比较短,人员流动相比于传统的项目也更加频繁,在这样的情况下,存储过程的管理确实是没有那么方便,同时,复用性也没有写在服务层那么好.

**8. 说一说三个范式**

第一范式: 每个列都不可以再拆分.第二范式: 非主键列完全依赖于主键,而不能是依赖于主键的一部分.第三范式: 非主键列只依赖于主键,不依赖于其他非主键.

在设计数据库结构的时候,要尽量遵守三范式,如果不遵守,必须有足够的理由.比如性能. 事实上我们经常会为了性能而妥协数据库的设计.

**9. MyBatis中的#和$有什么区别?**

乱入了一个奇怪的问题.....我只是想单独记录一下这个问题,因为出现频率太高了.

\# 会将传入的内容当做字符串,而$会直接将传入值拼接在sql语句中.

所以#可以在一定程度上预防sql注入攻击.
