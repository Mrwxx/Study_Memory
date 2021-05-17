## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．静态库
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;相当于Windows中的xxx.lib文件，Linux中的静态库文件为libxxx.a文件。当我们需要向他人提供源码的功能，但又不想将源码分享，就可以只分享头文件和库文件。他人就可以通过头文件知晓如何使用库文件中的函数功能。
<br>

### （二）制作步骤
&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 将源码文件编译为.o文件，即二进制文件。
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 将.0文件打包，打包的命令为：

```cpp
 ar rcs libxxx.a fil1.o file2.o…
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;前面为静态库文件名，后面为库里添加的.o文件
<br>
&nbsp;  &nbsp;  &nbsp;  &nbsp;3. 将头文件和库文件一起发布
<br>

### （三）查看静态库中的内容
&nbsp;  &nbsp;  &nbsp;  &nbsp;可以显示静态库包含的.0文件。

```cpp
nm libxxx.a
```

<br>

### （四）优缺点
#### 1. 优点
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行速度快，发布应用时不需要发布库。
<br>

#### 2. 缺点
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行程序体积比较大，因为库已经编译到代码中了，库变更后需要重新编译应用。
<br>

## 二．动态库	
### （一） 概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;Linux的动态库命名规则为：libxxx.so，Windows中动态库文件为dll文件。
<br>
### （二）制作步骤
&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 编译与位置无关的代码，生成.o二进制文件，使用的参数是-fPIC

```cpp
gcc -fPIC -c *.c -I ../include/
```

<br>
&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 将.o文件打包，使用参数 -shared，生成动态库文件libxxx.so文件。

```cpp
gcc -shared -o libxxx.so *.o
```
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;3. 将库文件和头文件一起发布
<br>

### （三）动态库无法加载的解决方法
&nbsp;  &nbsp;  &nbsp;  &nbsp;3种方法

&nbsp;  &nbsp;  &nbsp;  &nbsp;1. 拷贝到系统的库路径下：/lib，不推荐

&nbsp;  &nbsp;  &nbsp;  &nbsp;2. 将库路径添加到环境变量LD_LIBRARY_PATH中，用冒号分隔。推荐

&nbsp;  &nbsp;  &nbsp;  &nbsp;3. 修改/etc/Id.so.conf文件，添加库路径到该文件中，然后执行 sudo Idconfig -v
<br>

### （四）优缺点
#### 1. 优点
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行程序体积小，库变更时，一般不需要重新编译应用。
<br>

#### 2．缺点
&nbsp;  &nbsp;  &nbsp;  &nbsp;执行时要加载动态库，比静态库慢；发布应用时要同时发布动态库。



