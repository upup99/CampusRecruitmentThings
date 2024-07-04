## SQL语言

> 数据库语言是java项目开发必不可少的一部分。就我目前的理解：它这不就是高级Excel嘛？诚然，它的设计理论、流程比较复杂。对于关系型数据库如何工作的往后再谈。

### 0、复习内容

1. SQL语法基础
2. SQL语句进阶

### 1、SQL语法基础

#### 1.1 基础

- 主键的值不允许修改，也不可复用
- SQL语句不区分大小写（但是数据库表名、列名和值是否区分依赖于具体的DBMS以及配置）
- 三种注释方法：#、--、/\*注释1\*/
- 数据库创建与使用

```sql
CREATE DATABASE test;
USE test;
```

#### 1.2 创建表

```sql
CREATE TABLE myFirstTable(
	id INT NOT NULL AUTO_INCREMENT, #NOT NULL非空，后接限定值
	col INT NOT NULL DEFAULT 1,
	col2 VARCHAR(45) NULL,
	col3 DATE NULL,
	PRIMARY KEY(`id`));
```

![image-20220413100145104](D:\微信推文\images\image-20220413100145104.png)

#### 1.3 修改表

```sql
-- 添加列	
ALTER TABLE myFirstTable
	ADD col4 CHAR(20)
	AFTER col; # 在col后添加col4
-- 删除列
ALTER TABLE myFirstTable
	DROP COLUMN col4; 
-- 删除表
DROP TABLE myFirstTable;
```

#### 1.4 插入

```sql
-- 普通插入
INSERT INTO myFirstTable(col, col2)
	VALUES(100, 200);
-- 插入检索出来的数据
INSERT INTO myFirstTable(col, col2)
	SELECT col, col2
	FROM myFirstTable;
-- 将一个表的内容插入到一个新表
CREATE TABLE newtable AS
	SELECT * FROM myFirstTable; # 相当于复制表
```

![image-20220413102454509](D:\微信推文\images\image-20220413102454509.png)

#### 1.5 更新

```sql
-- 更新表
UPDATE myFirstTable
	SET col = 400
	WHERE id = 2; # 定语从句
```

![image-20220413102847322](D:\微信推文\images\image-20220413102847322.png)

#### 1.6 删除

```sql
-- 删除数据
DELETE FROM myFirstTable
	WHERE id = 1;
-- 清空表
TRUNCATE TABLE myFirstTable; # 删表跑路嘿嘿
```

![image-20220413103548446](D:\微信推文\images\image-20220413103548446.png)

#### 1.7 查询

```sql
-- 查询表
-- 1. distinct：相同值只会出现一次。它作用于所有列，也就是说所有列的值都相同才算相同。
SELECT DISTINCT col, col2
	FROM myFirstTable;
-- 2.limit：限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。
SELECT * FROM myFirstTable LIMIT 5; #返回前5行

SELECT * FROM myFirstTable 
	LIMIT 2, 3; # 返回3~5行
```



![image-20220413104320003](D:\微信推文\images\image-20220413104320003.png)

![](D:\微信推文\images\image-20220413104235031.png)

#### 1.8 排序

- **ASC** : 升序(默认)
- **DESC** : 降序

```sql
-- 排序，可以按多个列进行排序，优先级按位次	
SELECT * FROM myFirstTable
	ORDER BY col DESC, col2 ASC;
```

![image-20220413110103633](D:\微信推文\images\image-20220413110103633.png)

#### 1.9 过滤

不进行过滤的数据非常大，导致通过网络传输了多余的数据，从而浪费了网络带宽。因此尽量使用 SQL 语句来过滤不必要的数据，而不是传输所有的数据到客户端中然后由客户端进行过滤。

```sql
-- 过滤数据
SELECT * FROM myFirstTable
		WHERE col2 IS NULL; # 找出col2为空的元组
```

![image-20220413111025279](D:\微信推文\images\image-20220413111025279.png)

下表显示了 WHERE 子句可用的操作符

