## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

<br>

## 一．	Mysql服务的启动与停止
### （一）	Mysql服务的启动
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以通过cmd输入services.msc打开服务的窗口，手动停止Mysql的服务，也可以用命令的方式：

&nbsp;  &nbsp;  &nbsp;  &nbsp;首先通过管理员权限打开DOS命令窗口，输入以下命令启动Mysql服务：

```cpp
net start mysql
```
<br>

### （二）	Mysql服务的停止
&nbsp;  &nbsp;  &nbsp;  &nbsp;输入以下命令停止Mysql服务：

```cpp
net stop mysql
```

<br>

## 二．	Mysql的登陆与退出
### （一）	Mysql的登陆
<br>

登陆格式1：

```cpp
mysql -uroot -proot
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;-u代表着用户名，-p代表着密码。
<br>

登陆格式2：

```cpp
mysql -uroot -p
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;之后会让你输入密码，但密码不会明文显示
<br>

登录格式3：

```cpp
mysql -hip地址 -u用户名 -p密码
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;连接到某个ip地址的Mysql数据库中
<br>

登陆格式4：

```cpp
mysql –host=ip地址 –user=用户名 –password=密码
```
<br>


### （二）	Mysql的退出
&nbsp;  &nbsp;  &nbsp;  &nbsp;输入 quit或者exit即可退出。

<br>

## 三．	数据库管理系统
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;数据库管理系统（DataBase Management System, DBMS），是一种操作和管理数据库的大型软件，用于建立，使用和维护数据库。用户通过数据库管理系统访问数据库中表内的数据。
<br>

### （二）	DBMS，数据库，表的关系
&nbsp;  &nbsp;  &nbsp;  &nbsp;DBMS可以管理多个数据库，一般一个应用对应一个数据库，一个数据库中有多个表，每个表中有多条数据。

<br>

## 四．	SQL
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;SQL全名为Structured Query Language，结构化查询语言，用于在数据库中进行操作。
<br>

### （二）	SQL用途
&nbsp;  &nbsp;  &nbsp;  &nbsp;SQL是所有关系型数据库的查询规范，是一种通用的数据库操作语言，但是不同的数据库的SQL语言依然有些区别。
<br>

### （三）	SQL语句的分类
<br>

1.	Data Definition Language(DDL)，数据定义语言，用于操作数据库和表。

2.	Data Manipulation Language(DML)，数据操纵语言，用于对表中的数据增删改。

3.	Data Query Language(DQL)，数据查询语言，用于对表中的数据查询。

5.	Data Control Language(DCL)，数据控制语言，用于设置用户权限。
<br>

### （四）	SQL的语法
<br>

1.	每条语句以分号结尾

2.	SQL中不区分大小写，建议关键字用大写

4.	3种注释：

```cpp
单行注释： --空格
多行注释： /* */
Mysql中单行注释：#
```
<br>


## 五．	DDL（数据定义语言）
<br>

### （一）	操作数据库（CURD）
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;操作数据库一般由增删改查四种操作，增是C(Create)，创建；U是（Updata），修改；R是Retrieve，查询；D是Delete，删除。接下来依次介绍四种操作数据库的方式。
<br>

#### 1.	增（Create）
<br>

##### （1）	创建数据库

```cpp
CREATE DATABASE 数据库名称；
```
<br>

##### （2）	先判断是否存在，再创建数据库

```cpp
CREATE DATABASE IF NOT EXISTS 数据库名称；
```
<br>

##### （3）	创建数据库并制定使用的字符集

```cpp
CREATE DATABASE 数据库名称 CHARACTER SET gbk；
```

<br>

#### 2.	查（Retrieve）
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询数据库的信息
<br>

#####  （1）	查询所有数据库的名称

```cpp
SHOW DATABASES;
```
<br>

##### （2）	查询某个数据库的创建语句或字符集

```cpp
SHOW CREATE DATABASE 数据库名称；
```
<br>

#### 3.	改（Updata）
##### （1）	修改数据库的字符集

```cpp
ALTER DATABASE 数据库名称 CHARACTER SET 字符集名称；
```
<br>

#### 4.	删（Delete）
##### （1）	删除数据库

```cpp
DROP DATABASE 数据库名称；
```
<br>

##### （2）	判断是否存在，再删除

```cpp
DROP DATABASE IF EXISTS 数据库名称；
```
<br>

#### 5.	查看当前使用的数据库

```cpp
SELECT DATABASE();
```
<br>

#### 6.	进入使用某个数据库

```cpp
USE 数据库名；
```
<br>


### （二）	操作表（CRUD）
&nbsp;  &nbsp;  &nbsp;  &nbsp;操作表同样是增删改查。
<br>


#### 1.	C(Create) 增
##### （1）	创建表

```cpp
CREATE TABLE 表名(
		列名1，数据类型1，
		列名2，数据类型2，
		…。
);
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，最后一行，不需要加逗号！
<br>

