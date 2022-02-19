---
title: SQL in 10minuts a day
tags: SQL
author: Wang xu
top: False
---


# SQL in 10minuts a day

## A。基本概念：

database保存有组织的数据的容器（通常是一个文件或一组文件）。

table某种特定类型数据的结构化清

schema关于数据库和表的布局及特性的信息

column列

row行

primary key:一列（或一组列），其值能够唯一标识表中每一行。

Datatype数据类型

foreign key外键

clause语句

operator操作符

wildcard通配符

Field字段

alias别名，对应的就是语句里面的AS

query查询

correlated subquery相关子查询

SQL-Structured Query Language

DDL:

(Data Definition Language 数据定义语言)用于操作对象和对象的属性，这种对象包括数据库本身:

DDL的主要语句(操作)

Create语句：可以创建数据库和数据库的一些对象。

Drop语句：可以删除数据表、索引、触发程序、条件约束以及数据表的权限等。

Alter语句：修改数据表定义及属性

DML:

(Data Manipulation Language 数据操控语言)用于操作数据库对象中包含的数据  

DML的主要语句(操作)

Insert语句：向数据表张插入一条记录。

Delete语句：删除数据表中的一条或多条记录，也可以删除数据表中的所有记录，但是，它的操作对象仍是记录。

Update语句：用于修改已存在表中的记录的内容。

DML的操作对象——记录

DCL:

(Data Control Language 数据控制语句)的操作是数据库对象的权限  

DCL的主要语句(操作)

Grant语句：允许对象的创建者给某用户或某组或所有用户(PUBLIC)某些特定的权限。

Revoke语句：可以废除某用户或某组或所有用户访问权限

DCL的操作对象(用户) 

(Data Control Language 数据控制语句)的操作是数据库对象的权限  

DCL的主要语句(操作)

Grant语句：允许对象的创建者给某用户或某组或所有用户(PUBLIC)某些特定的权限。

Revoke语句：可以废除某用户或某组或所有用户访问权限

DCL的操作对象(用户)

## B。

SQL 语句不区分大小写;在处理SQL 语句时，其中所有空格都被忽略;多条SQL 语句必须以分号（；）分隔。

### 1.SELECT：

```
SELECT prodname FROM Products;
```

--在选择多个列时，一定要在列名之间加上逗号，但最后一个列名后不加。如果在最后一个列名后加了逗号，将出现错误。

```
SELECT DISTINCT vend_id FROM Products;
```

DISTINCT的作用是用来去重

```
SELECT prodid, prodbname, prodprice FROM Products;

SELECT * FROM Products;*
```

注意：如果需要限定返回的行数

//在SQL server和Access中--使用TOP

```
SELECT TOP 5 prodname FROM Products;
```

//DB2中--使用FETCH FIRST 5 ROWS ONLY

```
SELECT prodname FROM Products FETCH FIRST 5 ROWS ONLY;
```

//使用Oracle--WHERE ROWNUM

```
SELECT prodname FROM Products WHERE ROWNUM <=5;
```

//使用MySQL、MariaDB、PostgreSQL 或者SQLite--使用LIMIT

```
SELECT prodname FROM Products LIMIT 5;
```

```
SELECT prodname FROM Products LIMIT 5 OFFSET 5;
```

返回从第5 行起的5 行数据；第一个数字是指从哪儿开始，第二个数字是检索的行数

LIMIT 1 OFFSET1 会检索第2 行，而不是第1 行。如果写成LIMIT 3,4需要特别注意，前一个是OFFSET，后一个是LIMIT

> LIMIT 3,4 => the value before the  ,  is the OFFSET and the value after the  ,  is the LIMIT

### 2.注释（comments）：

 -- 这是一条注释；# 这是一条注释；注释从/*开始，到*/结束，/*和*/之间的任何内容都是注释

### 3.排序检索数据 order by

```
A.单个排序：SELECT prodname FROM Products ORDER BY prodname;
```

在指定一条ORDER BY 子句时，应该保证它是**SELECT 语句中最后一条子句**

```
B.按多个排序：ORDER BY prodprice, prodname;
```

仅在多个行具有相同的prodprice 值时才对产品按prodname 进行排序。如果prodprice 列中所有的值都是唯一的，则不会prodname 排序。

```
c.按列位置排序: ORDER BY 2，3;
```

```
d.按指定方向：ORDER BY prodprice DESC(or ASC);
```

### 4.过滤数据

