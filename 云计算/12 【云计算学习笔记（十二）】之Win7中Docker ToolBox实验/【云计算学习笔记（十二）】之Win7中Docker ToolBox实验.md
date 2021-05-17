@[TOC]
## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. Win7中ToolBox的使用
### （一） Windows版本选择
&nbsp;  &nbsp;  &nbsp;  &nbsp;对于Win10专业版用户，在Windows中使用Docker可以选用带有hyper-v功能的Docker for Windows，但是对于Win10家庭版，win8, win7用户，只能够使用Docker ToolBox来使用Docker。鉴于项目指定仿真环境统一为Win7环境，因此选用Docker ToolBox进行实验。
<br>


### （二）ToolBox介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;docker toolbox是一个工具集，它主要包含以下一些内容：

1. Docker CLI 客户端，用来运行docker引擎创建镜像和容器

2. Docker Machine. 可以让你在windows的命令行中运行docker引擎命令

3. Docker Compose. 用来运行docker-compose命令

4. Kitematic. 这是Docker的GUI版本

5. Docker QuickStart shell. 这是一个已经配置好Docker的命令行环境

6. Oracle VM Virtualbox. 虚拟机

&nbsp;  &nbsp;  &nbsp;  &nbsp;由于Docker引擎的守护进程使用的是Linux的内核，所以我们不能够直接在windows中运行docker引擎。而是需要运行Docker Machine命令 docker-machine， 在你的机器上创建和获得一个Linux虚拟机，用这个虚拟机才可以在你的windows系统上运行Docker引擎。
<br>


### （三） Win7中安装ToolBox
#### 1.安装ToolBox
&nbsp;  &nbsp;  &nbsp;  &nbsp;下载DockerToolbox-18.03.0-ce，在Win7虚拟机中安装。安装完成后，桌面会安装三个软件，一个是VirtualBox，一个是Kitematic，Docker的GUI版本，另一个是配置好的Docker命令行运行环境。
<br>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517151627440.png)

 
#### 2. 快速使用Docker ToolBox
&nbsp;  &nbsp;  &nbsp;  &nbsp;启动QuickStart环境，在安装成功的情况下会创建一个默认的虚拟机，并自动配置参数，出现以下图标表示安装成功：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517151640887.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

&nbsp;  &nbsp;  &nbsp;  &nbsp;再通过docker run命令运行一个hello-world，得到如下的结果表示docker运行正常：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210517151648258.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

&nbsp;  &nbsp;  &nbsp;  &nbsp;可能存在的问题：由于我们的Win7系统是虚拟机，因此它默认是没有开启虚拟化的，我们需要在Vmware的虚拟机设置中将vt开启，如下所示：
<br>
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021051715165699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 
### （四） 使用ToolBox
#### 1. docker-machine命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;通过该命令可以创建和管理Docker虚拟机，该虚拟机的本质还是一个优化过的linux操作系统，在Docker ToolBox中可以创建多个Docker虚拟机，每个虚拟机中都可以运行多个Docker容器。

```cpp
docker-machine ip 获取该虚拟机的ip地址；
docker-machine active 查看哪台虚拟机存活着；
docker-machine env 获取环境变量；
docker-machine inspect 获取虚拟机的详细信息；
docker-machine kill 杀掉一个虚拟机；
docker-machine ls 列出所有的虚拟机；
docker-machine restart 重启一个虚拟机；
docker-machine rm 删除一个虚拟机；
docker-machine scp 在虚拟机之间传递文件；
docker-machine mount 挂载或卸载一个目录；
docker-machine start 启动一个虚拟机；
docker-machine stop 停止一个虚拟机；
docker-machine url 获取虚拟机的url；
```
<br>


#### 2. docker 命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;docker命令就和普通的docker命令一样，这里就不赘述了。
<br>


#### 3. 在Docker虚拟机中安装Mysql容器测试
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用docker search mysql 命令查询最新的mysql镜像，pull下来然后通过docker run命令创建一个mysql容器：

```cpp
docker run --name my_mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123 mysql
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;也就是说，当前的mysql容器的3306端口与它所处的linux宿主机的3306端口是对应的，因此我们通过访问该linux虚拟机的3306端口就可以访问到mysql容器了。

&nbsp;  &nbsp;  &nbsp;  &nbsp;通过docker-machine ip查询到当前的default虚拟机的ip，要在win7虚拟机中访问到ToolBox中的这个linux虚拟机的3306端口，还需要对该mysql容器设置外部访问，在/etc/mysql/my.cnf配置文件中，[mysqld]下添加bind-address=0.0.0.0，表示允许外部访问。

&nbsp;  &nbsp;  &nbsp;  &nbsp;在Win7虚拟机中安装mysql，并且通过命令行可以访问到ToolBox中的这个mysql容器。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2021051715170938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

<br>

#### 4. 思考：为什么要在windows中使用容器？
&nbsp;  &nbsp;  &nbsp;  &nbsp;单独的微服务已经可以通过jar包以及bat批处理的方式在windows上启动，我为何还要耗费大量内存以及CPU资源在windows上生成一个linux虚拟机，然后在linux虚拟机上使用docker容器，这样不是既麻烦，又浪费空间吗？节点上的微服务的功能都是单一且不太可能会变化的，即使变化了，也可以通过重新打包的方式重新生成jar包。因为后期我们需要生成的每个虚拟机都是通过一个通用的win7镜像生成的，如果我们的节点上的微服务功能需要调整，只需要重新生成一个win7镜像即可，这个修改成本与每个win7节点上运行一个linux虚拟机来比是非常低的，因此，我觉得可以放弃在windows中使用容器来运行节点的微服务这个想法。