##### （2）	数据类型

```cpp
整数 ：int
小数：double(5, 2) ，表示5位小数，保留小数点后2位。
日期：date，只包含年月日，yyyy-MM-dd
日期：datetime，还包含分秒， yyyy-MM-dd HH:mm:ss
时间戳：timestamp，yyyy-MM-dd HH:mm:ss
字符串：varchar(20)，最大20个字符串
```

<br>

##### （3）	复制表

```cpp
CREATE TABLE 表名 like 被复制的表名
```
<br>


#### 2.	R(Retrieve) 查询
##### （1）	查询某个数据库中的所有表

```cpp
SHOW TABLES;
```
<br>

##### （2）	查询表的结构

```cpp
DESC 表名；
```
<br>

#### 3.	U(Update) 改
##### （1）	修改表名

```cpp
ALTER TABLE 表名 RENAME TO 新的表名；
```
<br>

##### （2）	修改表的字符集

```cpp
ALTER TABLE 表名 CHARACTER SET 字符集名称；
```
<br>

##### （3）	添加一列

```cpp
ALTER TABLE 表名 ADD 列名 数据类型；
```
<br>

##### （4）	修改列名，类型

```cpp
ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型；
ALTER TABLE 表名 MODIFY 列名 新数据类型；
```
<br>

##### （5）	删除列

```cpp
ALTER TABLE 表名 DROP 列名；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;总这些操作可以看出，修改表的命令首先都是ALTER TABLE 表名，其次跟着具体的操作命令。
<br>

#### 5．D（Delete）删

```cpp
DROP TABLE 表名；
DROP TALBE IF EXISTS 表名；
```
<br>


## 六．	DML（数据操作语言）
&nbsp;  &nbsp;  &nbsp;  &nbsp;DML用于操作表中的数据，同样是增删改查。
<br>

### （一）	添加数据

```cpp
INSERT INTO 表名（列名1，列名2…）VALUES（值1， 值2…）；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;如果表名后没有添加列名，则默认给所有列添加值。

&nbsp;  &nbsp;  &nbsp;  &nbsp;除了数字，其他类型都要用引号。
<br>

### （二）	删除数据

```cpp
DELETE FROM 表名  [WHERE 条件]；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;若不加条件，则删除表中全部数据。如果需要删除表中所有数据，则使用 

```cpp
TRUNCATE TABLE 表名
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;先删除表，再创建一张一样的表。
<br>

### （三）	修改数据

```cpp
UPDATE 表名 SET 列名1=值1， 列名2=值2  WHERE 条件
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;若不加条件，则所有记录都会被修改。
<br>


## 七．	DQL（数据查询语言）
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;DQL用于查询表中的数据，
<br>

### 1.	基础查询
#### （1）	多个字段的查询

```cpp
SELECT 字段名1，字段名2 … FROM 表名；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;若是查询所有字段，使用*。

```cpp
select * from student3;
select name,address from student3;
```
<br>

#### （2）	计算数值型列
&nbsp;  &nbsp;  &nbsp;  &nbsp;四则运算直接计算。

