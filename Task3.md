# SQL训练营复杂查询方法-视图、子查询、函数等学习笔记
本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql
## 视图
视图不是表，视图是虚表，视图依赖于表
### 为什么需要视图
1.通过定义视图可以将频繁使用的SELECT语句保存以提高效率。

2.通过定义视图可以使用户看到的数据更加清晰。

3.通过定义视图可以不对外公开数据表全部字段，增强数据的保密性。

4.通过定义视图可以降低数据的冗余。
### 创建视图
需要注意的是在一般的DBMS中定义视图时**不能**使用ORDER BY语句。

在 MySQL中视图的定义是允许使用 ORDER BY 语句的，但是若从特定视图进行选择，而该视图使用了自己的 ORDER BY 语句，则视图定义中的 ORDER BY 将被忽略。
- 基于单表的视图
```sql
CREATE VIEW <视图名称>(<列名1>,<列名2>,...) AS <SELECT语句>
```
```sql
CREATE VIEW productsum (product_type, cnt_product)
AS
SELECT product_type, COUNT(*)
  FROM product
 GROUP BY product_type ;
```
- 基于多表的视图
建表
```sql
CREATE TABLE shop_product
(shop_id    CHAR(4)       NOT NULL,
 shop_name  VARCHAR(200)  NOT NULL,
 product_id CHAR(4)       NOT NULL,
 quantity   INTEGER       NOT NULL,
 PRIMARY KEY (shop_id, product_id));
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000A',	'东京',		'0001',	30);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000A',	'东京',		'0002',	50);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000A',	'东京',		'0003',	15);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0002',	30);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0003',	120);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0004',	20);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0006',	10);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000B',	'名古屋',	'0007',	40);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',		'0003',	20);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',		'0004',	50);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',		'0006',	90);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000C',	'大阪',		'0007',	70);
INSERT INTO shop_product (shop_id, shop_name, product_id, quantity) VALUES ('000D',	'福冈',		'0001',	100);
```
建多表视图
```sql
CREATE VIEW view_shop_product(product_type, sale_price, shop_name)
AS
SELECT product_type, sale_price, shop_name
  FROM product,
       shop_product
 WHERE product.product_id = shop_product.product_id;
```
可以在视图上进行查询
### 修改视图结构
```sql
ALTER VIEW <视图名> AS <SELECT语句>
```
```sql
ALTER VIEW productSum
    AS
        SELECT product_type, sale_price
          FROM Product
         WHERE regist_date > '2009-09-11';
```
### 更新视图内容
更新视图只能改变你在该视图看到的内容，更新单表视图，也会改变原表内容。

举例：视图中我更新price = 100的内容为200，视图中price = 100只有A项目，原表中有A、B、C项目的price都为100。那么B和C不会被更新视图内容的操作所改变。

视图只是原表的一个窗口，所以它修改也只能修改透过窗口能看到的内容。
```sql
UPDATE productsum
   SET sale_price = '5000'
 WHERE product_type = '办公用品';
```
上面的操作更新了视图和原表中的内容。

**注意：这里虽然修改成功了，但是并不推荐这种使用方式。而且我们在创建视图时也尽量使用限制不允许通过视图来修改表**

因为视图是一个虚拟表，所以对视图的操作就是对底层基础表的操作，所以在修改时只有满足底层基本表的定义才能成功修改。

对于一个视图来说，如果包含以下结构的任意一种都是不可以被更新的：

- 聚合函数 SUM()、MIN()、MAX()、COUNT() 等。

- DISTINCT 关键字。

- GROUP BY 子句。

- HAVING 子句。

- UNION 或 UNION ALL 运算符。

- FROM 子句中包含多个表。
### 删除视图
```sql
DROP VIEW <视图名1> [ , <视图名2> …]
```
```sql
DROP VIEW productSum;
```
## 子查询
在一个查询中嵌套另一个查询
### 嵌套子查询
```sql
SELECT product_type, cnt_product
FROM (SELECT *
        FROM (SELECT product_type, 
                      COUNT(*) AS cnt_product
                FROM product 
               GROUP BY product_type) AS productsum
       WHERE cnt_product = 4) AS productsum2;
```
### 标量子查询
执行的SQL语句只能返回一个值，也就是要返回表中具体的某一行的某一列。

通过标量子查询语句查询出销售单价高于平均销售单价的商品。
```sql
SELECT product_id, product_name, sale_price
  FROM product
 WHERE sale_price > (SELECT AVG(sale_price) FROM product);
```

### 关联子查询
**难点**

选取出各商品种类中高于该商品种类的平均销售单价的商品
```sql
SELECT product_type, product_name, sale_price
  FROM product ASp1
 WHERE sale_price > (SELECT AVG(sale_price)
                     FROM product ASp2
                      WHERE p1.product_type =p2.product_type
                     GROUP BY product_type);
```
## 上面内容回顾
最好不要对视图进行insert，

当向视图中插入数据时，同时也会向原表插入数据插入数据 ，若不满足原表插入的约束条件，会无法插入

## 各种各样的函数
### 算术函数
- ABS – 绝对值

语法：ABS( 数值 )

当 ABS 函数的参数为NULL时，返回值也是NULL

- MOD – 求余数

语法：MOD( 被除数，除数 )

注意：主流的 DBMS 都支持 MOD 函数，只有SQL Server 不支持该函数，其使用%符号来计算余数。

- ROUND – 四舍五入

语法：ROUND( 对象数值，保留小数的位数 )

注意：当参数 保留小数的位数 为变量时，可能会遇到错误，请谨慎使用变量。
### 字符串函数
- CONCAT – 拼接

语法：CONCAT(str1, str2, str3)

MySQL中使用 CONCAT 函数进行拼接。

- LENGTH – 字符串长度

语法：LENGTH( 字符串 )

- LOWER – 小写转换

LOWER 函数只能针对英文字母使用，它会将参数中的字符串全都转换为小写。该函数不适用于英文字母以外的场合，不影响原本就是小写的字符。

类似的， UPPER 函数用于大写转换。

- REPLACE – 字符串的替换

语法：REPLACE( 对象字符串，替换前的字符串，替换后的字符串 )

举例：REPLACE("abcSSSabc", "abc", "ABC" ) ---> ABCSSSABC

- SUBSTRING – 字符串的截取

语法：SUBSTRING （对象字符串 FROM 截取的起始位置 FOR 截取的字符数）

使用 SUBSTRING 函数 可以截取出字符串中的一部分字符串。截取的起始位置从字符串最左侧开始计算，索引值起始为1。

举例：SUBSTRING("abcdef" FROM 3 FOR 2) ----> cd
### 日期函数
