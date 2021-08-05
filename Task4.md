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
图1
### 内连结
```sql
-- 内连结
FROM <tb_1> INNER JOIN <tb_2> ON <condition(s)>
```
![5cc92f169da76c8de02ee074c0b3c1f9_O1CN01AeO1bP1x1Sk80t9I3_!!6000000006383-2-tps-324-383](https://user-images.githubusercontent.com/55366350/128287152-c8a1c09c-5a2b-46f2-8bcc-4486feedacc9.png)
图2
如上图，通过一个相同的东西（product_id）将两个表连接起来
```sql
SELECT SP.shop_id
       ,SP.shop_name
       ,SP.product_id
       ,P.product_name
       ,P.product_type
       ,P.sale_price
       ,SP.quantity
  FROM shop_product AS SP
 INNER JOIN product AS P
    ON SP.product_id = P.product_id;
```

#### 结合 WHERE 子句使用内连结
```sql
SELECT  SP.shop_id
       ,SP.shop_name
       ,SP.product_id
       ,P.product_name
       ,P.product_type
       ,P.sale_price
       ,SP.quantity
  FROM shop_product AS SP
 INNER JOIN product AS P
    ON SP.product_id = P.product_id
 WHERE SP.shop_name = '东京'
   AND P.product_type = '衣服' ;
```
实例：

找出每个商店里的衣服类商品的名称及价格等信息. 希望得到如下结果:
![3c48d7a65f610cba0c86b569b793234b_O1CN01fOWsrG1pZL75mXYuF_!!6000000005374-2-tps-577-112](https://user-images.githubusercontent.com/55366350/128287622-e384c467-4147-42c9-a28e-6c965053aff1.png)
```sql
-- 参考答案 1--不使用子查询
SELECT  SP.shop_id,SP.shop_name,SP.product_id 
       ,P.product_name, P.product_type, P.purchase_price
  FROM shop_product  AS SP 
 INNER JOIN product AS P 
    ON SP.product_id = P.product_id
 WHERE P.product_type = '衣服';
 
-- 参考答案 2--使用子查询
SELECT  SP.shop_id, SP.shop_name, SP.product_id
       ,P.product_name, P.product_type, P.purchase_price
  FROM shop_product AS SP 
INNER JOIN -- 从 product 表找出衣服类商品的信息
  (SELECT product_id, product_name, product_type, purchase_price
     FROM product	
    WHERE product_type = '衣服')AS P 
   ON SP.product_id = P.product_id;
```
二者结果相同
```sql
000A	东京	0001	T恤衫	衣服	500
000A	东京	0003	运动T恤	衣服	2800
000B	名古屋	0003	运动T恤	衣服	2800
000C	大阪	0003	运动T恤	衣服	2800
000D	福冈	0001	T恤衫	衣服	500
```
