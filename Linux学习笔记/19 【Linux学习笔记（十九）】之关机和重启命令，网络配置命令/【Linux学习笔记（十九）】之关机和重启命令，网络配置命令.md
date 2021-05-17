## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	关机和重启
### （一）	sync数据同步
&nbsp;  &nbsp;  &nbsp;  &nbsp; sync命令（flush file system buffers），作用是刷新文件系统缓冲区到硬盘中，在/bin/sync中，所有用户都能够调用，通常在关机前调用sync命令保存数据。
<br>


### （二）	shutdown
&nbsp;  &nbsp;  &nbsp;  &nbsp; 安全地关机与重启命令，能够完整地关闭和保存系统的服务，在/sbin/shutdown中，只有root用户才能够调用。命令格式如下：

```cpp
shutdown [选项] 时间 [警告信息]
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -c: 取消已经执行的shutdown命令

&nbsp;  &nbsp;  &nbsp;  &nbsp; -h: 关机

&nbsp;  &nbsp;  &nbsp;  &nbsp; -r: 重启

&nbsp;  &nbsp;  &nbsp;  &nbsp; 可以看到，可以指定时间关机或者重启，但是不建议这么做，最好还是手动关机。

<br>


### （三）	reboot
&nbsp;  &nbsp;  &nbsp;  &nbsp; 也是安全的重启命令。
<br>



### （四）	halt和poweroff命令
&nbsp;  &nbsp;  &nbsp;  &nbsp; 关机命令，但是不会完整地关闭和保存系统的服务，不建议使用。

<br>



## 二．	网络命令
### （一）	配置IP地址

&nbsp;  &nbsp;  &nbsp;  &nbsp; 计算机要接入互联网，就需要配置唯一的公网IP地址，，配置IP地址有两种方法：
<br>



#### 1.	配置IP
&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置IP 有两种方式，一种是setup工具，一种是修改配置文件。
<br>



##### （1）setup工具
&nbsp;  &nbsp;  &nbsp;  &nbsp; 这是Centos中的命令工具，可以在图形化界面配置IP，
<br>



##### （2）vi /etc/sysconfig/network-scripts/ifcfg-eth0
&nbsp;  &nbsp;  &nbsp;  &nbsp; 手动修改配置文件的配置项。
<br>



#### 2.	重启网络

```cpp
service network restart
```
<br>



#### 3.	虚拟机桥接到有线网卡，并重启网络服务。
<br>



#### 4.	复制的镜像需要重置UUID

&nbsp;  &nbsp;  &nbsp;  &nbsp; UUID（唯一标识符），这是每个机器的唯一标识符，不可能重复。但是，如果我们是直接复制的镜像或者通过快照恢复的镜像，就会出现UUID冲突的情况，这时就需要修改UUID了。但是，也并不是直接修改UUID，UUID是通过MAC地址HWADDER生成的，因此我们需要在配置文件中删除MAC地址，然后删除MAC地址和UUID绑定文件，重启虚拟机后，它会自动生成MAC地址，然后计算生成新的UUID。

```cpp
vi /etc/sysconfig/network-scripts/ifcfg-eth0
rm -rf /etc/udev/rules.d/70-persistent-net.rules
reboot
```
<br>



### （二）	ifconfig
&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看IP地址，MAC地址，子网掩码，广播地址

<br>


### （三）	ping
&nbsp;  &nbsp;  &nbsp;  &nbsp; 通过ICMP协议进行网络探测，测试网络中主机的通信情况。命令格式如下：

```cpp
ping [选项] IP
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -b : 后面添加广播地址，查看在该子网段中有多少主机可以通信。如本机处于192.168.112网段中，广播地址是 192.168.112.255,通过该命令就可以看到112网段中有多少主机可以通信。

```cpp
ping -b 192.168.112.255
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; -c 次数 ： 用于指定发送PING的次数

&nbsp;  &nbsp;  &nbsp;  &nbsp; -s 字节 ： 指定探测包的大小
<br>



### （四）	netstat

#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看网络状态，既可以看到本机开启的端口，也可以查看有哪些客户端连接着。Centos7中netstat命令不是默认的，需要安装net-snmp和net-tools软件包。
<br>



#### 2.	选项

&nbsp;  &nbsp;  &nbsp;  &nbsp; -a : 列出所有网络状态，包括Socket程序连接

&nbsp;  &nbsp;  &nbsp;  &nbsp; -c 秒数： 指定每隔几秒刷新一次网络

&nbsp;  &nbsp;  &nbsp;  &nbsp; -n : 使用IP地址和端口号显示，不适用域名和服务名

&nbsp;  &nbsp;  &nbsp;  &nbsp; -p : 显示PID和程序名

&nbsp;  &nbsp;  &nbsp;  &nbsp; -t : 显示使用TCP协议端口的链接

&nbsp;  &nbsp;  &nbsp;  &nbsp; -u : 显示使用UDP协议端口的链接

&nbsp;  &nbsp;  &nbsp;  &nbsp; -l : 仅显示监听状态的链接

&nbsp;  &nbsp;  &nbsp;  &nbsp; -r : 显示路由表
<br>



#### 3.	查看本机开启的端口
&nbsp;  &nbsp;  &nbsp;  &nbsp; -tulnp，既可以看到那些端口开启了，也可以看到是哪个程序开启的，并显示该程序的PID和名称。

```cpp
netstat -tulnp
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 注意，连接的状态常用的有LISTEN监听和ESTABLISHED已建立连接两种。
<br>



#### 4.	查看所有连接

&nbsp;  &nbsp;  &nbsp;  &nbsp; -an，包括监听状态，建立连接状态，Socket程序连接等。

```cpp
netstat -an
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 从“Active UNIX domainsockets” 开始，后面的内容就是Socket程序产生的连接，之前的内容都是网络服务产生的连接。
<br>



### （五）	write
&nbsp;  &nbsp;  &nbsp;  &nbsp; 向服务器上的其他用户发送信息，命令格式如下：

```cpp
write user1 pts/1
hello
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 向在pts/1服务器上登录的user1用户发送消息，使用 Ctrl + D快捷键保存发送的数据。
<br>



### （六）	wall
&nbsp;  &nbsp;  &nbsp;  &nbsp; 给所有登录用户发送消息，在wall后面添加要发送的消息即可。

<br>


### （七）	mail
&nbsp;  &nbsp;  &nbsp;  &nbsp; 给其他用户发送邮件，命令格式如下：

```cpp
mail user1
Subject:
hello
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 发送邮件给user1，使用一个点来结束输入，邮件都保存在/var/spool/mail/用户名 目录下，每个用户都有一个自己名称的邮箱。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 发送文件root给用户，以"title"为邮件标题：

```cpp
mail -s “title” root < /root/anaconda.cfg
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 查看已经接受的邮件，N代表未读，后面的数字为邮件的编号，想要查看邮件内容的话，输入邮件的编号即可。




