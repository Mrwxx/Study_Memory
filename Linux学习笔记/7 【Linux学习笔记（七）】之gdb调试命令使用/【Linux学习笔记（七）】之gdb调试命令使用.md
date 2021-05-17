## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	gdb调试
 &nbsp;  &nbsp;  &nbsp;  &nbsp;gdb是UNIX及UNIX-like下的调试工具，优点是能够修复网络断点以及恢复链接等功能。
<br>

## 二．使用gdb
### （一）编译
 &nbsp;  &nbsp;  &nbsp;  &nbsp;要想使用gdb，在编译时需要加上-g参数：

```cpp
gcc main.c -o app -g
```
<br>

### （二）进入gdb

```cpp
gdb 对应的可执行程序名
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;这样就进入了gdb，用gdb开始调试该程序
<br>

### （三）gdb指令

#### 1．r(un)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;启动程序，既可输入run，也可输入r。
<br>

#### 2. start
 &nbsp;  &nbsp;  &nbsp;  &nbsp;启动程序，分布调试，但只是停留在main函数，不会往下执行
<br>

#### 3. n(ext)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;下一条指令，程序向下执行，不能进入函数内部。
<br>

#### 4. s(tep)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;下一条指令，程序向下执行，可以进入函数内部，库函数不能进。
<br>

#### 5. set args 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;设置启动参数，args后面接参数
<br>

#### 6. q(uit)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;退出gdb。
<br>

#### 7．b(reak)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;设置断点，后面可以接行数，也可以接函数名，也可以指定文件名的行数。

如：

```cpp
b 行号 –主函数所在文件的行号
b 函数名
b 文件名：行号 –指定文件的行号
```
<br>

#### 8. i(nfo) b
 &nbsp;  &nbsp;  &nbsp;  &nbsp;查看断点的信息，得到编号。
<br>

#### 9. d(el) 编号
 &nbsp;  &nbsp;  &nbsp;  &nbsp;删除断点。
<br>

#### 10．l(ist) 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;查看代码，默认显示10行。
<br>

#### 11. c(ontinue)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;跳到下一断点。
<br>

#### 12. p(rint)
 &nbsp;  &nbsp;  &nbsp;  &nbsp;打印变量的值
<br>

#### 13. ptype
 &nbsp;  &nbsp;  &nbsp;  &nbsp;打印变量的类型
<br>

#### 14. display
 &nbsp;  &nbsp;  &nbsp;  &nbsp;追踪变量的值，查看该变量的变化情况
<br>

#### 15. undisplay
 &nbsp;  &nbsp;  &nbsp;  &nbsp;不追踪该变量
<br>

#### 16. b 条件
 &nbsp;  &nbsp;  &nbsp;  &nbsp;设置条件断点。



