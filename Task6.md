# SQL训练营综合练习题-10道经典题目学习笔记
本笔记为阿里云天池龙珠计划SQL训练营的学习内容，链接为：https://tianchi.aliyun.com/specials/promotion/aicampsql

##下载相关
这里为了方便大家，数据脚本已经给大家准备好啦。
点击下方链接直接下载
创建数据表脚本：http://tianchi-media.oss-cn-beijing.aliyuncs.com/dragonball/SQL/create_table.sql
插入数据脚本：http://tianchi-media.oss-cn-beijing.aliyuncs.com/dragonball/SQL/data.zip

大家下载好脚本后，先在MySQL环境中运行create_table.sql脚本，创建数据表，然后解压下载好的data.zip，解压后目录如下：
```
8-10ccf_offline_stage1_train.sql
6-winequality-white.sql
5-8-10ccf_online_stage1_train.sql
4-macro industry.sql
3-ccf_offline_stage1_test_revised.sql
2-winequality-red.sql
1-9income statement.sql
1-9company operating.sql
1-7market data.sql
```
脚本文件名前面的序号表示用到该数据集的题目序号，例如1-7market data.sql表示第1题和第7题用到了该数据集。
同样的，这里给大家的也是sql脚本，里面是插入数据的语句，大家只需打开后在MySQL环境中运行即可将数据导入到数据表中。
## 练习1
请使用A股上市公司季度营收预测数据集《Income Statement.xls》和《Company Operating.xlsx》和《Market Data.xlsx》，以Market Data为主表，将三张表中的TICKER_SYMBOL为600383和600048的信息合并在一起。只需要显示以下字段。

![}LEJZGW%3_ {I9LPNR5TW`5](https://user-images.githubusercontent.com/55366350/129331634-aaf54073-81c9-426d-b785-1ba3fec34fc9.png)

```sql
SELECT  ins.TICKER_SYMBOL
       ,ins.END_DATE
       ,ins.T_REVENUE
       ,ins.T_COGS
       ,ins.N_INCOME
       ,MD.TICKER_SYMBOL
       ,MD.END_DATE
       ,MD.CLOSE_PRICE
       ,coper.TICKER_SYMBOL
       ,coper.INDIC_NAME_EN
       ,coper.END_DATE
       ,coper.VALUE
FROM `market data` AS MD
 INNER JOIN `income statement` AS ins
	ON MD.TICKER_SYMBOL = ins. TICKER_SYMBOL
 INNER JOIN `company operating` AS coper
  ON MD.TICKER_SYMBOL = coper.TICKER_SYMBOL
WHERE MD.TICKER_SYMBOL = '600383' OR MD.TICKER_SYMBOL = '600048';
```
两个inner join，条件是ticker_symbol相等
