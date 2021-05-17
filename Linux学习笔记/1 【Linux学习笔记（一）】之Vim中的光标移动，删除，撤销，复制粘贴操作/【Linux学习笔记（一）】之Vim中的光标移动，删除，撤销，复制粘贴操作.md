## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	光标的移动
&nbsp;  &nbsp;  &nbsp;  &nbsp; 在vim编辑器中，可以使用上下左右键来移动光标，也可以使用离字母键较近的hjkl四个键来移动光标。
方向| 键
-|-
上|k
下|j
左|h
右|l

<br>

## 二．	Vim中的删除操作
### （一）	小写x
&nbsp;  &nbsp;  &nbsp;  &nbsp; 删除光标所在的字母
<br>


### （二）	大写X
&nbsp;  &nbsp;  &nbsp;  &nbsp; 删除光标前的字母
<br>


### （三）	dw
&nbsp;  &nbsp;  &nbsp;  &nbsp; 删除从光标开始的那个单词
<br>


### （四）	d0
&nbsp;  &nbsp;  &nbsp;  &nbsp; 按0能够让光标移动到行首，则d0就是delete光标到行首的字符。
<br>


### （五）	d$(D)
&nbsp;  &nbsp;  &nbsp;  &nbsp; 按 $ 能够让光标移动到行尾，则d$就是delete光标到行尾的字符。

&nbsp;  &nbsp;  &nbsp;  &nbsp; D也有同样的作用。
<br>


### （六）	dd
&nbsp;  &nbsp;  &nbsp;  &nbsp; 直接删除光标所在行的所有字符。
<br>


### （七）n dd
&nbsp;  &nbsp;  &nbsp;  &nbsp; 删除从光标所在的行开始的n行字符。

<br>

## 三．	Vim中的撤销操作
### （一）	u
&nbsp;  &nbsp;  &nbsp;  &nbsp; 撤销前一个操作
<br>


### （二）	ctrl + r
&nbsp;  &nbsp;  &nbsp;  &nbsp; 反撤销，即将撤销的操作再恢复。
<br>


## 四．	Vim中的复制粘贴操作
### （一）	yy
&nbsp;  &nbsp;  &nbsp;  &nbsp; 复制光标所在的一行。
<br>

### （二）	n yy
&nbsp;  &nbsp;  &nbsp;  &nbsp; 复制光标所在行之后的n行（包括光标所在行）
<br>


### （三）	dd
&nbsp;  &nbsp;  &nbsp;  &nbsp; 既有删除一行的功能，也有剪切一行的功能。
<br>


### （四）	n dd
&nbsp;  &nbsp;  &nbsp;  &nbsp; 既有删除n行的功能，也有剪切n行的功能。
<br>


### （五）	r
&nbsp;  &nbsp;  &nbsp;  &nbsp; 替换光标所在的字符为输入r之后输入的字符。
<br>


### （六）	小写p
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将剪切板中的内容粘贴到光标所在行的上一行。
<br>


### （七）	大写P
&nbsp;  &nbsp;  &nbsp;  &nbsp; 将剪切板中的内容粘贴到光标所在行的下一行。
<br>


### （八）	选择复制内容
&nbsp;  &nbsp;  &nbsp;  &nbsp; 按v进入可视模式，直接移动光标选中内容，按y复制内容，然后按p/P粘贴内容。