| 操作符  |     说明     |
| :-----: | :----------: |
|    =    |     等于     |
|    <    |     小于     |
|    >    |     大于     |
|  <> !=  |    不等于    |
|  <= !>  |   小于等于   |
|  >= !<  |   大于等于   |
| BETWEEN | 在两个值之间 |
| IS NULL |  为 NULL 值  |

应该注意到，NULL 与 0、空字符串都不同。

**AND 和 OR**  用于连接多个过滤条件。优先处理 AND，当一个过滤表达式涉及到多个 AND 和 OR 时，可以使用 () 来决定优先级，使得优先级关系更清晰。

**IN**  操作符用于匹配一组值，其后也可以接一个 SELECT 子句，从而匹配子查询得到的一组值。

**NOT**  操作符用于否定一个条件。

#### 1.10 通配符

通配符也是用在过滤语句中，但它只能用于文本字段。

- **%**  匹配 >=0 个任意字符；
- **_**  匹配 ==1 个任意字符；
- **[ ]**  可以匹配集合内的字符，例如 [ab] 将匹配字符 a 或者 b。用脱字符 ^ 可以对其进行否定，也就是不匹配集合内的字符。

使用 Like 来进行通配符匹配。不要滥用通配符，**通配符位于开头处匹配会非常慢**。

```sql
-- 通配符的使用
SELECT * FROM myFirstTable
		WHERE col LIKE '[^ab]%'; # 找出不以 A 和 B 开头的任意文本
```

#### 1.11 计算字段

在数据库服务器上完成数据的转换和格式化的工作往往比客户端上快得多，并且转换和格式化后的数据量更少的话可以减少网络通信量。

```sql
-- 计算字段
SELECT col * col2 AS alias # 用as重命名是常见的
	FROM myFirstTable;

SELECT CONCAT(TRIM(col),'(',TRIM(col2),')') AS concat_col # 分隔
	FROM myFirstTable;
```

![](D:\微信推文\images\image-20220413135846693.png)图：col*col2相乘        ![image-20220413140414459](D:\微信推文\images\image-20220413140414459.png)图：concat函数的应用

#### 1.12 函数

##### 1.12.1 各个 DBMS 的函数都是不相同的，因此不可移植，以下主要是 MySQL 的函数。

|  函 数  |      说 明       |
| :-----: | :--------------: |
|  AVG()  | 返回某列的平均值 |
| COUNT() |  返回某列的行数  |
|  MAX()  | 返回某列的最大值 |
|  MIN()  | 返回某列的最小值 |
|  SUM()  |  返回某列值之和  |

```sql
-- 函数--avg（）示例,AVG() 会忽略 NULL 行。
SELECT AVG(DISTINCT col) AS avg_col # 使用 DISTINCT 可以让汇总函数值汇总不同的值。
	FROM myFirstTable;
```

![image-20220413141125685](D:\微信推文\images\image-20220413141125685.png)

##### 1.12.2 日期和时间处理

- 日期格式: YYYY-MM-DD
- 时间格式: HH:MM:SS

|     函 数     |             说 明              |
| :-----------: | :----------------------------: |
|   AddDate()   |     增加一个日期(天、周等)     |
|   AddTime()   |     增加一个时间(时、分等)     |
|   CurDate()   |          返回当前日期          |
|   CurTime()   |          返回当前时间          |
|    Date()     |     返回日期时间的日期部分     |
|  DateDiff()   |        计算两个日期之差        |
|  Date_Add()   |     高度灵活的日期运算函数     |
| Date_Format() |  返回一个格式化的日期或时间串  |
|     Day()     |     返回一个日期的天数部分     |
|  DayOfWeek()  | 对于一个日期，返回对应的星期几 |
|    Hour()     |     返回一个时间的小时部分     |
|   Minute()    |     返回一个时间的分钟部分     |
|    Month()    |     返回一个日期的月份部分     |
|     Now()     |       返回当前日期和时间       |
|   Second()    |      返回一个时间的秒部分      |
|    Time()     |   返回一个日期时间的时间部分   |
|    Year()     |     返回一个日期的年份部分     |