```
WHERE prodprice = 3.49;
```

在同时使用ORDER BY 和WHERE 子句时，应该让ORDER BY 位于 WHERE 之后，否则将会产生错误。

WHERE语句的操作符（有些是等价的）

    Operator     Description
    =             Equality
    < >           Nonequality
    !=            Nonequality
    <             Less than
    <=            Less than or equal to
    !<            Not less than
    >             Greater than
    >=            Greater than or equal to
    !>            Not greater than
    BETWEEN       Between two specified values（包含两端）
    IS NULL       Is a NULL value

### 5.高级过滤（WHERE + AND/OR; IN/NOT）

```
WHERE vendid = 'DLL01' AND prodprice <= 4;
```

```
WHERE vendid = 'DLL01' OR vendid = ‘BRS01’;
```

```
WHERE vendid = 'DLL01' OR vendid = 'BRS01' AND prodprice >= 10;
```

在处理OR 操作符前，优先处理AND 操作符。当SQL 看到上述WHERE 子句时，它理解为：由供应商BRS01 制造的价格为10 美元以上的所有产品，以及由供应商DLL01 制造的所有产品，而不管其价格如何。解决办法就是加上()

```
WHERE (vendid = 'DLL01' OR vendid = 'BRS01') AND prodprice >= 10;
```

IN操作符与 OR操作结果是一样的

```
WHERE vendid IN ('DLL01' , 'BRS01')  ORDER BY prodname;

为什么要用IN，而不推荐OR？
-速度快
-配合使用AND/OR，逻辑更清楚
-可以嵌套在更复杂的语句中
```

NOT操作符（也可以用\<\>）

```
WHERE NOT vendid = 'DLL01' ORDER BY prodname;
```

### 6.Wildcard用通配符进行过滤-LIKE

原则：通配符应该细心使用，不要使用过度

```
WHERE prodname LIKE 'Fish%';

WHERE prodname LIKE '%bean bag%';

WHERE prod_name LIKE 'F%y';
```

有一种情况下把通配符放在搜索模式中间是很有用的，就是根据邮件

地址的一部分来查找电子邮件， 例如WHERE email LIKE '[b%@forta.com](mailto:b%@forta.com)'

```
WHERE prodname LIKE '__ inch teddy bear';

WHERE prodname LIKE '% inch teddy bear';

# 注意这两个有区别，两个下划线是要匹配两个字符；%无所谓
```

    # find all contacts whose names begin with the letter J or M
    WHERE cust_contact LIKE '[JM]%'
    
    #not begin with the letter J or M
    WHERE cust_contact LIKE '[^JM]%'
    等价于:
    WHERE NOT cust_contact LIKE '[JM]%'

### 7.创建计算字段（Calculated Fields）

> 实际中可能会遇到以下需求：
> 
> > 你想将两列的字段合并在一起，比如国家+城市（可能原表中分为了两列）
> > 
> > 大小写可能混在一起，你要标准到一个大写或者小写
> > 
> > 提取某些需要的字段
> > 
> > 计算新的特征，比如平均数，总和等等

总结为： 计算字段 ； 拼接字段

#### 拼接字段：

```
SELECT vendname + ' (' + vendcountry + ')'
FROM Vendors
ORDER BY vendname;

或者

SELECT vendname || ' (' || vendcountry || ')'
FROM Vendors
ORDER BY vendname;

# MySQL or MariaDB
SELECT Concat(vend_name, ' (', vend_country, ')')
FROM Vendors
ORDER BY vend_name;
```

上述拼接会有空格，如果要去除掉空格的话，使用TRIM,RTRIM-去除右边的空格；LTRIM--去除左边的空格；TRIM--去除两边的空格：

```
SELECT RTRIM(vendname) + ' (' + RTRIM(vendcountry) + ')'
FROM Vendors
ORDER BY vendname;
```

使用别名（Aliases）：AS

```
SELECT RTRIM(vendname) + ' (' + RTRIM(vendcountry) + ')' AS vendtitle
FROM Vendors
ORDER BY vendname;
```

=\>MySQL 和MariaDB 中使用的语句是CONCAT

#### 计算字段：

```
SELECT 
prodid,
quantity,
itemprice,
quantity*itemprice AS expandedprice

FROM OrderItems

WHERE ordernum = 20008;
```

### 8.使用函数（Data Manipulation Functions）

=\>文本处理函数

LEFT()  RIGHT() 

UPPER()  LOWER()

