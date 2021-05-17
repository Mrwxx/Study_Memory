## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	Docker部署Mysql
### （一）	需求分析
&nbsp;  &nbsp;  &nbsp;  &nbsp;在Docker容器中部署Mysql，并通过外部的Mysql客户端操作Mysql Server。
<br>


### （二）	端口映射
&nbsp;  &nbsp;  &nbsp;  &nbsp;由于外部机器与容器不能直接通信，而外部机器与宿主机器是可以通信的，因此可以通过将容器中提供服务的端口映射到宿主机的端口上，当外部机器访问宿主机的该端口时，就能间接地访问容器的服务。这种操作称为端口映射。
<br>



### （三）	部署步骤

#### 1.	搜索mysql镜像

```cpp
docker search mysql
```
<br>



#### 2.	拉取mysql镜像

```cpp
docker pull mysql
```
<br>



#### 3.	创建容器，设置端口映射，目录映射
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先创建宿主机的mysql目录：/root/mysql，进入mysql目录。

```cpp
mkdir /root/mysql
cd /root/mysql
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;然后创建容器，-p参数表示设置端口映射，前面是宿主机的端口，后面是容器的端口， -v是映射目录，前面是宿主机数据卷，后面是容器目录；-e是设置容器环境，MYSQL_ROOT_PASSWORD初始化root用户的密码。

```cpp
docker run -id  -p 3307:3306  --name=c_mysql  -v $PWD/conf:/etc/mysql/conf.d  -v $PWD/logs:/logs  -v $PWD/data:/var/lib/mysql  -e MYSQL_ROOT_PASSWORD=123456  mysql
```
<br>



#### 4.	进入容器，操作Mysql

```cpp
docker exec -it c_mysql /bin/bash
```
<br>



#### 5.	登录mysql

```cpp
mysql -uroot -p123456
```
<br>



#### 6.	在本地机器中连接宿主机3307端口
&nbsp;  &nbsp;  &nbsp;  &nbsp;连接宿主机的3307端口，就映射到了容器的3306端口，那么就是直接操作容器的mysql了，这样，就实现了外部机器与容器之间的通信。

<br>



## 二．	Docker部署Tomcat
### （一）	搜索Tomcat镜像

```cpp
docker search tomcat
```
<br>



### （二）	拉取镜像

```cpp
docker pull tomcat
```
<br>



### （三）	创建容器，设置端口映射，目录映射

```cpp
mkdir /root/tomcat
cd /root/tomcat
```

```cpp
docker run -id --name=c_tomcat -p 8080:8080 -v $PWD:/usr/local/tomcat/webapps tomcat
```

<br>



## 三．	Docker 部署Redis
### （一）	搜索Redis镜像

```cpp
docker search redis
```
<br>



### （二）	拉取Redis镜像

```cpp
docker pull redis
```

<br>


### （三）	创建容器，设置端口映射

```cpp
docker run -id --name=c_redis -p 6379:6379 redis
```
<br>



### （四）	使用外部机器连接Redis
&nbsp;  &nbsp;  &nbsp;  &nbsp;在本地机器中进入Redis目录中，使用如下命令连接宿主机的端口。

```cpp
redis-cli.ext -h 192.168.000.000 -p 6379
```





