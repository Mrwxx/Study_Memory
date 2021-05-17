## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

### （一）	管道符
#### 1.	格式

```cpp
命令1 | 命令2
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;将命令1的正确输出作为命令2的操作对象。
<br>


#### 2.	举例

```cpp
ll -a /etc/  | more
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;将ll命令结果分页查看。

```cpp
ll -a /etc/ | grep yum
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看ll命令的结果中是否有yum关键字，使用grep而不是find，因为find仅仅用于查找文件名，而管道符会将前面的命令结果保存到一个临时文件中，grep命令用于查找文件中的字符。

```cpp
netstat -an | grep “ESTABLISHED” | wc -l
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;查看网络连接数量，首先使用netstat命令查看网络状态，然后搜索“ESTABLISHED”关键字的行，使用wc命令计算行数。

<br>


### （二）	netstat命令

#### 1.	-a
&nbsp;  &nbsp;  &nbsp;  &nbsp;列出所有网络状态，包括socket程序。
<br>



#### 2.	-c
&nbsp;  &nbsp;  &nbsp;  &nbsp;指定每隔几秒刷新一次网络。
<br>



#### 3.	-n
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用IP地址和端口号显示，不适用域名与服务名。
<br>



#### 4.	-p
&nbsp;  &nbsp;  &nbsp;  &nbsp;显示PID和程序名。
<br>



#### 5.	-t
&nbsp;  &nbsp;  &nbsp;  &nbsp;显示使用TCP协议端口的连接情况。
<br>



#### 6.	-u
&nbsp;  &nbsp;  &nbsp;  &nbsp;显示使用UDP协议端口的连接情况。
<br>



#### 7.	-l
&nbsp;  &nbsp;  &nbsp;  &nbsp;仅显示监听状态的连接。
<br>



#### 8.	-r
&nbsp;  &nbsp;  &nbsp;  &nbsp;显示路由表
<br>



### （三）	别名
&nbsp;  &nbsp;  &nbsp;  &nbsp;为了使用的方便，为比较长的命令设置别名，注意，别名不要和系统命令重复，因为别名的优先级同名的系统命令要高。在设置别名之前，输入想要设置的别名试试，看看系统有没有这个命令。

```cpp
alias ser=’service network restart’
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;这个别名是为了当前用户的使用方便，因此只有当前用户能够使用该别名。同时，当前设置的别名都是临时生效的，要想永久生效，需要将别名命令写入当前用户家目录的环境变量配置文件中： ~/.bashrc 

<br>


### （四）	快捷键
#### 1.	Tab键
&nbsp;  &nbsp;  &nbsp;  &nbsp;补全命令或者文件名，当已经输入了部分字符串时，如果命令或者文件名以该字符串的数量只有一个，那么按一下Tab键就可以补全，否则，按两下Tab键会搜索出所有的以该字符串开头的命令或者文件名。
<br>



#### 2.	ctrl + A
&nbsp;  &nbsp;  &nbsp;  &nbsp;当输入的命令过长时，可以移动光标到命令行开头。
<br>



#### 3.	ctrl + E 
&nbsp;  &nbsp;  &nbsp;  &nbsp;移动光标到命令行尾。
<br>



#### 4.	ctrl + C
&nbsp;  &nbsp;  &nbsp;  &nbsp;强制终止当前的命令。

<br>


#### 5.	ctrl + L
&nbsp;  &nbsp;  &nbsp;  &nbsp;清屏，相当于clear。
<br>



#### 6.	ctrl + U
&nbsp;  &nbsp;  &nbsp;  &nbsp;剪切光标之前的命令。

<br>


#### 7.	ctrl + Y
&nbsp;  &nbsp;  &nbsp;  &nbsp;粘贴ctrl + U 剪切的命令。


