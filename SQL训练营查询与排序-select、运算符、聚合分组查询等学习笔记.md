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
### AND优先级比OR高，
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
