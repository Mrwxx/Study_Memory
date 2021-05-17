## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	MySQL性能优化

### （一）	优化的风险

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  优化不是对一个环境进行的，而是对一个复杂的已投产系统进行的，存在一定的风险，当优化了一个问题后，另一个问题可能就会出现，因此要控制调优带来的问题在可接受的范围内。
<br>


### （二）	优化的需求
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  由于优化存在一定的风险，因此不能单纯为了优化而优化，要根据业务的需求来优化，重点是保证稳定性和业务的可持续性，这两点比性能更加重要。
<br>



### （三）	优化的四个维度

#### 1.	优化的方向

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  数据库优化有两个主要方向：安全与性能。从安全上看是要保障数据的安全性，从性能上看，是要保证数据的高性能访问。
<br>



#### 2.	优化的维度

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  数据库的优化可以从四个维度进行：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  硬件 ： CPU，内存，存储，网络设备
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  系统配置： 服务器系统，数据库服务参数
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  数据库表结构： 高可用，分库分表，读写分离，存储引擎，表设计
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  Sql及索引： sql语句，索引的使用

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  从优化的成本来分析： 硬件 > 系统配置 > 数据库表结构 > SQL及索引；
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  从优化的效果来分析： 硬件 < 系统配置 < 数据库表结构 < SQL即索引；
<br>



### （四）	优化的工具

#### 1.	mysqladmin
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  mysql客户端，可进行管理操作
<br>



#### 2.	mysqlshow
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看shell命令
<br>



#### 3.	show [SESSION | GLOBAL] variables
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看数据库参数信息
<br>



#### 4.	show [SESSION | GLOBAL] STATUS
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看数据库的状态信息
<br>



#### 5.	SHOW ENGINE INNODB STATUS Innodb
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看引擎的所有状态
<br>



#### 6.	information schema
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  获取元数据的方法
<br>



#### 7.	SHOW PROCESSLIST
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看当前所有连接session的状态
<br>



#### 8.	explain
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看查询语句的执行计划
<br>



#### 9.	how index
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  查看表的索引信息
<br>



#### 10.	slow-log
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  记录慢查询语句
<br>



#### 11.	mysqldumpslow
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  分析slowlog文件
<br>



#### 12.	Zabbix
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  监控主机，系统，数据库（部署zabbix监控平台）

<br>


#### 13.	mysqlslap
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  分析慢日志
<br>



#### 14.	sysbench
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  压力测试工具
<br>



#### 15.	workbench
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  管理，备份，监控，分析，优化工具
<br>



#### 16.	mysql profiling
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  统计数据库整体状态工具
<br>



#### 17， Performance Shema mysql
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  性能状态统计的数据
<br>



### （五）	数据库优化思路
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  数据库调优时不需要进行全面，大范围的调优，一般只需要在数据库层面优化即可。

<br>


#### 1.	应急调优的思路
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  当业务突然卡顿，无法正常运行时，需要立刻解决，思路如下：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （1）	show processlist(查看链接session状态)
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （2）	explain(分析查询计划)，show index from table(分析索引)
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （3）	通过执行计划判断，索引问题（有没有，是否合理）或者语句本身问题
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （4）	show status like “%lock%” ，查询锁状态
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （5）	SESSION_ID，杀掉有问题的session
<br>




#### 2.	常规调优的思路
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  对于业务周期性的卡顿，但是不影响正常的使用，用以下的思路解决：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （1）	查看slowlog, 分析slowlog, 分析出查询慢的语句
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （2）	按照一定优先级，排查所有的慢语句
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （3）	分析top sql ，进行explain调试，查看语句执行时间
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  （4）	调整索引或语句本身

<br>



## 二．	查询优化

### （一）	MySQL查询流程

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  由于数据库的读写操作比例是10:1，因此sql优化主要是解决查询的优化问题。下面是查询的执行路径：

1.	客户端将查询sql语句发送到服务器中；

