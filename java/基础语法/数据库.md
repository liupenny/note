# 数据库:

**本质上** 就是一个文件系统,通过标准的**sql**对数据进行curd操作

安装 数据库管理系统:

数据库管理系统大白话就是一个软件

常见的关系型数据库:

**关系型数据库:**

​	存放实体与实体之间的关系的数据库(就是二维表)

​	实体:

​		用户 订单 商品

​	关系:

​		用户拥有订单

​		订单包含商品

**非关系型数据库**:存放的是对象(redis) **No-sql (not only sql)** 

软件名                             厂商                           特点

mysql                              oracle                        开源的数据库

Oracle                            Oracle公司                      收费的大型数据库.

DB2                                   IBM公司                      收费的.银行系统中.

SQLServer                     MS公司                       收费的中大型的数据库.

SyBase  ：已经淡出历史舞台.提供了一个非常专业数据建模的工具PowerDesigner.

SQLite  : 嵌入式的小型数据库,应用在手机端.

**数据库服务器（软件服务器）**

​	安装了数据库管理系统的计算机

## SQL：结构化查询语句，作用：管理数据库

## 数据类型

| Java                      | MYSQL                                                        |
| ------------------------- | ------------------------------------------------------------ |
| byte                      | tinyint                                                      |
| short                     | smallint                                                     |
| int                       | int(☆)                                                       |
| long                      | bigint                                                       |
| float                     | float                                                        |
| double                    | double  doubel(5,2) 最大值是5位数，小数点占2位。999.99       |
| char                      | char(n)   固定长度,不管存什么，都用20字节表示，其他用空格补齐 |
| String                    | varchar(n)  可变长度,若只存3个数，就用3字节表示(☆)           |
| boolean                   | tinyint \| int                                               |
| java.sql.Date             | date日期   java.sql.Date是util.Date的子类                    |
| java.sql.Time             | time时间                                                     |
| java.sql.Timestamp        | timestap, datetime(☆)                                        |
| java,sql,Clob(长文本文件) | text,可只存4G的                                              |
| java,sql,Blob(二进制文件) | blob                                                         |

​     * Oracle的文件类型：BLOB  CLOB

- timestamp类型

```
-- 创建测试表
CREATE TABLE `timestamp_test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(20) DEFAULT NULL,
  // 表的创建时间，要这么写才会自动填入，而不是NULL
  `created_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

```

**SQL的分类：**

|      |              | 操作对象       | 关键词               |
| ---- | ------------ | -------------- | -------------------- |
| DDL  | 数据定义语言 | 数据库，表     | create alter drop    |
| DML  | 数据操作语言 | 记录           | insert,delete,update |
| DQL  | 数据查询语言 |                | Select               |
| DCL  | 数据控制语言 | 用户 事务 权限 | if,grant             |

# **约束：**

为了保证数据的有效性和完整性。

## **单表约束：**

### 1. 主键约束：primary key 

特点：

- 非空，唯一.
- 在开发中一般情况下主键是不具备任何含义，只是用于标识当前记录
- 一张表只能有一个主键,这个主键可以包含多个字段
- auto_increment必须给一个key添加

**方式1:**建表的同时添加约束格式:字段名称 字段类型primary key 。只能给一个字段添加主键，要想给两个字段添加，只能用方式2,3

```
create table pk01(
	id int primary key,
	username varchar (20),
);
```

```
3.删除主键：
alter table 表名 drop primary key;
alter table sort drop primary key;

4.主键自动增长：一般主键是自增长的字段，不需要指定。
实现添加自增长语句,主键字段后加auto_increment(只适用MySQL)
```
**方式2**:建表的同时在**约束区域**添加约束,

​	**所有的字段声明完成之后,就是约束区域了,**

​	格式: primary key(字段1,字段2)

```
create table pk01(
	id int,
	username varchar (20),
	primary key (id)
);

insert into pk01 values (1, 'tom');--成功
insert into pk01 values (1,'tom');--失败Duplicate entry '1' for key ' PRIMARY'

insert into pk01 values (null, 'tom');--失败 Column 'id' cannot be null

```

```
create table pk01(
	id int,
	username varchar (20),
	primary key (id，username)
);
```

**方式3**:建表之后,通过修改表结构添加约束,

```
create table pk02 (
	id int,
	username varchar (20)
);

alter table pk02 add primary key (字段名1,字段名2..);
alter table pk02 add primary key (id,username);

insert into pk02 values (1, 'tom');-
insert into pk02 values (1, 'tomcat');成功，因为现在是两个字段当做联合主键
```

### 2. 唯一约束：unique

被修饰的字段唯一，对null不起作用。一般**用java来维护数据的完整性**

**方式1:**建表的同时添加约束格式:字段名称 字段类型  unique 。只能给一个字段添加主键，要想给两个字段添加，只能用方式2,3

