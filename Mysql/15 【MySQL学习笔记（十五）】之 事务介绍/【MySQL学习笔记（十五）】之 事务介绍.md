## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	事务

### （一）	ACID

#### 1.	原子性Atomicity

&nbsp;  &nbsp;  &nbsp;  &nbsp; 现实世界中一个不可分割的操作可能对应着数据库中若干条不同的操作，数据库中一条操作也可能被分解为若干个步骤。原子性的意思是某些操作是不可分割的整体，要做全做，要么全不做。

<br>


#### 2.	一致性Consistency

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果数据库中的数据符合现实世界中的约束，这些数据就是符合一致性的。数据库本身能解决一部分一致性需求，如主键，唯一索引等等，也可以通过定义触发器来自定义约束条件，保证数据的一致性。更多的需要开发者在业务代码中检查数据的一致性。
<br>


#### 3.	隔离性Isolation

&nbsp;  &nbsp;  &nbsp;  &nbsp; 保证某次的状态转换不会影响到其他的状态转换，这个规则称为隔离性。
<br>


#### 4.	持久性Durability

&nbsp;  &nbsp;  &nbsp;  &nbsp; 当一个状态转换完成后，这个转换的结果将会永久保留，这个规则称为持久性。

<br>


### （二）	事务

&nbsp;  &nbsp;  &nbsp;  &nbsp; 把需要保证原子性，一致性，隔离性，持久性的一个或多个数据库操作称为事务。根据这些操作所执行的不同阶段事务被分为了几个状态：
<br>


#### 1.	活动的
&nbsp;  &nbsp;  &nbsp;  &nbsp; 事务对应的数据库操作正在执行过程中。
<br>


#### 2.	部分提交的
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当事务的最后一个操作执行完成，但是结果还没有刷新到磁盘中。

<br>

#### 3.	失败的
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当事务处于活动的或者部分提交的状态时，可能遇到了某些错误而无法继续执行。
<br>


#### 4.	中止的
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当回滚操作执行完毕后，数据库恢复到了执行事务之前的状态。
<br>


#### 5.	提交的
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当一个处于部分提交状态的事务将修改后的数据都刷新到磁盘中。

<br>


### （三）	MySQL中事务的语法

#### 1.	开启事务

&nbsp;  &nbsp;  &nbsp;  &nbsp; 两种语法开启事务：

<br>

##### （1）	BEGIN [WORK];

&nbsp;  &nbsp;  &nbsp;  &nbsp; 开启事务后，可以继续写若干条语句。
<br>


##### （2）	START TRANSACTION;

&nbsp;  &nbsp;  &nbsp;  &nbsp; 后面还可以跟随几个修饰符，如：

&nbsp;  &nbsp;  &nbsp;  &nbsp; READ ONLY: 标识当前事务是只读事务，不允许修改普通表数据（临时表可以）

&nbsp;  &nbsp;  &nbsp;  &nbsp; READ WRITE: 读写事务

&nbsp;  &nbsp;  &nbsp;  &nbsp; WITH CONSISTENT SNAPSHOT: 启动一致性读。
<br>


#### 2.	提交事务

&nbsp;  &nbsp;  &nbsp;  &nbsp; 开启事务后就可以编写需要放到该事务中的语句了，当最后一条语句写完后就可以提交该事务了，使用COMMIT语句提交即可。
<br>


#### 3.	手动终止事务

&nbsp;  &nbsp;  &nbsp;  &nbsp; ROLLBACK语句代表回滚一个事务。
<br>


#### 4.	自动提交
&nbsp;  &nbsp;  &nbsp;  &nbsp; MySQL中有一个系统变量autocommit，用来自动提交事务，默认“on”开启的，如果不显示地开启一个事务，那么每一条语句都算作是一个独立的事务，这种特性称为事务的自动提交。

#### 5.	隐式提交
&nbsp;  &nbsp;  &nbsp;  &nbsp; 有些特殊的语句会导致事务自动提交，如下所示：

&nbsp;  &nbsp;  &nbsp;  &nbsp; （1）	定义或修改数据库对象的数据定义语言（DDL）

&nbsp;  &nbsp;  &nbsp;  &nbsp; 所谓的数据库对象，就是指数据库，表，视图，存储过程等这些东西，当使用CREATE ,ALTER, DROP等语句修改这些数据库对象时，就会隐式地提交前面语句所属的事务。
<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp; （2）	隐式使用或修改mysql数据库中的表
<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp; （3）	事务控制或关于锁定的语句

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在一个事务没有提交时就重新开启另一个事务，此时会隐式地提交上一个事务。使用LOCK TABLES, UNLOCK TABLES 等锁定的语句也会隐式地提交前面语句所属的事务。

<br>

#### 6.	保存点
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在事务对应的数据库语句中打几个保存点，在调用ROLLBACK语句时可以指定回滚到哪个点，免得直接回滚一整个事务。定义保存点的语法如下：

```java
SAVEPOINT 保存点名称;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 回滚到某个保存点：

```java
ROLLBACK TO 保存点名称;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果不加保存点名称，直接回滚到事务执行之前的状态。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 删除某个保存点：

```java
RELEASE SAVEPOINT 保存点名称;
```
<br>


#### 7.	支持事务的存储引擎
&nbsp;  &nbsp;  &nbsp;  &nbsp; 只有InnoDB和NDB支持事务，如果某个事务包含的语句要修改某个表中的数据，但是该表使用的存储引擎不支持事务，那么对该表所做的修改将无法进行回滚。