2.	服务器检查查询缓存中是否有该sql语句，如果有则直接返回结果，否则进入下一步；

3.	服务器通过关键字解析sql语句，通过语法解析生成解析树，解析器使用Mysql语法规则验证和解析查询，再经过预处理器生成新的解析树；

4.	解析树被校验合法后，经过查询优化器优化转成查询计划，一条语句有很多种执行方式，最后都会返回相同的结果。优化器的作用就是找到这其中最好的执行计划；

5.	查询执行引擎根据执行计划完成整个查询操作，最常使用的是MyISAM引擎和InnoDB引擎。 

6.	服务器将查询结果发送回客户端；

<br>


### （二）	查询优化

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  sql是开发者与数据库的交互手段，因此SQL调优是花费最多时间的，常见的分析手段有慢查询日志，EXPLAIN分析查询，通过定位分析性能的瓶颈，才能够更好地优化数据库系统的性能。
<br>



#### 1.	慢查询

##### （1）	开启慢查询日志
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  在配置文件my.cnf或者my.ini中在【mysqld】一行下面加入两个配置参数：

```java
log-slow-queries=/data/mysqldata/slow-query.log
long_query_time=5
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  上面是慢查询日志的存放位置，一般这个目录要有mysql的运行账号的可写权限，该目录设置为mysql的数据存放位置。下面是慢查询的阈值，只有超过这个时间才是慢查询，可以记录在慢查询日志中。
<br>



##### （2）	慢查询分析
 &nbsp;  &nbsp;  &nbsp;  &nbsp;  通过打开log文件查看哪些SQL语句执行效率低，如果日志内容过多，可以使用mysqldumpslow工具对慢查询日志进行分类汇总，显示汇总后的摘要结果。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  进入log日志的存放目录，运行：

```java
mysqldumpslow slow-query.log
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  该命令有一些参数，如下所示：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  -s : 表示按照什么方式排序，c，t，l，r分别表示按照记录次数，时间，查询时间，返回的记录数排序；ac, at, al, ar, 表示相应的倒序。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  -t n： 表示返回前面n条数据

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  -g: 后面可写正则匹配模式，大小写不敏感

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  由于开启慢查询日志会占用CPU资源，因此我们可以阶段性地开启慢查询日志来定位性能瓶颈。
<br>



#### 2.	EXPLAIN

##### （1）	概述

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  EXPLAIN可以帮助开发人员分析SQL问题，它显示了SQL语句的执行计划，我们可以从执行计划中找出可以优化的地方。
<br>



##### （2）	使用

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  在SQL语句前加上EXPLAIN即可：

```java
EXPLAIN SELECT * FROM sts
```
<br>



##### （3）	输出结果

 &nbsp;  &nbsp;  &nbsp;  &nbsp;  EXPLAIN的输出结果由多个列组成：

```java
id : select查询序列号；
```

```java
select_type : select语句的类型
	simple: 简单select(不使用union或子查询)
	primary: 最外面的select语句
	union: union中的第二个或者后面的select语句
	dependent union: union中的第二个或后面的select语句
	union result: union的结果
	subquery: 子查询中的第一个select
	dependent subquery: 子查询中的第一个select，取决于外面的查询
	derived: 导出表的select
```

```java
table: 显示从哪张表中查询
```

```java
type: 区间索引，最重要的列，显示连接使用了何种类型
```

连接类型效果的排行如下所示：

system > const > eq_ref > ref > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL

```java
possible_keys: Mysql能够使用哪个索引在该表中找到行，如果是空的，则没有相关的索引，需要提高性能。
```

```java
key: 实际使用的索引，如果为null表示没有使用索引，如果是primary表示使用了主键。
```

```java
key_len: 最长的索引宽度，长度越短越好
```

```java
ref: 显示使用哪个列或者常数与key一起从表中选择行；
```

```java
rows: 显示mysql执行查询时必须检查的行数
```

```java
Extra: 执行状态说明，包含解决查询的详细信息
```


