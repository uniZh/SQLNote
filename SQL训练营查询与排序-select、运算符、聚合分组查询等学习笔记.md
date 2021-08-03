# SQL训练营查询与排序-select、运算符、聚合分组查询等学习笔记
本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql
## SELECT语句
有下面例子基本可以理解
```sql
-- 想要查询出全部列时，可以使用代表所有列的星号（*）。
SELECT *
  FROM <表名>;
-- SQL语句可以使用AS关键字为列设定别名（用中文时需要双引号（“”））。
SELECT product_id     AS id,
       product_name   AS name,
       purchase_price AS "进货单价"
  FROM product;
-- 使用DISTINCT删除product_type列中重复的数据
SELECT DISTINCT product_type
  FROM product;
```

## 符号相关
### 不等于用<>
mysql中用<>与!=都是可以的，但sqlserver中不识别!=,所以建议用<>
### 等于用=
### 其他算术、比较符没什么好说的
### AND优先级比OR高
举例 A or B and C == A or (B and C)

但是我自己还是喜欢加()括号，你好我好大家好，逻辑清晰，谁都能看懂。
### NOT运算符
字面意思，没什么好说的
```sql
SELECT product_name, product_type, sale_price
  FROM product
 WHERE NOT sale_price >= 1000;
```
### 含有NULL时的真值
NULL的真值结果既不为真，也不为假，因为并不知道这样一个值。

那该如何表示呢？

这时真值是除真假之外的第三种值——**不确定**（UNKNOWN）。一般的逻辑运算并不存在这第三种值。SQL 之外的语言也基本上只使用真和假这两种真值。与通常的逻辑运算被称为二值逻辑相对，只有 SQL 中的逻辑运算被称为三值逻辑。

三值逻辑下的AND和OR真值表为：

![cbed7c630639c6889a101d890baca23c_O1CN01TwEsH71Y9NBeHdXGF_!!6000000003016-2-tps-548-359](https://user-images.githubusercontent.com/55366350/127945869-e0bcd8de-566e-49dc-afc9-44c44b5b81b7.png)

### NULL只能用IS，IS NOT判断不能用比较运算符判断
```sql
SELECT *
  FROM product
 WHERE purchase_price = NULL;
 
SELECT *
  FROM product
 WHERE purchase_price <> NULL;
 
 SELECT *
  FROM product
 WHERE product_name > NULL;
```
上面三条返回值均为空

想要获得purchase_price为空，用下面语句
```sql
SELECT * FROM product WHERE purchase_price IS NULL;
```
## 聚合查询

### 聚合函数
SQL中用于汇总的函数叫做聚合函数。以下五个是最常用的聚合函数：

COUNT：计算表中的记录数（行数）

SUM：计算表中数值列中数据的合计值

AVG：计算表中数值列中数据的平均值

MAX：求出表中任意列中数据的最大值

MIN：求出表中任意列中数据的最小值
```sql
-- 计算全部数据的行数（包含NULL）
SELECT COUNT(*)
  FROM product;
	
-- 计算NULL以外数据的行数
SELECT COUNT(purchase_price)
  FROM product;
	
-- 计算销售单价和进货单价的合计值
SELECT SUM(sale_price), SUM(purchase_price) 
  FROM product;
	
-- 计算销售单价和进货单价的平均值
SELECT AVG(sale_price), AVG(purchase_price)
  FROM product;
	
-- MAX和MIN也可用于非数值型数据
SELECT MAX(regist_date), MIN(regist_date)
  FROM product;
```
### 去除重复值
```sql
-- 计算去除重复数据后的数据行数
SELECT COUNT(DISTINCT product_type)
 FROM product;
```
### 常用法则
COUNT函数的结果根据参数的不同而不同。COUNT(*)会得到包含NULL的数据行数，而COUNT(<列名>)会得到NULL之外的数据行数。

聚合函数会将NULL排除在外。但COUNT(*)例外，并不会排除NULL。

MAX/MIN函数几乎适用于所有数据类型的列。SUM/AVG函数只适用于数值类型的列。

想要计算值的种类时，可以在COUNT函数的参数中使用DISTINCT。

在聚合函数的参数中使用DISTINCT，可以删除重复数据。
## 对表进行分组
执行顺序为：

FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY

having后面会说
### GROUP BY语句
之前使用聚合函数都是会整个表的数据进行处理，当你想将进行分组汇总时（即：将现有的数据按照某列来汇总统计），GROUP BY可以帮助你：
```sql
-- 按照商品种类统计数据行数
SELECT product_type, COUNT(*)
  FROM product
 GROUP BY product_type;
```
在 GROUP BY 子句中指定的列称为**聚合键**或者**分组列**
### 聚合建中包含NULL时
此时会将NULL作为一组特殊数据进行处理，就是除了非null的各组（行），还会多一个null组（行）
```sql
SELECT purchase_price, COUNT(*)
  FROM product
 GROUP BY purchase_price;
```
### 常见错误
在使用聚合函数及GROUP BY子句时，经常出现的错误有：

1.在聚合函数的SELECT子句中写了聚合健以外的列 使用COUNT等聚合函数时，SELECT子句中如果出现列名，只能是GROUP BY子句中指定的列名（也就是聚合键）。

2.在GROUP BY子句中使用列的别名 SELECT子句中可以通过AS来指定别名，但在GROUP BY中不能使用别名。因为在DBMS中 ,SELECT子句在GROUP BY子句后执行。

3.在WHERE中使用聚合函数 原因是聚合函数的使用前提是结果集已经确定，而WHERE还处于确定结果集的过程中，所以相互矛盾会引发错误。 如果想指定条件，可以在SELECT，HAVING（下面马上会讲）以及ORDER BY子句中使用聚合函数。
## 为聚合键指定条件
### 用HAVING的到特定分组
```
0001	T恤衫	衣服		1000	500	2009-09-20
0002	打孔器	办公用品	500	320	2009-09-11
0003	运动T恤	衣服		4000	2800	NULL
0004	菜刀	厨房用具	3000	1400	2009-09-20
0005	高压锅	厨房用具	6800	2500	2009-01-15
0006	叉子	厨房用具	500	NULL	2009-09-20
0007	擦菜板	厨房用具	880	395	2008-04-28
0008	圆珠笔	办公用品	100	NULL	2009-11-11
```
还记得表里的数据吧，这里的我折腾过和最开始设的可能有些不同，反正是这个意思就行

用GROUP BY把product_type分成了三组，衣服、办公用品、厨房用具

想取出其中两组，就要用到HAVING

这里WHERE不可行，因为，WHERE子句只能指定记录（行）的条件，而不能用来指定组的条件（例如，“数据行数为 2 行”或者“平均值为 500”等）。

可以在GROUP BY后使用HAVING子句。
### HAVING特点
HAVING子句用于对分组进行过滤，可以使用数字、聚合函数和GROUP BY中指定的列名（聚合键）。
```sql
-- 数字
SELECT product_type, COUNT(*)
  FROM product
 GROUP BY product_type
HAVING COUNT(*) = 2;
```
```
办公用品	2
衣服		2
```
```sql
-- 错误形式（因为product_name不包含在GROUP BY聚合键中）
SELECT product_type, COUNT(*)
  FROM product
 GROUP BY product_type
HAVING product_name = '圆珠笔';
```
