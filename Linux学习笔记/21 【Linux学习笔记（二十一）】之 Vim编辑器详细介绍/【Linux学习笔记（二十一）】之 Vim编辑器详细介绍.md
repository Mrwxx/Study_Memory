## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	vim编辑器

### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;vim是vi编辑器的加强版，是一个纯文本编辑器，能够显示不同文件的颜色，帮助开发者辨别输入错误。可以添加别名，将vi设置为vim，添加到环境变量配置文件~/.bashrc中，永久生效。

```cpp
alias vi=’vim’
```
<br>


### （二）	vim三种工作模式

#### 1.	三种模式的关系
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201222170152266.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)

 
<br>



#### 2.	输入模式
 &nbsp;  &nbsp;  &nbsp;  &nbsp;用于文本编辑，输入数据。在进入输入模式时有以下的命令：

##### （1）	a
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在字符后插入
<br>



##### （2）	A
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在行尾插入

<br>


##### （3）	i
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在字符前插入
<br>



##### （4）	I
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在行首插入
<br>



##### （5）	o
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在光标下插入新行
<br>



##### （6）	O
 &nbsp;  &nbsp;  &nbsp;  &nbsp;在光标上插入新行。
<br>



#### 3.	编辑模式

##### （1）	:w 新文件名
 &nbsp;  &nbsp;  &nbsp;  &nbsp;保存不退出，后面添加文件名表示另存为新的文件。
<br>



##### （2）	:q
 &nbsp;  &nbsp;  &nbsp;  &nbsp;不保存退出。
<br>



##### （3）	:wq
 &nbsp;  &nbsp;  &nbsp;  &nbsp;保存退出。

<br>


##### （4）	:!
 &nbsp;  &nbsp;  &nbsp;  &nbsp;表示强制。如q!，表示强制不保存退出，用于修改文件后不想保存的情况。
 
 &nbsp;  &nbsp;  &nbsp;  &nbsp;如wq!，强制保存退出，只有文件的所有者或root用户，对文件没有写权限时，强制写入数据使用。
<br>



#### 4.	命令模式

##### （1）	移动光标

 &nbsp;  &nbsp;  &nbsp;  &nbsp;HJKL，在键盘上是从左到右的方向。H是左移，J是下移，K是上移，L是右移。当然，也可以使用方向键移动。

```cpp
gg：移动到文件头
G： 移动到文件尾
```

```cpp
^:  移动到行首
$:  移动到行尾
```

```cpp
:n : n是数字，表示移动到第几行
```
<br>



##### （2）	删除或剪切
 &nbsp;  &nbsp;  &nbsp;  &nbsp;删除也可以用于剪切，因为内容已经放到剪切板中了。

```cpp
x: 删除单个字母
nx: 删除n个字母，n是数字
```

```cpp
dd: 删除整行
ndd: 删除多行
:n1,n2d: 删除指定范围的行，n1，n2都是行数
dG: 从光标所在行删除到文件尾
```

```cpp
p: 粘贴到光标后
P（大写）: 粘贴到光标前
```
<br>



##### （3）	复制

```cpp
yy： 复制单行
nyy: 复制多行
```
<br>



##### （4）	撤销

```cpp
u: 撤销，可以一直撤销到文件打开时的状态
ctrl+r: 反撤销
```

<br>


##### （5）	替换

```cpp
r: 替换光标处的字符
R: 从光标所在处开始替换字符，按ESC结束
```
<br>



##### （6）	vim配置文件
 &nbsp;  &nbsp;  &nbsp;  &nbsp;下面的参数设置，多数需要在vim中配置才能够永久生效。

显示行号

```cpp
:set nu
:set nonu 
```

显示颜色帮助

```cpp
:syntax on
:syntax off
```

查找字符串高亮显示

```cpp
:set hlsearch
:set nohlsearch
```

设置右下角的状态栏

```cpp
:set ruler
:set noruler
```

设置左下角显示模式的状态栏

```cpp
:set showmode
:set noshowmode
```

设置是否显示隐藏字符，如Tab键使用”^I”， 回车键使用”$”。

```cpp
:set list
:set nolist
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;这些参数设置都需要写入到vim的配置文件~/.vimrc中，才能够永久生效。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;当我们将Windows下的文本文件传入Linux中时，由于Windows中的回车是”^M$”，与Linux中的大为不同，会导致在Linux中无法运行。我们可以通过命令”dos2unix”转换，也可以通过”unix2dos”转换为Windows格式。
<br>



##### （7）	查找

```cpp
/查找内容 ： 从光标所在行向下查找
?查找内容 ： 从光标所在行向上查找
n:  下一个
N:  上一个
```

<br>


##### （8）	替换

```cpp
:1,10s/old/new/g : 替换1到10行中的所有old为new
:%s/old/new/g : 替换整个文件的old为new
```

如注释文件的前10行，使用正则表达式^作为开头：

```cpp
:1,10s/^/#/g
```

取消注释：

```cpp
:1,10s/^#//g
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;当要替换“/”符号时，需要在它前面添加转移符\，因此如果要在文件的前10行添加//，如下所示：

```cpp
:1,10s/^/\/\//g
```
<br>



### （三）	Vim导入其他文件内容或命令内容

#### 1.	导入其他文件内容

```cpp
:r 文件名
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;将其他文件内容导入光标所在位置

<br>


#### 2.	在VIM中执行系统命令

```cpp
:!命令
```
<br>



#### 3.	导入命令结果

```cpp
:r !命令
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;将命令结果导入光标所在行
<br>



### （四）	设定快捷键

```cpp
:map 快捷键 执行的命令
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;按 CTRL+p 时， 在行首加入注释

```cpp
:map ^p I#<ESC>
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;注意：^p快捷键不能手动输入，需要输入Ctrl+v+p。

<br>


### （五）	字符替换
 &nbsp;  &nbsp;  &nbsp;  &nbsp;当我们输入一个长字符串的次数较多时，可以用一个简单的字符串替换

```cpp
:ab 源字符 替换的字符
```

```cpp
:ab mymail xxxxxxx@qq.com 
```
<br>



### （六）	打开多文件

 &nbsp;  &nbsp;  &nbsp;  &nbsp;在vim中可以同时打开两个文件，可以上下显示，也可以左右显示：

```cpp
-o小写，表示上下分屏
-O大写，表示左右分屏
vim -o a b
vim -O a b
```

 &nbsp;  &nbsp;  &nbsp;  &nbsp;当需要在两个文件中切换时，首先按 CTRL + w，然后按左右箭头或者上下箭头即可切换。







