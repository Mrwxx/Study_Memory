## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	MySQL的数据目录

### （一）	数据目录

 &nbsp;  &nbsp;  &nbsp;  &nbsp;MySQL服务器程序在启动时，回到文件系统的某个目录下加载一些数据，之后在运行过程中产生的数据也会存储到这个目录下的某些文件中，这个目录称为数据目录。注意，要与mysql的安装目录区分开。要确定该数据目录路径，通过系统变量datadir查看即可。

```java
SHOW VARIABLES LIKE ‘datadir’;
```
<br>


### （二）	数据目录的结构
 &nbsp;  &nbsp;  &nbsp;  &nbsp;MySQL会保存哪些数据呢？创建的数据库，表，视图和触发器等用户数据，还有额外的一些数据。
<br>


#### 1.	数据库
 &nbsp;  &nbsp;  &nbsp;  &nbsp;每当创建一个数据库时，数据目录都会创建一个子目录，与数据库同名，同时在该子目录下创建一个名为db.opt的文件，包含了该数据库的一些属性，如字符集与比较规则。MySQL中的数据库除了information_schema这个系统数据库没有对应的子目录外，其他的都有。

<br>

#### 2.	表
 &nbsp;  &nbsp;  &nbsp;  &nbsp;数据以记录的形式插入到表中，每个表的信息分为表结构的定义和表中的数据。InnoDB和MyISAM都在数据库子目录下创建了一个专用于描述表结构的文件：表名.frm，以二进制格式存储。
<br>


##### （1）	InnoDB中的表
 &nbsp;  &nbsp;  &nbsp;  &nbsp;对于表中的数据，不同的存储引擎是不太一样的。对于InnoDB，首先以页为单位管理数据，并使用表空间来管理这些页。表空间是抽象的概念，对应文件系统上一个或多个真实文件，每个表空间被划分为多个页，表空间有多种类型。系统表空间对应文件系统上一个或多个实际的文件，默认情况下，InnoDB会在数据目录下创建一个名为ibdata1，大小为12MB的系统表空间文件，该文件是自扩展的，文件大小可通过系统变量修改。一个MySQL服务器中，系统表空间只有一份。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;MySQL5.6.6之后，InnoDB不再默认将各个表的数据存储到系统表空间中，而是为每一个表建立一个独立表空间，该文件名为：表名.ibd。还有一些其他类型的表空间，如通用表空间，undo表空间，临时表空间等。
<br>


##### （2）	MyISAM中的表
 &nbsp;  &nbsp;  &nbsp;  &nbsp;由于MyISAM中的索引都是二级索引，该存储引擎的数据和索引是分开存放的，所以在文件系统中也是使用不同的文件来存储数据文件和索引文件，表的数据和索引也是存放到对应的数据库子目录下，表名.MYD表示表的数据文件，表名.MYI表示表的索引文件。
<br>


#### 3.	其他文件

 &nbsp;  &nbsp;  &nbsp;  &nbsp;服务器进程文件，日志文件，证书与密钥文件。
<br>


### （三）	MySQL系统数据库介绍

#### 1.	mysql
 &nbsp;  &nbsp;  &nbsp;  &nbsp;存储mysql的用户账户和权限信息，日志，帮助信息等。

<br>

#### 2.	information_schema
 &nbsp;  &nbsp;  &nbsp;  &nbsp;mysql服务器维护的所有其他数据库的信息，如哪些表，哪些视图，哪些触发器，哪些列，哪些索引，这些信息不是真实的用户数据，只是描述性信息，称为元数据。
<br>


#### 3.	performance_schema

 &nbsp;  &nbsp;  &nbsp;  &nbsp;保存mysql服务器运行过程中的状态信息，是对mysql运行状态的性能监控。
<br>


#### 4.	sys

 &nbsp;  &nbsp;  &nbsp;  &nbsp;通过视图的形式将information_schema 和 performance_schema结合起来，让开发者更方便了解mysql服务器的性能信息。