```sql
mysql> SELECT CURDATE();
```

```text
2022-4-13
```

##### 1.12.3 数值处理

|  函数  |  说明  |
| :----: | :----: |
| SIN()  |  正弦  |
| COS()  |  余弦  |
| TAN()  |  正切  |
| ABS()  | 绝对值 |
| SQRT() | 平方根 |
| MOD()  |  余数  |
| EXP()  |  指数  |
|  PI()  | 圆周率 |
| RAND() | 随机数 |

---

阶段总结：以上为SQL语言非常基础的部分，看到这里我们还是会认为SQL与Excel没有啥区别。且往下看吧！

#### 1.15 分组

分组就是把具有相同的数据值的行放在同一组中。

```sql
-- 分组
-- 指定的分组字段除了能按该字段进行分组，也会自动按该字段进行排序
SELECT col, COUNT(*) AS num
	FROM myfirsttable
	GROUP BY col;
-- GROUP BY 自动按分组字段进行排序，ORDER BY 也可以按汇总字段来进行排序
SELECT col, COUNT(*) AS num
	FROM myfirsttable
	GROUP BY col
	ORDER BY num;
-- WHERE 过滤行，HAVING 过滤分组，行过滤应当先于分组过滤
SELECT col, COUNT(*) AS num
	FROM myfirsttable
	WHERE col > 100
	GROUP BY col
	HAVING num >= 1;
```

![image-20220414090512275](D:\微信推文\images\image-20220414090512275.png)

![image-20220414090644627](D:\微信推文\images\image-20220414090644627.png)

![image-20220414092301170](D:\微信推文\images\image-20220414092301170.png)

#### 1.16 子查询

子查询中只能返回一个字段的数据。

可以将子查询的结果作为 WHRER 语句的过滤条件:

```sql
SELECT *
FROM mytable1
WHERE col1 IN (SELECT col2
               FROM mytable2);
```

下面的语句可以检索出客户的订单数量，子查询语句会对第一个查询检索出的每个客户执行一次:

```sql
SELECT cust_name, (SELECT COUNT(*)
                   FROM Orders
                   WHERE Orders.cust_id = Customers.cust_id)
                   AS orders_num
FROM Customers
ORDER BY cust_name;
    
```

#### 1.17 连接

连接用于连接多个表，使用 JOIN 关键字，并且条件语句使用 ON 而不是 WHERE。

连接可以替换子查询，并且比子查询的效率一般会更快。

可以用 AS 给列名、计算字段和表名取别名，给表名取别名是为了简化 SQL 语句以及连接相同表。

##### 1.17.1 内连接

内连接又称等值连接，使用 INNER JOIN 关键字。

```sql
SELECT A.value, B.value
FROM tablea AS A INNER JOIN tableb AS B
ON A.key = B.key;
```

可以不明确使用 INNER JOIN，而使用普通查询并在 WHERE 中将两个表中要连接的列用等值方法连接起来。

```sql
SELECT A.value, B.value
FROM tablea AS A, tableb AS B
WHERE A.key = B.key; 
```

在没有条件语句的情况下返回笛卡尔积。

##### 1.17.2 自连接

自连接可以看成内连接的一种，只是连接的表是自身而已。

一张员工表，包含员工姓名和员工所属部门，要找出与 Jim 处在同一部门的所有员工姓名。

子查询版本

```sql
SELECT name
FROM employee
WHERE department = (
      SELECT department
      FROM employee
      WHERE name = "Jim");
```

自连接版本

```sql
SELECT e1.name
FROM employee AS e1 INNER JOIN employee AS e2
ON e1.department = e2.department
      AND e2.name = "Jim";   
```

##### 1.17.3 自然连接

自然连接是把同名列通过等值测试连接起来的，同名列可以有多个。

内连接和自然连接的区别: 内连接提供连接的列，而自然连接自动连接所有同名列。

