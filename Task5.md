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
T恤衫	衣服	1000	1
打孔器	办公用品	500	2
运动T恤	衣服	4000	2
菜刀	厨房用具	3000	3
高压锅	厨房用具	6800	4
叉子	厨房用具	500	1
擦菜板	厨房用具	880	2
圆珠笔	办公用品	100	1
```