```cpp
Ifnull(表达式1，表达式2)
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;ifnull（）判断表达式1是否为null，如果是，替换为表达式2.
<br>

#### （3）	去除重复
Distinct

```cpp
select distinct address from student3;
```
<br>

#### （4）	起别名
&nbsp;  &nbsp;  &nbsp;  &nbsp;直接在字段名添加AS 别名，或者不加AS也可以。

```cpp
select name, math 数学, english 英语, math+ifnull(english,0) 总分 from student3;
```
<br>

### 2.	条件查询
&nbsp;  &nbsp;  &nbsp;  &nbsp;在WHERE子句后面加条件，条件运算符如下：
#### （1）	比较运算符
<br>


比较运算符  |说明
-|-
> 、< 、<= 、>= 、= 、<>  <>|在 SQL 中表示不等于，在 mysql 中也可以使用!=，没有==
BETWEEN...AND  |在一个范围之内，如：between 100 and 200相当于条件在 100 到 200 之间，包头又包尾
IN( 集合)  |集合表示多个值，使用逗号分隔
LIKE ' 张%'  |模糊查询，
IS NULL  |查询某一列为 NULL 的值，注：不能写=NULL

LIKE模糊查询：

&nbsp;  &nbsp;  &nbsp;  &nbsp;通配符 % : 匹配多个字符

&nbsp;  &nbsp;  &nbsp;  &nbsp;通配符 _ : 匹配一个字符

匹配王姓学生：

```cpp
SELECT * FROM student WHERE name LIKE ‘王%’;
```

匹配姓名第二个字为王

```cpp
SELECT * FROM student WHERE name LIKE ‘_王%’;
```
<br>


#### （2）	逻辑运算符
<br>


逻辑运算符  |说明
-|-
and  或 &&  |与，SQL 中建议使用前者，后者并不通用。
or  或  |  或
not  或 !  |非
<br>

### 3.	排序查询
&nbsp;  &nbsp;  &nbsp;  &nbsp;将数据按照某种顺序排列
#### （1）语法

```cpp
ORDER BY  排序字段1 排序方式1， 排序字段2 排序方式2
```
<br>

#### （2）排序方式

```cpp
ASC：默认升序
DESC：降序
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;多个排序条件，当前面条件值相同时，才会判断第二条件
<br>


按数学成绩升序排列：
```cpp
select * from student3 order by math;
```



先按数学升序排列，当数学相同时，按英语的升序排列：
```cpp
select * from student3 order by math asc, english asc;
```

<br>


### 4.	聚合函数
&nbsp;  &nbsp;  &nbsp;  &nbsp;将一列数据作为整体，进行纵向的计算。
#### （1）	count
&nbsp;  &nbsp;  &nbsp;  &nbsp;计算数据的个数，一般选择非空的列（主键）

```cpp
select count(math) from student3;
```
<br>

#### （2）	max
&nbsp;  &nbsp;  &nbsp;  &nbsp;计算最大值
<br>

#### （3）	min
&nbsp;  &nbsp;  &nbsp;  &nbsp;计算最小值
<br>

#### （4）	sum
&nbsp;  &nbsp;  &nbsp;  &nbsp;计算数据的和
<br>

#### （5）	avg
&nbsp;  &nbsp;  &nbsp;  &nbsp;计算数据的均值 

&nbsp;  &nbsp;  &nbsp;  &nbsp;聚合函数忽略null值，因此尽量选择非空的列进行计算或者使用ifnull()函数。
<br>

### 5.	分组查询
#### （1）ORDER BY
&nbsp;  &nbsp;  &nbsp;  &nbsp;分组查询是指使用 GROUP BY  语句对查询信息进行分组，相同数据作为一组

```sql
SELECT  字段 1, 字段 2... FROM  表名 GROUP BY  分组字段；
```

```sql
select sex, avg(math), count(id) from student3 group by sex;
```
<br>

#### （2）添加条件
&nbsp;  &nbsp;  &nbsp;  &nbsp;添加WHERE条件和HAVING条件。

