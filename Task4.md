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
GROUP BY 也可以用在里面
### 自连结
找出每个商品种类当中售价高于该类商品的平均售价的商品.

上下分别是关联子查询和自连结
```sql
SELECT product_name, product_type, sale_price
  FROM product AS P1
 WHERE sale_price > (SELECT AVG(sale_price)
                       FROM product AS P2
                      WHERE P1.product_type = P2.product_type
                      GROUP BY product_type);
                      
SELECT  P1.product_name
       ,P1.product_type
       ,P1.sale_price
       ,P2.avg_price
       ,P1.product_id
  FROM product AS P1
 INNER JOIN 
   (SELECT product_type,AVG(sale_price) AS avg_price 
      FROM product 
     GROUP BY product_type) AS P2 
    ON P1.product_type = P2.product_type
 WHERE P1.sale_price > P2.avg_price;
```
```
打孔器	办公用品	500
运动T恤	衣服	       4000
菜刀	厨房用具	3000
高压锅	厨房用具	6800

打孔器	办公用品	500	300.0000	0002
运动T恤	衣服	       4000	2500.0000	0003
菜刀	厨房用具	3000	2795.0000	0004
高压锅	厨房用具	6800	2795.0000	0005
```
### 自然连结
当两个表进行自然连结时, 会按照两个表中都包含的列名来进行等值内连结, 此时无需使用 ON 来指定连接条件.
```sql
SELECT *  FROM shopproduct NATURAL JOIN product

SELECT  SP.product_id,SP.shop_id,SP.shop_name,SP.quantity
       ,P.product_name,P.product_type,P.sale_price
       ,P.purchase_price,P.regist_date  
  FROM shop_product AS SP 
 INNER JOIN product AS P 
    ON SP.product_id = P.product_id
```
上下两条得到的结果相同
#### 使用自然连结还可以求出两张表或子查询的公共部分
**但是**

在进行自然连结时, 来自于 product 和 product_copy 这一行数据在进行比较时, 实际上是在逐字段进行等值连结, 回忆我们在IS NULL,IS NOT NULL的缺失值的比较方法就可得知, 两个缺失值用等号进行比较, 结果不为真. 而连结只会返回对连结条件返回为真的那些行.

比如说copy和product内容完全相同，自然连结的时候，regist_date为null的就不会被显示出来

解决办法，避开null的
```sql
SELECT * 
  FROM (SELECT product_id, product_name
          FROM product ) AS A 
NATURAL JOIN 
   (SELECT product_id, product_name 
      FROM product_copy1) AS B;
```
### 外连结
左连结会保存左表中无法按照 ON 子句匹配到的行, 此时对应右表的行均为缺失值; 

右连结则会保存右表中无法按照 ON 子句匹配到的行, 此时对应左表的行均为缺失值; 

而全外连结则会同时保存两个表中无法按照 ON子句匹配到的行, 相应的另一张表中的行用缺失值填充.
```sql
-- 左连结     
FROM <tb_1> LEFT JOIN <tb_2> ON <condition(s)>
-- 右连结     
FROM <tb_1> RIGHT JOIN <tb_2> ON <condition(s)>
-- 全外连结
FROM <tb_1> FULL JOIN <tb_2> ON <condition(s)>
```
关键词OUTER可以省略

举例：统计每种商品分别在哪些商店有售, 需要包括那些在每个商店都没货的商品.
```sql
SELECT SP.shop_id
       ,SP.shop_name
       ,SP.product_id
       ,P.product_name
       ,P.sale_price
  FROM product AS P
  LEFT JOIN shop_product AS SP
    ON SP.product_id = P.product_id;
```
```
000A	东京	0001	T恤衫	1000
000A	东京	0002	打孔器	500
000A	东京	0003	运动T恤	4000
000B	名古屋	0002	打孔器	500
000B	名古屋	0003	运动T恤	4000
000B	名古屋	0004	菜刀	3000
000B	名古屋	0006	叉子	500
000B	名古屋	0007	擦菜板	880
000C	大阪	0003	运动T恤	4000
000C	大阪	0004	菜刀	3000
000C	大阪	0006	叉子	500
000C	大阪	0007	擦菜板	880
000D	福冈	0001	T恤衫	1000
			高压锅	6800
			圆珠笔	100
```
- 外连结要点 1: 选取出单张表中全部的信息
- 外连结要点 2:使用 LEFT、RIGHT 来指定主表

使用 LEFT 时 FROM 子句中写在左侧的表是主表,使用 RIGHT 时右侧的表是主表
