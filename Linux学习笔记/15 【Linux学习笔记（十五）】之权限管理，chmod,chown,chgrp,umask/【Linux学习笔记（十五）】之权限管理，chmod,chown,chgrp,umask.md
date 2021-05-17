## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	权限管理
### （一）	权限位介绍
&nbsp;  &nbsp;  &nbsp;  &nbsp;一般权限位有10位，在10位后面还带有一个“.”，这个符号表示着该文件是被Selinux所保护的。下面来详细介绍一下各个权限位：

#### 1.	第一位
&nbsp;  &nbsp;  &nbsp;  &nbsp;代表文件类型，常见的有：
<br>


##### （1）	-
&nbsp;  &nbsp;  &nbsp;  &nbsp;普通文件
<br>



##### （2）	b
&nbsp;  &nbsp;  &nbsp;  &nbsp;块设备文件，表示存储设备，如分区文件/dev/sda1
<br>



##### （3）	c
&nbsp;  &nbsp;  &nbsp;  &nbsp;字符设备文件，如输入设备，键盘。

<br>


##### （4）	d
&nbsp;  &nbsp;  &nbsp;  &nbsp;目录文件。
<br>



##### （5）	l
&nbsp;  &nbsp;  &nbsp;  &nbsp;软链接文件。

<br>


#### 2.	2-4位
&nbsp;  &nbsp;  &nbsp;  &nbsp;代表所有者的权限，分别为r(读)，w（写），x（执行），有字母对应着有该权限。

<br>


#### 3.	5-7位
&nbsp;  &nbsp;  &nbsp;  &nbsp;代表文件所属组的权限。

<br>


#### 4.	8-10位
&nbsp;  &nbsp;  &nbsp;  &nbsp;代表其他人的权限。
<br>



### （二）	基本权限命令
#### 1.	chmod
&nbsp;  &nbsp;  &nbsp;  &nbsp;修改文件权限（change file mode bits），可以使用字母，也可以使用数字代表权限。u代表所有者，g代表所属组，o代表其他人，a代表全部身份。使用方式如下所示：
<br>



##### （1）	字母加减设置权限

```cpp
chmod u+x dev
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;给dev文件的所有者添加执行权限。

```cpp
chmod u-x dev
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;给dev文件的所有者减去执行权限。


```cpp
chmod u=rwx dev
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;给dev文件的所有者设置rwx三种权限。

```cpp
chmod 744 dev
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;4代表R（读）权限，2代表W（写）权限，1代表X（执行）权限。

&nbsp;  &nbsp;  &nbsp;  &nbsp;上面的命令表示给dev文件的所有者设置读写执行权限，给所属组和其他人设置读的权限。

<br>


#### 2.	chown命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;修改文件和目录的所有者和所属组的命令（change file owner and group），普通用户不能修改文件的所有者，即使它拥有这个文件也不行，只有root用户能够修改文件的所属者。

```cpp
chown wxx dev
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改dev文件的所有者为wxx，前提是当前用户是root。

```cpp
chown wxx:wxx dev
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;修改dev文件的所有者为wxx，且所属组是wxx组。冒号后面表示所属组，每个用户默认都会有一个同名的组。
<br>



#### 3.	chgrp命令
&nbsp;  &nbsp;  &nbsp;  &nbsp;修改文件和目录的所属组（change group ownership）。
<br>



### （三）	基本权限的作用
&nbsp;  &nbsp;  &nbsp;  &nbsp;读，写，执行权限对于文件和目录的作用是不同的。
<br>



#### 1.	权限对于文件
##### （1）	读
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以读取文件中的数据，执行cat, more, less, head, tail等命令。
<br>



##### （2）	写
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以修改文件中的数据，执行vim，echo等命令，但是不能删除文件本身，想要删除文件，需要该文件的上级目录的写权限。因为文件的写权限仅限于读取该文件的block块中的内容，而该文件的文件名和I节点号都存储在上级目录的block块中，想要删除该文件，需要访问上级目录的block块获取文件的I节点号和文件名，也就是获取上级目录的写权限。
<br>



##### （3）	执行
&nbsp;  &nbsp;  &nbsp;  &nbsp;只要文件有执行权限，则该文件就是可执行文件了，执行权限是文件的最高权限。
<br>



#### 2.	权限对于目录
##### （1）	读
&nbsp;  &nbsp;  &nbsp;  &nbsp;查看目录下的内容，可执行ls命令。
<br>



##### （2）	写
&nbsp;  &nbsp;  &nbsp;  &nbsp;可修改目录下的数据，如新建，删除文件等等，可执行touch, rm, cp, mv命令，写权限是目录的最高权限。
<br>



##### （3）	执行
&nbsp;  &nbsp;  &nbsp;  &nbsp;目录不能执行，只能进入，可执行cd命令。
<br>



##### （4）	目录的可用权限
&nbsp;  &nbsp;  &nbsp;  &nbsp;目录只有3个可用权限，为什么没有4权限呢？因为目录只有读没有执行的话都不能进入目录：

```cpp
0： 无权限
5： 目录浏览和进入权限
7： 完全权限
```
<br>



### （四）	umask默认权限
#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;当我们新建一个文件或者文件夹时，系统都会默认设置权限，如在普通用户下，文件为664的权限，文件夹为775的权限。

&nbsp;  &nbsp;  &nbsp;  &nbsp;这是系统根据umask权限来计算的，系统给文件的默认最大权限原本是666，没有执行权限，因为执行权限对于文件来说比较危险，不能在新建文件时默认赋予。如果umask权限是0002时，通过计算就可以得出文件的权限位664了。

&nbsp;  &nbsp;  &nbsp;  &nbsp;目录同理，目录的默认最大权限是777，可以赋予执行权限，通过和umask权限0002计算得出权限位775。

<br>


#### 2.	权限计算方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以简单地理解为减法，用最大权限减去umask权限即可。但实际上，需要使用二进制进行逻辑与和逻辑非运算CIA可以得到默认权限。

<br>


#### 3.	查看umask权限

```cpp
umask
```
<br>



#### 4.	临时修改umask权限

```cpp
umask 0022
```
<br>



#### 5.	永久修改umask权限
&nbsp;  &nbsp;  &nbsp;  &nbsp;在 /etc/profile中修改umask权限值。