TRIM()  RTRIM()  LTRIM()

SOUNDEX()  

LENGTH(){DATALENGTH() or LEN()}

=\>日期和时间处理函数

```
#SQL Server
SELECT ordernum
FROM Orders
WHERE DATEPART(yy, orderdate) = 2020;

#Progresql
SELECT ordernum
FROM Orders
WHERE DATEPART('year', orderdate) = 2020;

#Oracle
SELECT ordernum
FROM Orders
WHERE EXTRACT(year FROM orderdate) = 2020;

#使用BETWEEN
SELECT order_num
FROM Orders
WHERE order_date BETWEEN to_date('2020-01-01', 'yyyy-mm-dd')
AND to_date('2020-12-31', 'yyyy-mm-dd');


#DB2, MySQL, MariaDB
SELECT order_num
FROM Orders
WHERE YEAR(order_date) = 2020;

#SQLite
SELECT order_num
FROM Orders
WHERE strftime('%Y', order_date) = '2020';
```

=\>数值处理函数

ABS()  COS()  SIN()  TAN()   SQRT()   PI()  EXP()

### 9.汇总数据(Summarizing Data)

Aggregate Functions:

> AVG()  COUNT()  MAX()  MIN()  SUM()

=>AVG()

AVG()函数忽略列值为NULL 的行。

```
SELECT AVG(prodprice) AS avgprice


SELECT AVG(prod_price) AS avg_price
FROM Products
WHERE vend_id = 'DLL01';
```

=\>COUNT()

如果指定列名，则COUNT()函数会忽略指定列的值为空的行，但如果COUNT()函数中用的是星号（*），则不忽略。

```
SELECT COUNT(*) AS numcust
FROM Customers;
```

=\>MAX()和MIN()

忽略列值为NULL 的行；在用于文本数据时，返回按该列排序后的最后一行

```
SELECT MAX(prodprice) AS maxprice
FROM Products;
```

=\>SUM()

```
SELECT SUM(itemprice x quantity) AS totalprice
FROM OrderItems
WHERE ordernum = 20005;
```

聚集不同值 :ALL DISTINCT

组合:

```
SELECT COUNT(*) AS numitems,
       MIN(prodprice) AS pricemin,
       MAX(prodprice) AS pricemax,
       AVG(prodprice) AS priceavg

FROM Products;
```

### 10.数据分组(GROUP BY)

```
SELECT vendid, COUNT(*) AS numprods
FROM Products
GROUP BY vendid;
#返回的是两列，第一列是不同vendid，第二列每个不同vendid有多少个
```

Groups的过滤怎么做？

```
SELECT cust_id, COUNT(*) AS orders
FROM Orders
GROUP BY custid
HAVING COUNT(*) >= 2;
```

学了GROUP BY，那么以下问题就来了：

##### 说明1：HAVING 和WHERE 的差别

这里有一种理解方法，WHERE 在数据分组前进行过滤，HAVING 在数据分组后进行过滤。那么意思就是：WHERE 过滤掉的的行就不会被包含在分组中， 从而影响HAVING 子句中基于这些值过滤掉的分组。

对比以下两个代码：

    S#ELECT vend_id, COUNT(*) AS num_prods
    FROM Products
    WHERE prod_price >= 4
    GROUP BY vend_id
    HAVING COUNT(*) >= 2;
    
    #输出结果
    vend_id num_prods
    ------- -----------
    BRS01     3
    FNG01     2
    
    对比：
    
    SELECT vend_id, COUNT(*) AS num_prods
    FROM Products
    GROUP BY vend_id
    HAVING COUNT(*) >= 2;
    
    #输出结果
    
    vend_id num_prods
    ------- -----------
    BRS01     3
    DLL01     4
    FNG01     2

##### 说明2：分组GROUP BY和排序ORDER BY

```
SELECT ordernum, COUNT(*) AS items
FROM OrderItems
GROUP BY ordernum
HAVING COUNT(*) >= 3;
```

上述是默认按照ordernum来排序的，如果需要按照item的数量来排序的话，需要再最后一行加上语句ORDER BY

    SELECT order_num, COUNT(*) AS items
    FROM OrderItems
    GROUP BY order_num
    HAVING COUNT(*) >= 3
    ORDER BY items, order_num;

SELECT子句及其顺序

子 句           说 明                        是否必须使用

SELECT       要返回的列或表达式    是

FROM         从中检索数据的表      仅在从表选择数据时使用

