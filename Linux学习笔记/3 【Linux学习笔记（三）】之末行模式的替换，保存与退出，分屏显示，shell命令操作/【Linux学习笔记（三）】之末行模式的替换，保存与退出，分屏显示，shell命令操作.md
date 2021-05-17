## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	末行模式的替换操作
### （一）: s/src/des
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将光标所在行的第一个src替换为des
<br>

### （二）: s/src/des/g
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将光标所在行的所有src替换为des
<br>

### （三）:%s/src/des
&nbsp;  &nbsp;  &nbsp;  &nbsp; 替换所有行的第一个匹配的src为des
<br>

### （四）:%s/src/des/g
&nbsp;  &nbsp;  &nbsp;  &nbsp; 替换文件中的所有src为des

<br>

## 二．	末行模式中的保存与退出
### （一）:wq
&nbsp;  &nbsp;  &nbsp;  &nbsp; 保存并退出
<br>

### （二）	:w
&nbsp;  &nbsp;  &nbsp;  &nbsp; 仅仅保存，没有退出操作
<br>

### （三）:q
&nbsp;  &nbsp;  &nbsp;  &nbsp; 仅退出，没有保存修改的文件

<br>

### （四）:q!
&nbsp;  &nbsp;  &nbsp;  &nbsp; 强制退出
<br>

### （五）:x
&nbsp;  &nbsp;  &nbsp;  &nbsp; 保存并退出

<br>

## 三．	末行模式中的分屏显示
### （一）:sp filename
&nbsp;  &nbsp;  &nbsp;  &nbsp; 横屏显示多个文件 
<br>

### （二）:vsp filename
&nbsp;  &nbsp;  &nbsp;  &nbsp; 竖屏显示多个文件
<br>

### （三）CTRL + w
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在多个文件间切换
<br>

### （四）:q
&nbsp;  &nbsp;  &nbsp;  &nbsp; 退出一个文件
<br>

### （五）:qall
&nbsp;  &nbsp;  &nbsp;  &nbsp; 退出所有文件
<br>

### （六）:wqall
&nbsp;  &nbsp;  &nbsp;  &nbsp; 保存所有文件并退出
<br>


## 四．	末行模式下执行shell命令
<br>

**:! Command**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 在文件中可以直接执行shell命令。



