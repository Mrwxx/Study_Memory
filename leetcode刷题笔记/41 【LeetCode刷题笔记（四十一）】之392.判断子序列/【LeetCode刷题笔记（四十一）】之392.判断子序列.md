## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	题干

&nbsp;  &nbsp;  &nbsp;  &nbsp; 给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 你可以认为 s 和 t 中仅包含英文小写字母。字符串 t 可能会很长（长度 ~= 500,000），而 s 是个短字符串（长度 <=100）。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。
<br>


### （二）	示例

示例 1:

```cpp
s = "abc", t = "ahbgdc"

返回 true.
```

示例 2:

```cpp
s = "axc", t = "ahbgdc"

返回 false.
```
<br>



## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp; 两个序列，判断前一个序列是否是后一个序列的子序列。所谓的子序列是指直接在后一个序列中删除一些字符，而不改变原有的字符顺序。一时没有想到思路，对于这种两个序列的比较问题，应该使用双指针来做。在两个序列都设置一个指针，分别比较指针指向的字符，如果不相等，就增加后一个序列的指针；如果相同，则增加前一个序列的指针。最后，如果前一个序列是后一个序列的子序列，那么该序列的指针一定会到达序列的末尾，这就是成功的标志。
<br>



### （二）	代码实现

Java：

```java
class Solution {
    public boolean isSubsequence(String s, String t) {
        int sn = s.length();
        int tn = t.length();
        int i = 0, j = 0;
        while(i < sn & j < tn){
            if(s.charAt(i) == t.charAt(j)){
                i++;
            }
            j++;
        }
        return i==sn;
    }
}
```




