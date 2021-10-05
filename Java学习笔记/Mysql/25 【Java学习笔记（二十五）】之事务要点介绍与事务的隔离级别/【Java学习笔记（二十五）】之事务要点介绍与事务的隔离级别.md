## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	事务
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;事务管理着一个包含多个步骤的业务操作，这些步骤要么同时成功，要么同时失败。当这些步骤中出现异常错误时，事务会及时地回滚到业务初始状态，防止出现逻辑错误。
<br>


### （二）	操作
#### 1.	开启事务

```sql
start transaction;
```
<br>



#### 2.	回滚
`rollback;` 回滚到开启事务的位置、
<br>



#### 3.	提交

```sql
commit;
```
<br>



### （三）	事务自动提交与手动提交
#### 1.	自动提交
&nbsp;  &nbsp;  &nbsp;  &nbsp;一条DML（增删改）语句会自动提交一次事务
<br>



#### 2.	手动提交
&nbsp;  &nbsp;  &nbsp;  &nbsp;需要先开启事务，再提交
<br>



#### 3.	修改事务的默认提交方式
##### （1）	查看事务的默认提交方式：

```sql
SELECT @@autocommit；
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;1 代表自动提交， 0代表手动提交
<br>



##### （2）	修改默认提交方式

```sql
SET @@autocommit = 0；
```
<br>



### （四）	事务原理

&nbsp;  &nbsp;  &nbsp;  &nbsp;事务开启之后, 所有的操作都会临时保存到事务日志中, 事务日志只有在得到 commit 命令才会同步到数据表中，其他任何情况都会清空事务日志(rollback，断开连接)
<br>



### （五）	事务的步骤

&nbsp;  &nbsp;  &nbsp;  &nbsp;1.客户端连接数据库服务器，创建连接时创建此用户临时日志文件

&nbsp;  &nbsp;  &nbsp;  &nbsp;2．  开启事务以后，所有的操作都会先写入到临时日志文件中

&nbsp;  &nbsp;  &nbsp;  &nbsp;3． 所有的查询操作从表中查询，但会经过日志文件加工后才返回

&nbsp;  &nbsp;  &nbsp;  &nbsp;4． 如果事务提交则将日志文件中的数据写到表中，否则清空日志文件。
<br>



### （六）	回滚点
#### 1.	回滚点概述

&nbsp;  &nbsp;  &nbsp;  &nbsp;在某些成功的操作完成之后，后续的操作有可能成功有可能失败，但是不管成功还是失败，前面操作都已经成功，可以在当前成功的位置设置一个回滚点。可以供后续失败操作返回到该位置，而不是返回所有操作，这个点称之为**回滚点**

&nbsp;  &nbsp;  &nbsp;  &nbsp;设置回滚点可以让我们在失败时回到回滚点，而不是回到事务开启的位置。
<br>



#### 2.	回滚点操作

作用  |语句
-|-
设置回滚点  |savepoint 名字
回到回滚点  |rollback to 名字

<br>




## 二．	事务的四大特征ACID
### （一）	原子性(Atomicity)
&nbsp;  &nbsp;  &nbsp;  &nbsp;  事务是一个整体，不可分割的最小操作单位，要么同时成功，要么同时失败。
<br>



### （二）	持久性(Durability)
&nbsp;  &nbsp;  &nbsp;  &nbsp;当事务提交或回滚后，数据库会持久化地保存数据。
<br>



### （三）	隔离性(Isolation)
&nbsp;  &nbsp;  &nbsp;  &nbsp;多个事务之间是相互独立的。
<br>



### （四）	一致性(Consistency)
&nbsp;  &nbsp;  &nbsp;  &nbsp;事务在操作前后的数据总量是不变的，若变动了，则说明有异常出现。

<br>



## 三．	事务的隔离级别
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;多个事务之间是独立的，但当多个事务操作同一批数据时，会引发并发访问的问题，通过设置事务的隔离级别来解决。
<br>



### （二）	存在的问题
#### 1.	脏读
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个事务读取到另一个事务中没有提交的数据。如一个事务A中改变的数据，被事务B读取了，但是事务A之后将事务回滚了，则事务B读取到的数据就是错误的。
<br>



#### 2.	不可重复读（虚读）
&nbsp;  &nbsp;  &nbsp;  &nbsp;在同一个事务中，两次读取到的数据内容不同，这是事务UPDATE时引发的问题。如事务A中改变的数据，事务B第一次读取结果为R1，事务A将业务回滚后，事务B第二次读取结果为R2，在事务B这一个事务中，读取同一数据结果不同，容易混淆、
<br>



#### 3.	幻读
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个事务中两次读取的数据的 数量 不一致，要求在一个事务多次读取的数据的数量是一致的，这是 insert 或  delete时引发的问题。
<br>



### （三）	四种隔离级别
#### 1.	read uncommitted(读未提交)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生的问题：脏读，不可重复读，幻读
<br>



#### 2.	read Committed(读已提交)
&nbsp;  &nbsp;  &nbsp;  &nbsp;当一个事务提交后，该事务的数据才能够读取到。

&nbsp;  &nbsp;  &nbsp;  &nbsp;产生的问题：不可重复读，幻读

&nbsp;  &nbsp;  &nbsp;  &nbsp;解决的问题：脏读

&nbsp;  &nbsp;  &nbsp;  &nbsp;Oracle默认级别
<br>



#### 3.	repeatable read(可重复读)
&nbsp;  &nbsp;  &nbsp;  &nbsp;在一个事务中，可以重复地读取一个数据且该数据不会改变。

&nbsp;  &nbsp;  &nbsp;  &nbsp;产生的问题：幻读

&nbsp;  &nbsp;  &nbsp;  &nbsp;解决的问题：脏读，不可重复读

&nbsp;  &nbsp;  &nbsp;  &nbsp;	Mysql默认级别
<br>



#### 4.	serializable(串行化)
&nbsp;  &nbsp;  &nbsp;  &nbsp;类似于锁的操作，当一个事务在操作一个表时，其他事务是无法操作这个表的，只能等待这个表被释放。

&nbsp;  &nbsp;  &nbsp;  &nbsp;不会产生前面的三种问题。

&nbsp;  &nbsp;  &nbsp;  &nbsp;从上到下，隔离级别越高，性能越差，安全性越高。
<br>



### （四）	查询隔离级别

```sql
SELECT @@tx_isolation;
```
<br>



### （五）	设置隔离级别

```sql
set global transaction isolation level 级别字符串；
```






