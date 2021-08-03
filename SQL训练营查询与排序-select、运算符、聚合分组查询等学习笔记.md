# SQL训练营查询与排序-select、运算符、聚合分组查询等学习笔记
本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql

## 符号相关
### 不等于**用<>**

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