```
create table pk01(
	id int unique,
	username varchar (20) unique,
);
两个字段都是唯一的

insert into un values (1, 'tom');--成功
insert into un values (1, 'kacj');--错误，1重复
insert into un values (null, 'tom');--成功
insert into un values (null, 'rose');--成功,对null不起作用
```

**方式2**:建表的同时在**约束区域**添加约束,

格式: unique(字段1,字段2)  **联合唯一**

**方式3**:建表之后,通过修改表结构添加约束,

```
alter table pk02 add unique(id,username); 联合唯一，是指这两个的组合唯一
alter table pk02 add unique(username); 给一个添加唯一
alter table pk02 add unique(id);  给另一个添加唯一
```
### 3. 非空约束：not null

被修饰过的字段非空

方式（只有一个方式-在创建的时候写）：

```
create table pk01(
	id int not null,
	username varchar (20) not null,
);
```
### 4. 外键约束：foreign key

见多表关系

## 多表的关系

Ø  一对多关系：

客户和订单，分类和商品，部门和员工.

一对多建表原则：在多的一方创建一个字段，字段作为外键指向一的一方的主键.

Ø  多对多关系：

学生和课程:

多对多关系建表原则：需要创建第三张表,中间表中至少两个字段，这两个字段分别作为外键指向各自一方的主键.

Ø  一对一关系：

在实际的开发中应用不多.因为一对一可以创建成一张表.

两种建表原则：

唯一外键对应：假设一对一是一个一对多的关系，在多的一方创建一个外键指向一的一方的主键，将外键设置为unique.

主键对应：让一对一的双方的主键进行建立关系.

### 1. 一对多

背景：在开发中,关系中的一方称之为主表或者一表,关系中的多方称之为多表或者从表,

为了表示一对多的关系,一般会在多表的一方添加一个字段,字段名称自定义(建议:主表的名称_id)。字段类型一般和主表的主键的类型保持一致,我们称这个字段为**外键**。多个表之间的关系**靠外键约束来维护**

```
格式：alter table 多表名称 add foreign key （外键名称）references  一表名称（主键）

举例：alter table product add foreign key (cno) references category(cid);
```

- 添加了外键约束之后有如下特点：

1. 主表中不能删除从表中已经引用的数据
2. 从表中不能添加主表中不存在的数据

- **开发中处理一对多（☆）**：

  在多表中添加一个外键，名称一般为主表的名称_id,字段类型一般和主表的主键类型一致。为了保证数据的有效性和完整性，在多表的外键上添加外键约束即可

### 2. 多对多

在开发我们一般引入一张中间表,在中间表中存放两张表的主键,一般还会将这两个主键设置成中间表的联合主键,将多对多**拆分成两个一对多.**

为了保证数据的有效性和完整性,在中间表上添加两个外键约束即可。

### 3. 多表查询

- 交叉连接查询(基本不会使用-得到的是两个表的乘积)

语法：

select * from A,B;

1. 内连接查询(使用的关键字 inner join  -- inner可以省略)

\* 隐式内连接：

​    * select a.\*,b.\* from A,B where ab的链接条件;

\* 显示内连接：

​    * select a.\*,b.\* from A [inner] join B on ab连接的条件;

2. 外连接查询(使用的关键字 outer join -- outer可以省略)

\* 左外连接：left outer join

​	select a.\*,b.\* from A left [outer] join B on 条件;

先展示join左边（a表）的所有数据，根据条件关联查询join右边的表（b）。符合条件则展示出来，不符合则用null展示。

\* 右外连接：right outer join

​	select a.\*,b.\*  from A right [outer] join A on 条件;

先展示join右边（a表）的所有数据，根据条件关联查询join左边的表（b）。符合条件则展示出来，不符合则用null展示。

- 子查询

  在sql语言中，**当一个查询是另一个查询的条件时，称之为子查询**

   

### 练习:

1. 查看用户为张三的订单详情

```
select * from user where user_id = (select id from user where username = '张三')； 
后面这句话的结果只有一个值，所以用=
```

2. 查询出订单的价格大于300的所有用户信息。

```
select * from user where id in (select user_id from orders where price > 300) 
后面那句话的查询结果是(3,3,5,null)，有4条，所以用 in
```

3. 查询订单价格大于300的订单信息及相关用户的信息。

```
 内连接：select orders.*,user.*  from orders,user where user (这里要加取出唯一数据的语句，不然会是笛卡尔积)user.id=orders.id and orders.price>300; 

子查询（将一个表的查询结果当做临时表）：select * from orders where price > 300;(所有订单价格>300的订单)  

再去连接：select user.*, tmp.* from  user,tmp where user.id=tmp.user_id;

select user.*, tmp.* from  user,（select * from orders where price > 300）as tmp where user.id=tmp.user_id;
```

