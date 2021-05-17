## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	约束
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;对表中的数据进行限定，保证数据的正确性，有效性和完整性。
<br>

### （二）	分类
<br>



1.	主键约束	primary key

2.	非空约束	not null

4.	唯一约束	unique
5.	外键约束	foreign key

<br>


## 二．	主键约束
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;非空且唯一，一张表只能有一个字段为主键，主键是表的唯一标识。
<br>

### （二）	创建表时添加主键
<br>

```cpp
CREATE TABLE stu(
		Id INT PRIMARY KEY,
		Name VARCHAR(20)
);
```
<br>


### （三）	修改主键
<br>


```cpp
ALTER TABLE stu MODIFY id INT PRIMARY KEY;
```

### （四）	删除主键

```cpp
ALTER TABLE stu DROP PRIMARY KEY;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，主键的删除与一般的删除不同，直接DROP PRIMARY KEY，因为只有一个字段为主键。
<br>


### （五）	自动增长auto_increment
&nbsp;  &nbsp;  &nbsp;  &nbsp;某一列数值型，使用auto_increment可以使值自动增长，一般都配合着主键使用。


<br>


## 三．	非空约束
### （一）	创建表时添加约束

```cpp
CREATE TABLE stu(
		Id INT,
		name VARCHAR(20) NOT NULL
);
```
<br>


### （二）	修改表的非空约束

```cpp
ALTER TABLE stu MODIFY name VARCHAR(20) NOT NULL;
```
<br>


### （三）	删除表的非空约束

```cpp
ALTER TABLE stu MODIFY name VARCHAR(20);
```

<br>


## 四．	唯一约束
&nbsp;  &nbsp;  &nbsp;  &nbsp;某一列数据不能重复
<br>


### （一）	创建表时设置唯一约束

```cpp
CREATE TABLE stu(
		Id INT,
		number VARCHAR(20) UNIQUE
);
```
<br>


### （二）	修改唯一约束

```cpp
ALTER TABLE stu MODIFY number VARCHAR(20) UNIQUE;
```
<br>


### （三）	删除唯一约束

```cpp
ALTER TABLE stu DROP INDEX number；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;注意，这里的删除与一般的删除不同，用INDEX 接 字段名。
<br>



## 五．	外键约束
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;外键就是和其他的表产生关联，外键所存储的数据是引用于主表（即另一个表）中的某一列数据。即外键所在的表为副表，外键所在字段引用主表的某一字段的数据。
<br>



### （二）创建表时添加外键

```cpp
CREATE TABLE 表名(
		外键列, --外键列的定义
		CONSTRAINT 外键名称 FOREIGN KEY (外键列名称) REFERENCES 主表名称(主表列名称)
);
```
<br>


### （三）修改外键

```cpp
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称（主表列名称）;
```
<br>


### （四）	删除外键

```cpp
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```
<br>


### （五）	外键的级联操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们修改主表的数据时，希望副表中的外键数据也跟着同步更新，那么就需要级联操作。
<br>


#### 1.	级联更新

```cpp
ON UPDATE CASCADE
```
<br>


#### 2.	级联删除

```cpp
ON DELETE CASCADE
```
<br>


#### 3.	修改级联关系

```cpp
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY （外键字段名称）REFERENCES 主表名称（主表列名称）ON UPDATE CASCADE ON DELETE CASCADE;
```
<br>


#### 4.	创建时添加级联关系

```cpp
CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称（主表列名称）ON UPDATE CASCADE ON DELETE CASCADE
```








