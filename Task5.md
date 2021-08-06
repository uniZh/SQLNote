# SQL训练营窗口函数学习笔记
本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql
## 窗口函数
窗口函数是对数据库数据进行实时分析处理

常规的SELECT语句都是对整张表进行查询，而窗口函数可以让我们有选择的去某一部分数据进行汇总、计算和排序。
```sql
<窗口函数> OVER ([PARTITION BY <列名>]
                     ORDER BY <排序用列名>)  
```
**PARTITON BY**是用来分组

**ORDER BY**是用来排序

举例
```sql
SELECT product_name
       ,product_type
       ,sale_price
       ,RANK() OVER (PARTITION BY product_type
                         ORDER BY sale_price) AS ranking
  FROM product  
```
```
T恤衫	衣服	        1000	1
打孔器	办公用品	500	2
运动T恤	衣服	        4000	2
菜刀	厨房用具	3000	3
高压锅	厨房用具	6800	4
叉子	厨房用具	500	1
擦菜板	厨房用具	880	2
圆珠笔	办公用品	100	1
```
### 专用窗口函数
- RANK函数

计算排序时，如果存在相同位次的记录，则会跳过之后的位次。

例）有 3 条记录排在第 1 位时：1 位、1 位、1 位、4 位……

- DENSE_RANK函数

同样是计算排序，即使存在相同位次的记录，也不会跳过之后的位次。

例）有 3 条记录排在第 1 位时：1 位、1 位、1 位、2 位……

- ROW_NUMBER函数

赋予唯一的连续位次。

例）有 3 条记录排在第 1 位时：1 位、2 位、3 位、4 位
### 聚合函数在窗口函数上的使用
```sql
SELECT  product_id
       ,product_name
       ,sale_price
       ,SUM(sale_price) OVER (ORDER BY product_id) AS current_sum
       ,AVG(sale_price) OVER (ORDER BY product_id) AS current_avg  
  FROM product;  
```
分别是从上到下求和，从上到下求平均，sum的表示每一行都是前面的price求和，avg表示每一行都是上面的求和再除以**当前**（自己动态变化）有多少行
## 窗口函数的的应用 - 计算移动平均
```sql
<窗口函数> OVER (ORDER BY <排序用列名>
                 ROWS n PRECEDING )  
                 
<窗口函数> OVER (ORDER BY <排序用列名>
                 ROWS BETWEEN n PRECEDING AND n FOLLOWING)
```
PRECEDING（“之前”）， 将框架指定为 “截止到之前 n 行”，加上自身行

FOLLOWING（“之后”）， 将框架指定为 “截止到之后 n 行”，加上自身行

BETWEEN 1 PRECEDING AND 1 FOLLOWING，将框架指定为 “之前1行” + “之后1行” + “自身”

示例：
```sql
SELECT  product_id
       ,product_name
       ,sale_price
       ,AVG(sale_price) OVER (ORDER BY product_id
                               ROWS 2 PRECEDING) AS moving_avg
       ,AVG(sale_price) OVER (ORDER BY product_id
                               ROWS BETWEEN 1 PRECEDING 
                                        AND 1 FOLLOWING) AS moving_avg  
  FROM product;
```
ROWS 2 PRECEDING：

![01ccb5c849ce029db50a391bed1edacd_O1CN01l9qlat1JNN3u15JEj_!!6000000001016-2-tps-898-322](https://user-images.githubusercontent.com/55366350/128451095-68a67382-dbfc-4a55-8412-933257281571.png)

ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING：

![f7436f5d31ef9f56a0144dd52fb3a3e5_O1CN01aO6L4k1IiboQy4j3c_!!6000000000927-2-tps-920-323](https://user-images.githubusercontent.com/55366350/128451110-5aeab58f-3de5-47b2-b516-a4cae3429fbf.png)

- 原则上，窗口函数只能在SELECT子句中使用。
- 窗口函数OVER 中的ORDER BY 子句并不会影响最终结果的排序。其只是用来决定窗口函数按何种顺序计算。
## GROUPING运算符
常规的GROUP BY 只能得到每个分类的小计，有时候还需要计算分类的合计，可以用 ROLLUP关键字。
```sql
SELECT  product_type
       ,regist_date
       ,SUM(sale_price) AS sum_price
  FROM product
 GROUP BY product_type, regist_date WITH ROLLUP  
```
![4e9d56e711976efbfd4337a6b043e399_O1CN01a2wvRr20h5aecQ6Zq_!!6000000006880-2-tps-416-219](https://user-images.githubusercontent.com/55366350/128452863-7d509173-4681-40d0-a0e6-7abf3d4a326e.png)
### COALESCE函数
将NULL值替换成其他值
```sql
coalesce(success_cnt, 1)
```
当success_cnt 为null值的时候，将返回1，否则将返回success_cnt的真实值

### MySQL中不能使用 NULLS FIRST