```sql
SELECT A.value, B.value
	FROM tablea AS A NATURAL JOIN tableb AS B;   
```

##### 1.17.4 外连接

外连接保留了没有关联的那些行。分为左外连接，右外连接以及全外连接，左外连接就是保留左表没有关联的行。

检索所有顾客的订单信息，包括还没有订单信息的顾客。

```sql
SELECT customers.`cust_id`, customers.`cust_name`,orders.`order_id`
	FROM customers LEFT OUTER JOIN orders
	ON customers.`cust_id` = orders.`cust_id`;
```

![image-20220414143346867](D:\微信推文\images\image-20220414143346867.png)

![image-20220414143405343](D:\微信推文\images\image-20220414143405343.png)

![image-20220414143421481](D:\微信推文\images\image-20220414143421481.png)

#### 1.18 组合查询

使用  **UNION**  来组合两个查询，如果第一个查询返回 M 行，第二个查询返回 N 行，那么组合查询的结果一般为 M+N 行。

每个查询必须包含相同的列、表达式和聚集函数。

默认会去除相同行，如果需要保留相同行，使用 UNION ALL。

只能包含一个 ORDER BY 子句，并且必须位于语句的最后。

```sql
SELECT col FROM myfirsttable
	WHERE col = 1
	UNION
	SELECT col FROM myfirsttable
	WHERE col = 100;
```

![image-20220414144245159](D:\微信推文\images\image-20220414144245159.png)

#### 1.19 视图

视图是虚拟的表，本身不包含数据，也就不能对其进行索引操作。

对视图的操作和对普通表的操作一样。

视图具有如下好处:

- 简化复杂的 SQL 操作，比如复杂的连接；
- 只使用实际表的一部分数据；
- 通过只给用户访问视图的权限，保证数据的安全性；
- 更改数据格式和表示。

```sql
CREATE VIEW myview AS
SELECT Concat(col1, col2) AS concat_col, col3*col4 AS compute_col
FROM mytable
WHERE col5 = val;
```

#### 1.20 存储过程

存储过程可以看成是对一系列 SQL 操作的批处理。

使用存储过程的好处:

- 代码封装，保证了一定的安全性；
- 代码复用；
- 由于是预先编译，因此具有很高的性能。

命令行中创建存储过程需要自定义分隔符，因为命令行是以 ; 为结束符，而存储过程中也包含了分号，因此会错误把这部分分号当成是结束符，造成语法错误。

包含 in、out 和 inout 三种参数。

给变量赋值都需要用 select into 语句。

每次只能给一个变量赋值，不支持集合的操作。

```sql
delimiter //

create procedure myprocedure( out ret int )
    begin
        declare y int;
        select sum(col1)
        from mytable
        into y;
        select y*y into ret;
    end //

delimiter ;
```

```sql
call myprocedure(@ret);
select @ret; 
```

#### 1.21 游标

在存储过程中使用游标可以对一个结果集进行移动遍历。

游标主要用于交互式应用，其中用户需要对数据集中的任意行进行浏览和修改。

使用游标的四个步骤:

1. 声明游标，这个过程没有实际检索出数据；
2. 打开游标；
3. 取出数据；
4. 关闭游标；

```sql
delimiter //
create procedure myprocedure(out ret int)
    begin
        declare done boolean default 0;

        declare mycursor cursor for
        select col1 from mytable;
        # 定义了一个 continue handler，当 sqlstate '02000' 这个条件出现时，会执行 set done = 1
        declare continue handler for sqlstate '02000' set done = 1;

        open mycursor;

        repeat
            fetch mycursor into ret;
            select ret;
        until done end repeat;

        close mycursor;
    end //
 delimiter ;
```

#### 1.22 触发器

触发器会在某个表执行以下语句时而自动执行: DELETE、INSERT、UPDATE。

触发器必须指定在语句执行之前还是之后自动执行，之前执行使用 BEFORE 关键字，之后执行使用 AFTER 关键字。BEFORE 用于数据验证和净化，AFTER 用于审计跟踪，将修改记录到另外一张表中。