给表取别名：

​	表 【as】 别名

- 分页查询

```
分页查询每个数据库的语句是不通用的.

MYSQL:使用limit的关键字.

select * from product limit a,b;  --a:从哪开始,b:查询多少条.

Oracle:使用SQL语句嵌套.

SQLServer:使用top的关键字.
```

## CURD操作

### 对数据库

1. **创建数据库**

```sql
* create database 数据库名;
* create database 数据库名 character set 字符集;
	`create database test character set utf8;`
* create database 数据库名 character set 字符集 collate 校对规则;
```

![img](assets/clip_image001.png) 

2. **查看数据库**

```
* 查看数据库服务器中的所有的数据库:show databases;
* 查看某个数据库的定义的信息:show create database 数据库名;
```

3. **删除数据库**

```
* drop database 数据库名称;
```

4. **修改数据库**

5. ```
   * alter database 数据库名 character set 字符集 collate 校对规则;
   ```

   **其他的数据库操作命令**

```
* 切换数据库：use 数据库名;
* 查看正在使用的数据库:select database();
```

### 对表

1. **创建表**

```
create table 表名(
   字段名 类型(长度) 约束,
   字段名 类型(长度) 约束
);

Java				MYSQL
int					int
float				float
double				double
char/String			char/varchar(char固定长度字符串，varchar可变长度的字符串)
Date				date,time,datetime,timestamp
文件类型				BLOB、TEXT   TEXT指的是文本文件  BLOB二进制文件
     * Oracle的文件类型：BLOB  CLOB

单表约束:
主键约束：primary key
非空约束：not null
唯一约束：unique

```

```
CREATE TABLE 'author' (
    'authorid' char(20) NOT NULL,
    'name' char(20) NOT NULL,
    'age' char(20) NOT NULL,
    'country' char(20) NOT NULL
)DEFAULT CHARSET=utf8 AUTO_INCREMENT=1;
```

**创建一个分类表：**

```
分类ID int类型主键 自动增长
分类名称 字符串类型 长度20

create table category(
    cid int primary key auto_increment,
    cname varchar(20)
);
```

***** 注意：建表之前一定先选择数据库.

2. **查看表**

查看数据库中的所有表：show tables;

查看表结构：desc 表名;

查看建表语句：show create table 表名；（这样就可以用这句话再去创建别的表）

3. **删除表**

drop table 表名;

4. **修改表**

| 用途               | 语句                                                   |
| ------------------ | ------------------------------------------------------ |
| 修改表名           | alter table 旧表名 rename to 新表名；                  |
| 添加列             | alter table 表名 add 列名 类型(长度) 约束;             |
| 修改列名&列的类型. | alter table 表名 change 旧列名 新列名 类型(长度) 约束; |
| 修改列的约束       | alter table 表名 modify 列名 类型(长度) 约束;          |
| 删除字段名         | alter table 表名 drop 列名;                            |
| 修改表的字符集     | alter table 表名 character set 字符集;                 |

### 对表中记录

1. **插入记录**

```
* insert into 表名 (列名1,列名2,列名3..) values  (值1,值2,值3..); -- 向表中插入某些列
* insert into 表名 values (值1,值2,值3..); --向表中插入所有列
  1. 默认插入全部子段
  2. 必须保证values后面内容的 类型和顺序 和表结构中的一致
  3. 若子段类型是数字，可以省略引号，但别省略。值如果是字符串或者日期需要加 '  '.(单引号)

例如：
  1. insert into user values(1, 'tom')   // 第一个字段是数字类型，第二个是字符串
  2. insert into user values('1', 'tom')
```

**cmd下插入中文的乱码的解决：** 修改mysql.ini文件.

​    * 将[mysql]下面的字符集改为gbk

​    * 重启mysql服务.services.msc

2. **修改记录**

\* update 表名 set 字段名=值,字段名1=值1 ... [where 条件]; 

- []表示是可选的

- 名的类型与修改的值要一致.
  修改值得时候不能超过最大长度.
  值如果是字符串或者日期需要加’’.

  ```
  将sid为s002的记录中的sname改成 日用品
  UPDATE sort SET sname='日用品' WHERE sid='s002';
  ```

3. **删除记录**

语法：

-  delete from 表名 [where 条件];  

  表中会维护一个auto_increment自增，要求

  1. 被修饰的字段类型支持自增，一般是Int
  2. 被修饰的字段必须是一个key 一般是primary key

  deletde from 表名: 会删除表中所有记录，但auto_increment还是维持着原来的记录条目数，再新加入一条记录时，自增的字段在auto_increment基础上继续增加。

- truncate  [table]表名

  会直接删掉这个表，再重建一个。所以auto_increment会从1开始。

