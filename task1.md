# Task1 笔记
这里我用的是MySQL+navicat，markdown似乎无法将sql语句中的一些关键词高亮，大坏
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
### 插入
```sql
INSERT INTO product (product_id, product_name, product_type, sale_price, purchase_price, regist_date) 
VALUES ('0006', '叉子', '厨房用具', 500, NULL, '2009-09-20');  

INSERT INTO product VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
```
可以使用INSERT … SELECT 语句从其他表复制数据。
```sql
-- 将商品表中的数据复制到商品复制表中
INSERT INTO productocpy (product_id, product_name, product_type, sale_price, purchase_price, regist_date)
SELECT product_id, product_name, product_type, sale_price, 
purchase_price, regist_date
FROM Product; 
```
我们假设product表现在是个空表，则可以用以下语句插入数据（教程中的例子）
```sql
INSERT INTO product VALUES('0001', 'T恤衫', '衣服', 1000, 500, '2009-09-20');
INSERT INTO product VALUES('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
INSERT INTO product VALUES('0003', '运动T恤', '衣服', 4000, 2800, NULL);
INSERT INTO product VALUES('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');
INSERT INTO product VALUES('0005', '高压锅', '厨房用具', 6800, 5000, '2009-01-15');
INSERT INTO product VALUES('0006', '叉子', '厨房用具', 500, NULL, '2009-09-20');
INSERT INTO product VALUES('0007', '擦菜板', '厨房用具', 880, 790, '2008-04-28');
INSERT INTO product VALUES('0008', '圆珠笔', '办公用品', 100, NULL, '2009-11-11');
```
**但是**，当表中已经有了数据，比如已经按插入那里的两条语句，插入了数据，再执行上面的，会报错1062 - Duplicate entry，因为主键是**唯一**的，重复插入一样的就会有错。想修改可以用update。

## 习题答案
```sql
-- 1
CREATE TABLE Addressbook (
regist_no      INTEGER		NOT NULL,
name			VARCHAR(128)	NOT NULL,
address		VARCHAR(128)	NOT NULL,
tel_no		CHAR(10),
mail_address 	CHAR(10),
PRIMARY KEY (regist_no)
);

-- 2
ALTER TABLE Addressbook ADD COLUMN postal_code CHAR(8) NOT NULL;

-- 3
DROP TABLE Addressbook;

-- 4
-- 删除的表无法使用命令进行恢复，重新用语句创建表
```
