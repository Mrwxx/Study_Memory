## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	MySQL启动选项

### （一）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp; MySQL的服务器程序和客户端程序有很多设置项，如允许同时连入的客户端数量，客户端和服务器的通信方式等等，都可以通过启动选项来设置，可以在命令行中指定，也可以在配置文件中指定，命令行的优先级大于配置文件。
<br>


### （二）	命令行

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在命令行中指定启动选项时需要在选项名之前加上--前缀，如果选项名是由多个单词构成的，可以由短划线-连接，或者下划线_连接。如禁止TCP/IP:

```java
mysqld –skip-networking
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 指定存储引擎为MyISAM:

```java
mysqld –default-storage-engine=MyISAM
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 还有一些启动选项具有短形式，即长度很短的字母，如-h, -u等等。
<br>


### （三）	配置文件

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在命令行中设置的启动选项只对当次启动有效，如果要永久生效，需要在配置文件中配置启动选项，每次启动服务器时都从这个文件中加载。

<br>

#### 1.	配置文件的路径
&nbsp;  &nbsp;  &nbsp;  &nbsp; MySQL程序启动时会在多个路径下寻找配置文件，这些路径有个是固定的，有的是在命令行中指定。不同系统的路径都是不同的，但是多个路径的优先级的逻辑是相似的，MySQL程序每次启动时按照系统->服务器->客户端的优先级顺序来加载，越靠后的配置文件优先级越大。命令行还能够通过 defaults-extra-file 选项来指定额外加载的配置文件。

<br>

#### 2.	配置文件的内容

&nbsp;  &nbsp;  &nbsp;  &nbsp; 配置文件中的启动选项被划分为若干个组，每个组有一个组名，用中括号[]扩起来，每个组下面可以定义若干个启动选项，如下所示：

```java
[server]
option1
optino2 = value2
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 不同的选项组是个不同的程序使用的，如果选项组名称与程序名称相同，那么组中的选项就会专用于该程序，不过也有特例，如[server]组下面的启动选项将用于所有的服务器程序，[client]组下面的启动选项将用于所有的客户端程序，还有mysqld_safe和mysql.server这两个程序会在启动时读取[mysqld]选项组中的内容。

<br>

#### 3.	特定MySQL版本的专用选项组
&nbsp;  &nbsp;  &nbsp;  &nbsp; 我们可以在选项组的名称后面加上特定的MySQL版本号，如：

```java
[mysqld-5.7]
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 含义和[mysqld]一样，但是只有版本号为5.7的mysqld程序能够使用这个选项组中的选项。

<br>

#### 4.	选项组的优先级

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果在不同的选项组中出现了同样的启动选项，那么将以最后一个出现的组中的启动选项为准。

<br>

#### 5.	default-file

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在命令行中添加default-file 选项，表示只在该路径下搜索配置文件，不会到默认的路径搜索，而default-extra-file 选项表示在默认的搜索路径之外的额外搜索路径。

<br>


## 二．	系统变量

### （一）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp; MySQL服务器程序在运行过程中会用到许多影响程序行为的变量，称为系统变量，如允许同时连入的客户端数量用系统变量max_connections表示，严格地说，MySQL服务器允许max_connections+1个客户端连接，多出来的一个是root用户。每一个系统变量都有一个默认值，我们可以在命令行或配置文件中设置，同时大多数系统变量的值也可以在程序运行过程中修改，无需重启服务器。
<br>


### （二）	查看系统变量

&nbsp;  &nbsp;  &nbsp;  &nbsp; 使用下面的命令查看MySQL服务器程序支持的系统变量以及当前值：

```java
SHOW VARIABLES [like 匹配模式];
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; like表达式用于指定过滤条件，如查看当前的存储引擎：

```java
SHOW VARIABLES like “default_storage_engine”;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 也可以采用模糊查询的方式：

```java
SHOW VARIABLES like “default%”;
```

<br>

### （三）	设置系统变量

#### 1.	通过启动选项设置
&nbsp;  &nbsp;  &nbsp;  &nbsp; 大部分系统变量都可以通过启动服务器时传送启动选项的方式设置，通过命令行或者配置文件：

```java
mysqld --default-storage-engine=MyISAM
```

```java
[server]
default-storage-engine=MyISAM
```
<br>


#### 2.	运行过程中设置

&nbsp;  &nbsp;  &nbsp;  &nbsp; 对于大部分系统变量，可以在服务器程序运行过程中动态修改，不过系统变量有作用范围之分，系统变量的作用范围分为全局范围和会话范围。全局范围是影响服务器的全面变量，而会话范围是影响某个客户端连接的会话变量。服务器在启动时，会将每个全局变量设为默认值，服务器还为每个连接的客户端维护一组会话变量，客户端的会话变量在连接时使用相应全局变量的当前值进行初始化。很显然，通过启动选项设置的系统变量的系统变量的作用范围都是GLOBAL的，因为服务器启动时客户端还没有连接。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在服务器运行期间通过客户端程序设置系统变量的语法：

```java
SET [ GLOBAL|SESSION] 系统变量名 = 值;
SET [@@(GLOBAL|SESSION).]系统变量名 = 值;
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 设置的语法默认作用范围是SESSION，如果使用SESSION修饰符，表示针对当前连接有效的系统变量值。
<br>


#### 3.	注意事项

&nbsp;  &nbsp;  &nbsp;  &nbsp; 并不是所有的系统变量都有GLOBAL 和 SESSION 的作用范围，有一些系统变量只有GLOBAL,如max_connections；有一些变量只有SESSION,如insert_id，表示对某个AUTO_INCREMENT列的表进行插入时，该列的初始值。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 有的系统变量是只读的，如version表示mysql的版本信息。
<br>


### （四）	启动选项和系统变量的区别

&nbsp;  &nbsp;  &nbsp;  &nbsp; 大部分系统变量都可以当做启动选项传入；
&nbsp;  &nbsp;  &nbsp;  &nbsp; 有些启动选项也不是系统变量，如defaults-file

<br>


## 三．	状态变量

&nbsp;  &nbsp;  &nbsp;  &nbsp; MySQL服务器程序维护了一些关于程序运行状态的变量，称为状态变量，如Threads_connected，表示当前连接的客户端数量。这些状态变量只能由服务器程序自己设置，不能认为控制。与系统变量类似，状态变量也有GLOBAL 和 SESSION两个作用范围，查看状态变量的语句可以这样写：

```java
SHOW [GLOBAL|SESSION] STATUS [like 匹配模式];
```

```java
show status like “thread%”;
```







