## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Redis
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Redis 高性能的NOSQL系列的菲关系型数据库。Redis通过提供多种键值数据类型来适应不同场景下的存储需求。
<br>


### （二）	Redis支持的数据类型
1.	字符串类型 String

2.	哈希类型  hash

3.	列表类型 list

4.	集合类型  set

6.	有序集合类型  sortedset
<br>



### （三）	Redis的应用场景
1.	缓存（数据查询，短连接，内容）

2.	任务队列（秒杀，抢购）

3.	应用排行榜

4.	数据过期处理

6.	分布式集群架构中的session分离
<br>



### （四）	安装与使用
#### 1.	Redis下载
&nbsp;  &nbsp;  &nbsp;  &nbsp;大陆地区请到Redis中文网http://www.redis.net.cn/，下载redis。
<br>



#### 2.	使用
&nbsp;  &nbsp;  &nbsp;  &nbsp;解压下载的压缩包，可以得到一些文件：

&nbsp;  &nbsp;  &nbsp;  &nbsp;redis.windows.conf是配置文件

&nbsp;  &nbsp;  &nbsp;  &nbsp;redis-cli.ext是redis的客户端

&nbsp;  &nbsp;  &nbsp;  &nbsp;redis-server.exe是redis的服务器端

&nbsp;  &nbsp;  &nbsp;  &nbsp;要使用redis，，先要打开服务器端，再打开客户端连接到服务器。
<br>



### （五）	Redis的数据结构
&nbsp;  &nbsp;  &nbsp;  &nbsp;Redis存储的是键值对的数据，key都是字符串，value有5种不同类型：

&nbsp;  &nbsp;  &nbsp;  &nbsp;字符串类型 String

&nbsp;  &nbsp;  &nbsp;  &nbsp;哈希类型  hash

&nbsp;  &nbsp;  &nbsp;  &nbsp;列表类型 list

&nbsp;  &nbsp;  &nbsp;  &nbsp;集合类型  set

&nbsp;  &nbsp;  &nbsp;  &nbsp;有序集合类型  sortedset
<br>



### （六）	Redis的命令操作
#### 1.	字符串类型String
##### （1）	存储
&nbsp;  &nbsp;  &nbsp;  &nbsp;set key value
如：`set age 22`
<br>



##### （2）	获取
&nbsp;  &nbsp;  &nbsp;  &nbsp;get key
如：`get age`
<br>



##### （3）	删除
&nbsp;  &nbsp;  &nbsp;  &nbsp;del key
如：`del age`
<br>



#### 2.	哈希类型 hash
##### （1）	存储
&nbsp;  &nbsp;  &nbsp;  &nbsp;hset key field value

如：field value是一个键值对

```sql
hset user name 22
```
<br>



##### （2）	获取
&nbsp;  &nbsp;  &nbsp;  &nbsp;hget key field 获取指定的field对应的值

如：`hget user name`

&nbsp;  &nbsp;  &nbsp;  &nbsp;hgetall key获取所有的field和value

如：`hgetall user`
<br>



##### （3）	删除
&nbsp;  &nbsp;  &nbsp;  &nbsp;hdel key field

如：`hdel user name`
<br>



#### 3.	列表类型list
##### （1）	添加
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以添加一个元素到列表的头部（左边）或者尾部（右边）

&nbsp;  &nbsp;  &nbsp;  &nbsp;lpush key value 将元素添加到列表的左边

&nbsp;  &nbsp;  &nbsp;  &nbsp;rpush key value 将元素添加到列表的右边
如：

```sql
lpush mylist 1
rpush mylist  10
```
<br>



##### （2）	获取
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以通过一个范围获取元素：

&nbsp;  &nbsp;  &nbsp;  &nbsp;lrange key start end
如：

```sql
lrange mylist 1 2
```
<br>



##### （3）	删除
&nbsp;  &nbsp;  &nbsp;  &nbsp;lpop key 删除列表最左边的元素，并返回该元素

&nbsp;  &nbsp;  &nbsp;  &nbsp;rpop key 删除列表最右边的元素，并返回该元素
<br>



