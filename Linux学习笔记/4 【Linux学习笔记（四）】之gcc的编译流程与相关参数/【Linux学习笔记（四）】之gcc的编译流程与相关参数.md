## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	gcc概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; GNU编译器套件（GNU Compiler Collection），是以GPL许可证所发行的自由软件，现已被大多数Unix操作系统（Linux，BSD，Mac OS）采纳为标准的编译器，GCC也同样适用于Windows。
<br>

## 二．	gcc流程
### （一）	gcc -E hello.c
&nbsp;  &nbsp;  &nbsp;  &nbsp; 进行代码的预处理，展开头文件，并且进行宏替换。生成hello.i文件。
<br>

### （二）	gcc -S hello.i
&nbsp;  &nbsp;  &nbsp;  &nbsp; 生成汇编代码，生成hello.s文件。
<br>

### （三）	gcc -c hello.s
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将汇编代码编译为二进制文件，生成hello.o文件。
<br>

### （四）	ld hello.o
&nbsp;  &nbsp;  &nbsp;  &nbsp; 链接操作，生成可执行文件 hello.out。

<br>

## 三．	gcc相关参数
### （一）-I （大写的i）
&nbsp;  &nbsp;  &nbsp;  &nbsp; 当编译的文件中需要导入其他头文件时，直接用-I后面接头文件所在的目录。

```cpp
gcc add.c -I./include/ -o app
```
<br>

### （二）-D
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为源文件添加宏

```cpp
gcc add.c -I./include/ -D DEBUG -o app
```
<br>

### （三）-L
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为源文件添加包含的库路径
<br>

### （四）-l
&nbsp;  &nbsp;  &nbsp;  &nbsp; 为源文件需要的库指定库名。
<br>

### （五）-o
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将源文件编译到指定的目标文件中。

<br>

### （六）-g
&nbsp;  &nbsp;  &nbsp;  &nbsp; 用于gdb调试
<br>

### （七）-Wall
&nbsp;  &nbsp;  &nbsp;  &nbsp; 显示更多的警告
<br>

### （八）-E
&nbsp;  &nbsp;  &nbsp;  &nbsp; 输出到标准输出，宏替换，头文件展开
<br>

### （九）-c
&nbsp;  &nbsp;  &nbsp;  &nbsp; 编译成二进制文件，.0文件
<br>

### （十）-S
&nbsp;  &nbsp;  &nbsp;  &nbsp; 生成汇编代码。




