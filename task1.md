# Task1 笔记
这里我用的是MySQL，markdown似乎无法将sql语句中的一些关键词高亮，大坏
## 数据库的创建
创建数据库的时候要记得设置格式，默认是latinxxx的，当数据库有中文内容的时候需要设置utf8格式，如下
```sql
CREATE DATABASE `databasename` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
```
## 表的创建
```sql
CREATE TABLE product(
     product_id CHAR(4) NOT NULL, 
     product_name VARCHAR(100) NOT NULL, 
     product_type VARCHAR(32) NOT NULL, 
     sale_price INTEGER, 
     purchase_price INTEGER, 
     regist_date DATE, 
     PRIMARY KEY(product_id)
 )  ;
 
 CREATE TABLE productins(
     product_id CHAR(4) NOT NULL,
     sale_price INTEGER DEFAULT 0, -- 销售单价的默认值设定为0;
     PRIMARY KEY (product_id)
);  
```
## 命名规则
只能使用半角英文字母、数字、下划线（_）作为数据库、表和列的名称

名称必须以半角英文字母开头
## 4种基本数据类型 及 NOT NULL 和 主键（上面创建表的时候有例子）
### INTEGER 型
用来指定存储整数的列的数据类型（数字型），不能存储小数。
### CHAR 型
用来存储定长字符串，当列中存储的字符串长度达不到最大长度的时候，使用半角空格进行补足，由于会浪费存储空间，所以一般不使用。
### VARCHAR 型
用来存储可变长度字符串，定长字符串在字符数未达到最大长度时会用半角空格补足，但可变长字符串不同，即使字符数未达到最大长度，也不会用半角空格补足。
### DATE 型
用来指定存储日期（年月日）的列的数据类型（日期型）。
### NOT NULL
是非空约束，即该列必须输入数据。
### PRIMARY KEY
是主键约束，代表该列是**唯一**值，可以通过该列取出特定的行的数据。

## 表的删除和更新
**表删除了就无法恢复**，注意！！！
### 删除
```sql
DROP TABLE product;
```
### 添加、删除 列
```sql
 ALTER TABLE product ADD COLUMN product_name_pinyin VARCHAR(100);
 
 ALTER TABLE product DROP COLUMN product_name_pinyin;
```
### 清空表
```sql
TRUNCATE TABLE product;
```
### 更新表
```sql
UPDATE product
   SET sale_price = sale_price * 10,
       purchase_price = purchase_price / 2
 WHERE product_type = '厨房用具';  
 ```