```sql
select sex, avg(math), count(id) from student3  where math > 70 group by sex;

select sex, avg(math), count(id) from student3 where math > 70 group by sex having count(id) > 2;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意：WHERE语句出现在group by之前，HAVING在group by 之后。
<br>

#### （3）WHERE 和 HAVING的区别
&nbsp;  &nbsp;  &nbsp;  &nbsp;WHERE在分组前进行限定，如果不满足条件则不参加分组，HAVING在分组之后进行限定，如果不满足条件，不会被查询出来。

&nbsp;  &nbsp;  &nbsp;  &nbsp;WHERE后不可以跟聚合函数，HAVING后可以加聚合函数。
<br>


### 6.	分页查询
#### （1）	语法

```sql
limit 开始的索引，每页查询的条数；
```

```sql
SELECT *| 字段列表 [as  别名] FROM  表名 [WHERE  子句] [GROUP BY  子句][HAVING  子句][ORDER BY  子
句][LIMIT  子句];
```
<br>

#### （2）	公式
&nbsp;  &nbsp;  &nbsp;  &nbsp;开始的索引= （当前的页码-1）* 每页查询的条数

```sql
SELECT * FROM student LIMIT 0,3 –第一页
```

<br>

#### （3）	注意
&nbsp;  &nbsp;  &nbsp;  &nbsp;limit只是Mysql的专有语言


<br>


## 八．	DCL（数据控制语言）
&nbsp;  &nbsp;  &nbsp;  &nbsp;DCL用于管理数据库中的用户以及对于用户的授权。
<br>




### （一）	添加用户

```sql
CREATE USER ‘用户名‘@’主机名’ IDENTIFIED BY ‘密码’；
```
<br>



### （二）	删除用户

```sql
DROP USER ‘用户名’@‘主机名’；
```
<br>



### （三）	查询用户
#### 1.	切换到mysql数据库

```sql
USE mysql;
```
<br>



#### 2.	查询USER表

```sql
SELECT * FROM USER;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;通配符%表示任意的意思，用于主机名表示可以在任意主机登录数据库，可以用于创建用户时使用。
如：

```sql
CREATE USER ‘用户名’@‘%’ IDENTIFIED BY ‘密码’；
```
<br>



### （四）	修改普通用户密码

```sql
UPDATE USER SET PASSWORD = PASSWORD(‘新密码’) WHERE USER = ‘用户名’；
```

```sql
SET PASSWORD FOR ‘用户名’@‘主机名’ = PASSWORD(‘新密码’)；
```
<br>



### （五）	修改root用户密码
&nbsp;  &nbsp;  &nbsp;  &nbsp;在未登录mysql的情况下，打开管理员权限的DOS窗口，输入以下命令：

```sql
mysqladmin -uroot -p password
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;根据提示，设置新密码
<br>



### （六）	忘记root用户密码
#### 1.	先停止mysql服务

```sql
cmd -> net stop mysql
```
<br>



#### 2.	使用无验证方式启动mysql服务

```sql
cmd -> mysqld –skip-grant-tables
```
<br>



#### 3.	直接mysql登录即可，无需密码
<br>



### （七）	权限管理
&nbsp;  &nbsp;  &nbsp;  &nbsp;用户被创建后是没有权限的，需要管理员为用户授权。
#### 1.	查询权限

```sql
SHOW GRANTS FOR ‘用户名’@‘主机名’；
SHOW GRANTS FOR ‘LIST’@’%’;
```
<br>



#### 2.	授予权限

```sql
GRANT 权限列表 ON 数据库名.表名 TO ‘用户名’@‘主机名’；

GRANT SELECT,DELECT, ON db3.account TO ‘list’@‘%’;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;给用户授予所有权限，在任意数据库任意表中：

```sql
GRANT ALL ON  *.* TO ‘用户名’@‘表名’；
```
<br>



#### 3.	撤销权限

```sql
REVOKE 权限列表 ON 数据库名.表名 FROM ‘用户名’@‘主机名’；
```

















