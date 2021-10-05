## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	多表查询
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;所谓的多表查询就是在多个表中同时查询，如下所示：

```sql
SELECT * FROM 表1，表2；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这样，会将两个表中的所有情况的组合显示出来，这种现象叫做笛卡尔积，这其中包含着许多无用的数据，需要通过条件过滤掉。
<br>


### （二）多表查询的分类
#### 1. 内连接
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）隐式内连接

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）显示内连接
<br>



#### 2. 外连接
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）左外连接

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）右外连接
<br>



#### 3. 子查询
<br>




## 二．	内连接查询
&nbsp;  &nbsp;  &nbsp;  &nbsp;用左边表的数据取匹配右边表的数据，如果符合条件则显示。内连接只能显示两个表有交集的部分数据。
<br>



### （一）	隐式内连接
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用WHERE条件语句消除无用的数据。

```sql
SELECT  字段名 FROM  左表,  右表 WHERE  条件；
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;在具体的使用时，我们可以为每个表取简短的别名，如果不同表中的字段名有重复的，则使用表的别名作为前缀区分字段名。且SQL语句一般都不会写在一行之中。

如：
<br>



```sql
SELECT 
		t1.name,
		t1.gender,
		t2.name
FROM
		Emp t1,
		Dept t2
WHERE
		t1.’dept_id’ = t2.’id’;
```
<br>



### （二）	显式内连接
&nbsp;  &nbsp;  &nbsp;  &nbsp;显式内连接和隐式内连接功能相同，只是写法不同。INNER可省略，如：

```sql
SELECT 字段列表 FROM 表名1 【INNER】JOIN 表名2 ON 条件；
SELECT * FROM emp INNER JOIN dept ON emp.’dept_id’ = dept.’id’;
```
<br>




## 三．	外连接查询

### （一）	左外连接
&nbsp;  &nbsp;  &nbsp;  &nbsp;外连接同样使用左边表的数据来匹配右边表的数据，如果符合条件则显示。但是外连接在内连接的基础上还可以保证左边表的数据全部显示，即显示左边表的全部数据和交集部分。

OUTER可省略

```sql
SELECT  字段名 FROM  左表 LEFT [OUTER] JOIN  右表 ON  条件；
```
<br>



### （二）	右外连接
&nbsp;  &nbsp;  &nbsp;  &nbsp;用右边表的数据取匹配左边表的数据，如果符合条件的则显示；否则，显示 NULL。

&nbsp;  &nbsp;  &nbsp;  &nbsp;同理，在内连接的基础上保证右表的数据全部显示。即右表数据加交集。

```sql
SELECT  字段名 FROM  左表 RIGHT [OUTER] JOIN  右表 ON  条件；
```
<br>



## 四．	子查询
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;在查询中嵌套查询，称嵌套的查询为子查询。

```sql
SELECT * FROM emp WHERE emp.’salary’ = (SELECT MAX(salary) FROM emp);
```
<br>



### （二）	子查询的结果是单行单列的
&nbsp;  &nbsp;  &nbsp;  &nbsp;单行单列的结果可以作为条件，进行判断。

```sql
SELECT * FROM emp WHERE emp.salary < (SELECT AVG(salary) FROM emp);
```
<br>



### （三）	子查询的结果是多行单列的
&nbsp;  &nbsp;  &nbsp;  &nbsp;多行单列的结果为多个数据，可以使用in来判断。

```sql
SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE name=’a’ OR name = ‘b’ );
```
<br>



### （四）	子查询的结果是多行多列的
&nbsp;  &nbsp;  &nbsp;  &nbsp;子查询可以作为一张虚拟表参与查询。

如：
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用子查询：

```sql
SELECT * FROM dept t1, (SELECT * FROM emp WHERE emp.’join_data’ > ‘2011-11-11’) t2
WHERE t1.id = t2.dept_id;
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;使用内连接达到同样效果：

```sql
SELECT * FROM emp t1, dept t2 WHERE t1.’dept_id’ = t2.’id’ AND t1.’join_data’ > ‘2011-11-11’;
```