WHERE        行级过滤                  否

GROUP BY   分组说明                  仅在按组计算聚集时使用

HAVING       组级过滤                  否

ORDER BY    输出排序顺序            否

### 11.子查询(Subqueries)

子查询常用于WHERE 子句的IN 操作符中，以及用来填充计算列。详细过程查阅原书第11章

    SELECT order_num
    FROM OrderItems
    WHERE prod_id = 'RGAN01;
    
    SELECT cust_id
    FROM Orders
    WHERE order_num IN (20007,20008)；
    
    #将上面两个综合起来：
    SELECT cust_id
    FROM Orders
    WHERE order_num IN (SELECT order_num
                        FROM OrderItems
                        WHERE prod_id = 'RGAN01');
    
    还可以继续嵌套子查询下去：
    SELECT cust_name, cust_contact
    FROM Customers
    WHERE cust_id IN (SELECT cust_id
                      FROM Orders
                      WHERE order_num IN (SELECT order_num
                                          FROM OrderItems
                                          WHERE prod_id = 'RGAN01'));
    
    这样子嵌套多个子查询效率比较低，一般都会转化成表联结的方式（参考第12、13章）
    SELECT cust_name, cust_contact
    FROM Customers, Orders, OrderItems
    WHERE Customers.cust_id = Orders.cust_id
    AND OrderItems.order_num = Orders.order_num
    AND prod_id = 'RGAN01';

用来做填充计算列（Using Subqueries as Calculated Fields）

    SELECT cust_name,
           cust_state,
           (SELECT COUNT(*)
            FROM Orders
            WHERE Orders.cust_id = Customers.cust_id) AS orders
    FROM Customers
    ORDER BY cust_name;

### 12.联结表（Joins）

关系数据库的可伸缩性远比非关系数据库要好（scale well）

创建一个关联表

```
SELECT vendname, prodname, prodprice
FROM Vendors, Products
WHERE Vendors.vendid = Products.vendid;


#use the INNER JOINS to achieve the same:

SELECT vend_name, prod_name, prod_price
FROM Vendors
INNER JOIN Products ON Vendors.vend_id = Products.vend_id;
```

说明：SELECT 语句与前面所有语句一样指定要检索的列。这里最大的差别是所指定的两列（prod*name 和prod*price）在一个表中，而第三列（vend*name）在另一个表中*

如果没有联结条件的表关系，返回的结果为笛卡儿积cartesian product。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。

```
SELECT vend*name, prod*name, prod*price*
FROM Vendors, Products;
```

创建多个表联结：

    SELECT prod_name, vend_name, prod_price, quantity
    FROM OrderItems, Products, Vendors
    WHERE Products.vend_id = Vendors.vend_id
    AND OrderItems.prod_id = Products.prod_id
    AND order_num = 20007;

### 13.高级联结

=> 使用别名，可以在column上用，也可以在table上用（注：Oracle里面没有AS语句，只需要在后面跟着简写就行）

    SELECT RTRIM(vend_name) + ' (' + RTRIM(vend_country) + ')'
    AS vend_title
    FROM Vendors AS V
    ORDER BY vend_name;

表别名它不仅能用于WHERE 子句，还可以用于SELECT 的列表、ORDER BY 子句以及其他语句部分。

=> 使用不同的联结方式(Join Type)

1.自联结self-join

```
SELECT custid, custname, custcontact
FROM Customers
WHERE cust*name = (SELECT cust*name
                   FROM Customers
                   WHERE cust*contact = 'Jim Jones');

可以更改成：

SELECT c1.custid, c1.custname, c1.custcontact
FROM Customers AS c1, Customers AS c2
WHERE c1.cust*name = c2.cust*name
AND c2.cust*contact = 'Jim Jones';
```

总结：自联结通常作为外部语句，用来替代从相同表中检索数据的使用子查询语句。虽然最终的结果是相同的，但许多DBMS 处理联结远比处理子查询快得多。应该试一下两种方法，以确定哪一种的性能更好。

2.Natural Joins

    SELECT C.*, O.order_num, O.order_date,
    OI.prod_id, OI.quantity, OI.item_price
    FROM Customers AS C, Orders AS O,
    OrderItems AS OI
    WHERE C.cust_id = O.cust_id
    AND OI.order_num = O.order_num
    AND prod_id = 'RGAN01';

