## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	Vim中的查找操作
### （一）/ string
&nbsp;  &nbsp;  &nbsp;  &nbsp;从光标处开始查找string字符串，用n向下遍历，用N向上遍历
<br>

### （二）? String
&nbsp;  &nbsp;  &nbsp;  &nbsp;从光标处开始查找string字符串，用n向上遍历，用N向下遍历，与/相反。
<br>

### （三）#
&nbsp;  &nbsp;  &nbsp;  &nbsp;当光标在某个单词处时，我们直接用#键就可以查找该单词，遍历依然用n/N

<br>

## 二．	Vim中的格式调整操作
### （一）	>>
&nbsp;  &nbsp;  &nbsp;  &nbsp;当前光标所在行右移一个tab
<br>

### （二）	n >>
&nbsp;  &nbsp;  &nbsp;  &nbsp;移动光标所在行开始的n行右移一个tab
<br>

### （三） <<
&nbsp;  &nbsp;  &nbsp;  &nbsp;当前光标所在行左移一个tab
<br>

### （四）n >>
&nbsp;  &nbsp;  &nbsp;  &nbsp;移动光标所在行开始的n行左移一个tab
<br>

### （五） gg=G
&nbsp;  &nbsp;  &nbsp;  &nbsp;将整个文件一次性调整格式

<br>

## 三．	Vim中查看man帮助文档
<br>

&nbsp;  &nbsp;  &nbsp;  &nbsp;在Vim中，光标位于想要查询的函数处，按Shift + 小写k查看该函数文档；按住n + 大写K转到第n页文档。
<br>

## 四．	命令模式转编辑模式
### （一）	小写i
&nbsp;  &nbsp;  &nbsp;  &nbsp;在光标之前插入字符
<br>

### （二）	大写I
&nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在的行首插入字符
<br>

### （三）	小写a
&nbsp;  &nbsp;  &nbsp;  &nbsp;在光标之后插入字符
<br>

### （四）	大写A
&nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在的行尾插入字符
<br>

### （五）	小写o
&nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在的行的下一行插入字符
<br>

### （六）	大写O
&nbsp;  &nbsp;  &nbsp;  &nbsp;在光标所在行的上一行插入字符
<br>

### （七）	小写s
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除光标所在的字符，变为插入模式
<br>

### （八）	大写S
&nbsp;  &nbsp;  &nbsp;  &nbsp;删除光标所在的字符，变为插入模式
<br>

### （九）	ESC
&nbsp;  &nbsp;  &nbsp;  &nbsp;退出插入模式到命令模式