#### 4.	集合类型set
&nbsp;  &nbsp;  &nbsp;  &nbsp;set集合不允许有重复的元素。

##### （1）	存储
&nbsp;  &nbsp;  &nbsp;  &nbsp;sadd key value
如：

```sql
sadd age 22
```
<br>



##### （2）	获取
&nbsp;  &nbsp;  &nbsp;  &nbsp;smembers key 获取set集合中的所有元素

如：

```sql
smembers age
```
<br>



##### （3）	删除
&nbsp;  &nbsp;  &nbsp;  &nbsp;srem key value， 删除set集合中的某个元素

```sql
srem age 22
```
<br>



#### 5.	有序集合类型sortedset
&nbsp;  &nbsp;  &nbsp;  &nbsp;不仅不允许重复元素，且元素有序
##### （1）	存储
&nbsp;  &nbsp;  &nbsp;  &nbsp;zadd key score value， 通过score分数来排序

如：

```sql
zadd age 80 22
```
<br>



##### （2）	获取
&nbsp;  &nbsp;  &nbsp;  &nbsp;zrange key start end

如：

```sql
zrange age 0 -1
```
<br>



##### （3）	删除
&nbsp;  &nbsp;  &nbsp;  &nbsp;zrem key value

如：

```sql
zrem age 22
```
<br>



#### 6.	通用命令
##### （1）	keys *
&nbsp;  &nbsp;  &nbsp;  &nbsp;查询所有的键
<br>



##### （2）	type key
&nbsp;  &nbsp;  &nbsp;  &nbsp;获取键对应的value的类型
<br>



##### （3）	del key
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除指定的key value
<br>



### （七）	持久化
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Redis是一个内存数据库，当Redis服务器重启或关闭时，数据就会丢失，我们需要将Redis内存中的数据持久化到硬盘上。
<br>



#### 2.	Redis持久化机制
##### （1）	RDB
&nbsp;  &nbsp;  &nbsp;  &nbsp;Redis持久化的默方式，不需要进行配置，在一定的间隔时间中，会自动检测key的变化情况，然后持久化数据。

&nbsp;  &nbsp;  &nbsp;  &nbsp;想要修改RDB的间隔时间，可以到conf配置文件中进行修改

找到如下的字段：

```sql
save 900 1		：至少一个key变化时900s后持久化
save 300 10	：至少10个key变化时300s后持久化
save 60 10000 ：至少10000个key变化时，60s后持久化
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;通过注释来阅读并修改。修改后，重启服务器，并制定配置文件名称。

&nbsp;  &nbsp;  &nbsp;  &nbsp;cd 到redis的文件夹，执行redis-server.exe命令并 添加 配置文件，如：

```sql
redis-server.exe redis.windows.conf
```
<br>



##### （2）	AOF
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过日志记录的方式，可以记录每一条命令的操作，在执行每一条Redis命令后，都会持久化数据。

&nbsp;  &nbsp;  &nbsp;  &nbsp;同样的，我们可以在conf配置文件中修改AOF的配置：

```sql
appendonly no 表示着关闭AOF
appendonly yes 表示着开启AOF

appendfsync always 表示着每一次操作有持久化
appendfsync everysec 表示这每隔一秒进行一次持久化
appendfsync no	 表示这不进行持久化
```
<br>



## 二．	 NOSQL
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Not Only SQL，泛指非关系型数据库。随着Web2.0的兴起，传统的关系型数据库对于超大规模与高并发的Web2.0网站已经显得力不从心了。菲关系型数据库可以用作缓存来解决大数据的应用难题。由于NOSQL将数据存储在缓存中，查询速度会非常快。且NOSQL的存储格式有很多种，如key ,value,文档，图片。
<br>



### （二）NOSQL与关系型数据库的优劣
#### 1.	菲关系型数据库的优势
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	性能NOSQL是基于键值对的，可以想象成表中的主键和之的对应关系，不需要经过SQL层的解析，性能很高。

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	可扩展性也是基于键值对，数据之间没有耦合性，所有容易扩展。
<br>



#### 2.	关系型数据库的优势
&nbsp;  &nbsp;  &nbsp;  &nbsp;（1）	复杂查询可以用SQL语句方便地在一个表以及多个表之间做复杂查询。

&nbsp;  &nbsp;  &nbsp;  &nbsp;（2）	事务支持使得对于安全性能很高的数据访问得以实现。

<br>



## 三．	Jedis
### （一）	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;如同我们之前使用JDBC连接MySql数据库一样，Jedis用于Java操作Redis数据库。
<br>



### （二）	使用步骤
1.	导入jedis的jar包

3.	通过Java编写程序操作Redis数据库

```java
package redis;