3.Outer Joins

    SELECT Customers.cust_id, Orders.order_num
    FROM Customers
    LEFT OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id;
    
    
    SELECT Customers.cust_id, Orders.order_num
    FROM Customers
    RIGHT OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id;
    #SQLite里面没有右联结
    
    
    SELECT Customers.cust_id, Orders.order_num
    FROM Customers
    FULL OUTER JOIN Orders ON Customers.cust_id = Orders.cust_id;
    #基本不用，MariaDB, MySQL, SQLite都不不支持全联结语句

### 14.组合查询

用UNION 操作符来组合SELECT 语句。利用UNION，可以把多条查询的结果作为一条组合查询返回，不管结果中有无重复。使用UNION 可极大地简化复杂的WHERE 子句，简化从多个表中检索数据的工作。

    SELECT cust_name, cust_contact, cust_email
    FROM Customers
    WHERE cust_state IN ('IL','IN','MI')
    UNION ALL
    SELECT cust_name, cust_contact, cust_email
    FROM Customers
    WHERE cust_name = 'Fun4All';

### 15.插入数据(INSERT)

**INSERT INTO xxx  VALUES('', '', '', '')**

假如想把另一表中的顾客(CustNew)列合并到Customers 表中。不需要每次读取一行再将它用INSERT 插入，可以如下：

```
INSERT INTO Customers("xx", "xx", "xx","xx" #columns)
SELECT xx,xx,xx,xx #columns
FROM CustNew;
```

如想要赋值一个table:

    CREATE TABLE CustCopy AS SELECT * FROM Customers;
    
    或者：
    
    SELECT * INTO CustCopy FROM Customers;

这条SELECT 语句创建一个名为CustCopy 的新表，并把Customers 表的整个内容复制到新表中。因为这里使用的是SELECT 星号，所以将在CustCopy 表中创建（并填充）与Customers 表的每一列相同的列。要想只复制部分的列，可以明确给出列名，而不是使用通配符。

### 16.更新和删除数据

    UPDATE Customers
    SET cust_email = 'kim@thetoystore.com', #一定要记得这个逗号
        cust_contact = 'Sam Roberts'
    WHERE cust_id = 1000000005;

UPDATE 语句以WHERE 子句结束，它告诉DBMS 更新哪一行。没有WHERE子句，DBMS 将会用这个电子邮件地址更新Customers 表中的所有行，SET后面还可以再加很多。

下面的语句从Customers 表中删除一行

```
DELETE FROM Customers
WHERE custid = '1000000006';
```

下面是许多SQL 程序员使用UPDATE 或DELETE 时所遵循的重要原则:

> 1.除非确实打算更新和删除每一行，否则绝对不要使用不带WHERE 子句的UPDATE 或DELETE 语句。
> 
> 2.保证每个表都有主键（如果忘记这个内容，请参阅第12 课），尽可能像WHERE 子句那样使用它（可以指定各主键、多个值或值的范围）。
> 
> 3.在UPDATE 或DELETE 语句使用WHERE 子句前，应该先用SELECT 进行测试，保证它过滤的是正确的记录，以防编写的WHERE 子句不正确。
> 
> 4.使用强制实施引用完整性的数据库（关于这个内容，请参阅第12 课），这样DBMS 将不允许删除其数据与其他表相关联的行。
> 
> 5.有的DBMS 允许数据库管理员施加约束，防止执行不带WHERE 子句的UPDATE 或DELETE 语句。如果所采用的DBMS 支持这个特性，应该使用它。

### 17.创建和操作表TABLE

CREATE TABLE 表名（表列的名字和定义，表列的名字和定义）

    CREATE TABLE OrderItems
    (                
        order_num     INTEGER    NOT NULL,
        order_item    INTEGER    NOT NULL,
        prod_id       CHAR(10)   NOT NULL,
        quantity      INTEGER    NOT NULL   DEFAULT 1,
        item_price    DECIMAL(8,2)  NOT NULL
    );

指定默认值，默认值经常用于日期或时间戳列

DBMS                  函数/变量

Access                 NOW()

DB2                    CURRENT*DATE*

MySQL               CURRENT*DATE()*

Oracle                 SYSDATE

PostgreSQL          CURRENT*DATE*

SQL Server           GETDATE()

SQLite                 date('now')

更新表：ALTER TABLE

##添加新的一列

```
ALTER TABLE Vendors
ADD vendphone CHAR(20);
```

##删除一列

```
ALTER TABLE Vendors
DROP COLUMN vendphone;
```

##删除表：