INSERT 触发器包含一个名为 NEW 的虚拟表。

```sql
CREATE TRIGGER mytrigger AFTER INSERT ON mytable
FOR EACH ROW SELECT NEW.col into @result;

SELECT @result; -- 获取结果 
```

DELETE 触发器包含一个名为 OLD 的虚拟表，并且是只读的。

UPDATE 触发器包含一个名为 NEW 和一个名为 OLD 的虚拟表，其中 NEW 是可以被修改的，而 OLD 是只读的。

MySQL 不允许在触发器中使用 CALL 语句，也就是不能调用存储过程。

#### 1.23 事务管理

基本术语:

- 事务(transaction)指一组 SQL 语句；
- 回退(rollback)指撤销指定 SQL 语句的过程；
- 提交(commit)指将未存储的 SQL 语句结果写入数据库表；
- 保留点(savepoint)指事务处理中设置的临时占位符(placeholder)，你可以对它发布回退(与回退整个事务处理不同)。

不能回退 SELECT 语句，回退 SELECT 语句也没意义；也不能回退 CREATE 和 DROP 语句。

MySQL 的事务提交默认是隐式提交，每执行一条语句就把这条语句当成一个事务然后进行提交。当出现 START TRANSACTION 语句时，会关闭隐式提交；当 COMMIT 或 ROLLBACK 语句执行后，事务会自动关闭，重新恢复隐式提交。

通过设置 autocommit 为 0 可以取消自动提交；autocommit 标记是针对每个连接而不是针对服务器的。

如果没有设置保留点，ROLLBACK 会回退到 START TRANSACTION 语句处；如果设置了保留点，并且在 ROLLBACK 中指定该保留点，则会回退到该保留点。

```sql
START TRANSACTION
// ...
SAVEPOINT delete1
// ...
ROLLBACK TO delete1
// ...
COMMIT
```

#### 1.24 字符集

基本术语:

- 字符集为字母和符号的集合；
- 编码为某个字符集成员的内部表示；
- 校对字符指定如何比较，主要用于排序和分组。

除了给表指定字符集和校对外，也可以给列指定:

```sql
CREATE TABLE mytable
(col VARCHAR(10) CHARACTER SET latin COLLATE latin1_general_ci )
DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;    
```

可以在排序、分组时指定校对:

```sql
SELECT *
FROM mytable
ORDER BY col COLLATE latin1_general_ci; 
```

#### 1.25 权限管理

MySQL 的账户信息保存在 mysql 这个数据库中。

```sql
USE mysql;
SELECT user FROM user;    
```

**创建账户**

新创建的账户没有任何权限。

```sql
CREATE USER myuser IDENTIFIED BY 'mypassword';
```

**修改账户名**

```sql
RENAME myuser TO newuser;
```

**删除账户**

```sql
DROP USER myuser;
```

**查看权限**

```sql
SHOW GRANTS FOR myuser;   
```

**授予权限**

账户用 username@host 的形式定义，username@% 使用的是默认主机名。

```sql
GRANT SELECT, INSERT ON mydatabase.* TO myuser; 
```

**删除权限**

GRANT 和 REVOKE 可在几个层次上控制访问权限:

- 整个服务器，使用 GRANT ALL 和 REVOKE ALL；
- 整个数据库，使用 ON database.*；
- 特定的表，使用 ON database.table；
- 特定的列；
- 特定的存储过程。

```sql
REVOKE SELECT, INSERT ON mydatabase.* FROM myuser;
```

**更改密码**

必须使用 Password() 函数

```sql
SET PASSWROD FOR myuser = Password('new_password');
```

---

阶段总结：SQL语法基础大概就是这些，下半部分明显区别于Excel，或者Excel附属于SQL？

整理自@pdai，本文仅做了部分可视化作业和整理。

著作权归https://pdai.tech所有。 链接：https://www.pdai.tech/md/db/sql-lan/sql-lan.html