### 面试⭐：

删除表中所有记录使用delete from 表名; 还是用truncate table 表名;

\* 删除方式：delete 一条一条删除. 而truncate 直接将表删除，重新建表.

\* 事务控制DML,而delete属于DML.如果在一个事务中，delete数据，这些数据可以找回.truncate删除的数据找不回来.

4. 查询记录

- select [distinct]*[列名,列名] from 表 [where 条件].

**select ... from 表名 where 条件 group by 分组字段 having 条件 order by 排序字段 ase|desc**

- where后的条件写法：

​    * > ,<,=,>=,<=,<>

​    * like 使用占位符 _ 和 %  _代表一个字符 %代表任意个字符. 

​        * select * from product where pname like '%新%';

​    * in在某个范围中获得值.

​        * select * from product where pid in (2,5,8);

- 排序查询:

1.查询所有的商品，按价格进行排序.(asc-升序,desc-降序)

2.查询名称有新的商品的信息并且按价格降序排序.

- 聚合函数: 对一列进行计算，返回值是一个,忽略null值。

\* sum(),avg(),max(),min(),count();

1.获得所有商品的价格的总和：

select  sum(price)  from products;

2.获得商品表中价格的平均数：

round(值，保留的小数位)

select  round(avg(price),2)  from products;

3.获得商品表中有多少条记录：

select  count(*)  from products;

- 分组：使用group by

1.根据cno字段分组，分组后统计商品的个数.

select cno,count(*) from products group by cno;

2.根据cno分组，分组统计每组商品的总数量，并且总数量> 200;

select cno,sum(pnum)  from products group by cno having t>200;   

**注意：where和having的区别**

1. where是对分组前的数据进行过滤；Having是对分组后的数据进行过滤
2. where后面不能使用聚合函数，having可以

### 练习

#### 基础查询:

1,查询所有的商品

​	select * from products;

2,查询商品名和商品价格.

​	查看指定的字段,

​	格式: select字段名1,字段名2 from表名

`select pname,price from products;`

3,查询所有商品都有那些价格.

​	去重操作distinct

​	格式: select distinct字段名,字段名2 from表名,select price from products;

`select distinct price from products;`

4,将所有商品的价格+10元进行显示. (别名)

​	可以在查询的结果之上进行运算,不影响数据库中的值

​	给列起别名  格式:字段名 [as] 别名,

```
select price+10 from products;
select price+10 新价格 from products;
select price+10 '新价格' from products;
select price+10 新 价 格 from products;--错误
select price+10 '新 价 格' from products;
select price+10 `新 价 格` from products;
```

#### 条件查询

1.查询商品名称为十三香的商品所有信息:

select * from products where pname ='十三香;

2,查询商品价格>60元的所有的商品信息:

select * from products where price>60;

3.查询商品名称中包含"新"的商品

-- 模糊匹配

-- 格式:  字段名  like "匹配规则";

-- 匹配内容 %表示任何字符出现任意次数**(可以是0次)**.  

​	"龙" 值为龙

​	"%龙" 值以"龙"结尾

​	"龙%" 值以"龙"开头

​	"%龙%" 值包含"龙"

-- 匹配个数 "__"占两个位置,只能匹配单个字符 

​	select * from products where pname like "%新%";

4,查询价格为38,68.98的商品

select * from products where price = 38 or price = 68 or price=98;

select * from products where price in (38,68,98);

#### 

# 总结

多表的创建,        关系的创建 

## 常见的关系:

### 一对多:

在开发中,在多表的一方添加一个外键,外键的名称一般为主表的名称id,字段类型一般和主表的主键的类型保持一致. **外键可以是NULL**

为了保证数据的有效性和完整性,

​	在多表的一方添加外键约束(不是必须的,也可以通过java程序来控制)

​		格式:

​		alter table多表名称add foreign key (外键名称) references主表名称(主键名称)

### 多对多:

在开发中,一般引入一个中间表,在中间表中存放另外两张表的主键,这样就可以将多对多的关系拆分两个一对多的关系为了保证数据的有效性和完整性,

​	在中间表上添加两个外键约束(不是必须的,也可以通过iava程序来控制)

外键约束的特点:

​	1.主表中不能删除从表中已引用的数据

​	2.从表中不能添加主表中不存在的数据

### 一对一:了解

在开发中,例如:person idcard

思路1:将两个实体合二为一

思路2:在一个表上将这个表的主键设置成外键且添加外键约束,

## 多表查询:

### 内连接:

显式的内连接

select a.* ,b.* from a join b on 连接条件:

隐式内连接

select a.*,b.* from a,b where 连接条件;

### 外连接

左外连接

select a.*,b.* from a left join b on  连接条件.

展示a表所有数据,根据条件关联查询b表,满足条件展示,不满足以null值展示