```
DROP TABLE CustCopy;
```

##重命名表

DB2、MariaDB、MySQL、Oracle 和PostgreSQL 用户使用RENAME语句，SQL Server 用户使用sp__rename 存储过程，SQLite 用户使用ALTER TABLE 语句。

### 18.使用视图(Views)

视图为虚拟的表。它们包含的不是数据而是根据需要检索数据的查询。视图提供了一种封装SELECT 语句的层次，可用来简化数据处理，重新格式化或保护基础数据。简单点说就是在原来语句上加上一个类似创建虚拟环境语句：CREATE VIEW XXX AS即可

- Using Views to Reformat Retrieved Data
  
    CREATE VIEW VendorLocations AS
    SELECT RTRIM(vend_name) + ' (' + RTRIM(vend_country) + ')'
    AS vend_title
    FROM Vendors;

- Using Views to Filter Unwanted Data
  
    CREATE VIEW CustomerEMailList AS
    SELECT cust_id, cust_name, cust_email
    FROM Customers
    WHERE cust_email IS NOT NULL;

- Using Views with Calculated Fields
  
    CREATE VIEW OrderItemsExpanded AS
    SELECT order_num,
    prod_id,
    quantity,
    item_price,
    quantity*item_price AS expanded_price
    FROM OrderItems

### 总结

常用语句速查：

```
ALTER TABLE

ALTER TABLE 用来更新现存表的模式。

ALTER TABLE tablename

(

ADD|DROP column datatype [NULL|NOT NULL] [CONSTRAINTS],

ADD|DROP column datatype [NULL|NOT NULL] [CONSTRAINTS],

...

);

COMMIT

COMMIT 用来将事务写入数据库。

COMMIT [TRANSACTION];

CREATE INDEX

CREATE INDEX 用来为一列或多列创建索引

CREATE INDEX indexname

ON tablename (column, ...);

CREATE TABLE

CREATE TABLE 用来创建新的数据库表。可以用ALTER TABLE 来更新一个现存表的模式。

CREATE VIEW

CREATE VIEW 用来创建一个或多个表的视图。

DELETE

DELETE 用来从表中删除一行或多行。

DROP

DROP 用来永久性地删除数据库对象（表、视图、索引等）

INSERT

INSERT 用来对表添加一个新行。

INSERT SELECT

INSERT SELECT 用来将SELECT 的结果插入到表中。

ROLLBACK

ROLLBACK 用来撤销事务块。

SELECT

SELECT 用来从一个或多个表（或视图）中检索数据。

UPDATE

UPDATE 用来对表中的一行或多行进行更新。
```

### 用python处理Maritime Data时候遇到的问题

A。如何将pandas里面的DATAFRAME格式的数据传入到SQL数据库新建的TABLE中

1.新建TABLE后，通过循环语句

```python
for index,row in df,iterrows():

        cursor.execute("INSERT INTO tablename VALUES(?,?,?), (row[''],row[''],row['']))
```

2.或者通过to_sql
将df_clear csv数据导入到表mrv2019.db database as creating new table named mrv2019

```pyhton
df_clear.to_sql("mrv2019", connection,index=False)
```

B。要注意的问题：
SQLITE-要注意表一旦建立不能修改主键，不能修改列名，只能通过新建新表-复制旧表到新表-删除旧表-重命名表名 这种复杂的方式来更改。

> If we have create a table in Sqlite, then add IMO as primary key by four steps:
> 1.create new copy table;
> 
> 2.copy data into copy table;
> 
> 3.Drop old table;
> 
> 4.rename new copy table to old table

```
#代码实现
cursor.execute("INSERT INTO mrv2019copy SELECT * FROM mrv2019")

cursor.execute("DROP TABLE mrv2019")

cursor.execute("ALTER TABLE mrv2019copy INTO mrv2019")
```

C。从SQL提取数据后如何将提取的数据转化成DataFrame格式后再分析呢？
针对SQLITE的方式:

```python
import sqlalchemy
from sqlalchemy import create_engine
engine = create_engine('sqlite:///C:\\Users\\guoha\\mrv2019.db')
sql_cmd = "SELECT PortofRegistry AS Port, count(*) AS Number FROM mrv2019 GROUP BY PortofRegistry ORDER BY count(*) DESC LIMIT 21"
#提取船籍港登记数量Top21，降序排列
df_PoR = pd.read_sql(sql=sql_cmd, con=engine)
```


End

By Wang Xu in NUS