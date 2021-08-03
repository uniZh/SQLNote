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
```sql
CREATE VIEW <视图名称>(<列名1>,<列名2>,...) AS <SELECT语句>

CREATE VIEW productsum (product_type, cnt_product)
AS
SELECT product_type, COUNT(*)
  FROM product
 GROUP BY product_type ;
```
