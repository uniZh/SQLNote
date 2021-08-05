# SQL训练营集合运算-表的加减法和join等学习笔记
本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql
## 表的加减法
### 并集 UNION
假设连锁店想要增加毛利率超过 50%或者售价低于 800 的货物的存货量, 请使用 UNION 对分别满足上述两个条件的商品的查询结果求并集.
```sql
SELECT  product_id,product_name,product_type
       ,sale_price,purchase_price
  FROM product 
 WHERE sale_price<800
  
 UNION
 
SELECT  product_id,product_name,product_type
       ,sale_price,purchase_price
  FROM product 
 WHERE sale_price>1.5*purchase_price;
```
### 包含重复行的集合运算 UNION ALL
UNION 会对两个查询的结果集进行合并和去重

UNION ALL则不会去重

## 连结
![3e539c8d2985eb3cc06319cefdbfc00f_O1CN01hOR4yP1NU1GvI82sE_!!6000000001572-2-tps-613-134](https://user-images.githubusercontent.com/55366350/128120217-ee7a808d-b650-4d64-93b8-86af706b7435.png)