import org.junit.Test;
import redis.clients.jedis.Jedis;

/**
 * jedis的测试类
 */
public class RedisTest {

    @Test
    public void test1(){
        //获取连接
        Jedis jedis = new Jedis("localhost", 6379);
        //操作
        jedis.set("username", "hehe");
        //关闭连接
        jedis.close();

    }

}
```
<br>



### （三）	字符串的操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;Jedis中的方法名与Redis的操作是相同的。

&nbsp;  &nbsp;  &nbsp;  &nbsp;set存储,get获取,setex存储有时间限制的键值对。

```java
Jedis jedis = new Jedis();
jedis.set(“username”, “hehe”);
String username = jedis.get(“username”);
jedis.setex(“activecode”, 20, “hehe”);	//20秒后删除
jedis.close();
```
<br>



### （四）	哈希类型的操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;hset, hget。

```java
jedis.hset(“user”, “name”, “eee”);
jedis.hset(“user”, “age”, “23”);

String name – jedis.hget(“user”, “name”);
//获取hash中的所有map中的数据
Map<String ,String> user = jedis.hgetAll(“user”);
Set<String> keyset = user.keySet();
for(String key : keySet){
		String value = user.get(key);
		System.out.println(key);
}
```
<br>



### （五）	列表list的操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;lpush, rpush, lpop, rpop

```java
jedis.lpush(“mylist”, “a”);
jedis.rpush(“mylist”, “b”);
List<String> mylist = jedis.lrange(“mylist”, 0, -1);
```
<br>



### （六）	集合set的操作
&nbsp;  &nbsp;  &nbsp;  &nbsp;sadd，smembers

```java
jedis.sadd(“myset”, “java”);
jedis.sass(“myset”, “c”);
Set<String> myset = jedis.smembers(“myset”);
```
<br>



### （七）	Jedis连接池
&nbsp;  &nbsp;  &nbsp;  &nbsp;Jedis内置了连接池JedisPool，使用方法非常简单：

&nbsp;  &nbsp;  &nbsp;  &nbsp;首先创建JedisPool连接池对象，再调用getResource()方法获取Jedis连接。

```java
JedisPool jedisPool = new JedisPool();
Jedis jedis = jedisPool.getResource();
jedis.set(“name”, “hehe”);
jedis.close();
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;可以通过配置对象来配置JedisPool。在创建JedsiPool连接池对象时要将该配置对象传进去。

```java
JedisPoolConfig config = new JedisPoolConfig();
config.setMaxTotal(50);
config.setMaxIdle(10);
JedisPool jedisPool = new JedisPool(config, “localhost”, 6379);
```
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;也可以通过设置一个RedisPoolUtils工具类专用于连接Redis数据库以及通过redis.properties配置文件来配置RedisPool连接池。

```java
package utils;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;
import redis.clients.util.JedisURIHelper;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class JetisUtils {

    private static JedisPool jedisPool;
    static {
        //读取配置文件
        InputStream is = JetisUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
        //创建Properties对象
        Properties pro = new Properties();
        //冠梁文件
        try {
            pro.load(is);
        } catch (IOException e) {
            e.printStackTrace();
        }
        //获取数据，设置到JedisPoolConfig中
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
        config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));

        //初始化JedisPool
        jedisPool = new  JedisPool(config, pro.getProperty("host"), Integer.parseInt(pro.getProperty("port")));


    }

    public static Jedis getJedis(){
        return jedisPool.getResource();
    }
}
```













