## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目

### （一）	题干

&nbsp;  &nbsp;  &nbsp;  &nbsp;给你一个字符串 s ，请你根据下面的算法重新构造字符串：

&nbsp;  &nbsp;  &nbsp;  &nbsp;从 s 中选出 最小 的字符，将它 接在 结果字符串的后面。
&nbsp;  &nbsp;  &nbsp;  &nbsp;从 s 剩余字符中选出 最小 的字符，且该字符比上一个添加的字符大，将它 接在 结果字符串后面。

&nbsp;  &nbsp;  &nbsp;  &nbsp;重复步骤 2 ，直到你没法从 s 中选择字符。

&nbsp;  &nbsp;  &nbsp;  &nbsp;从 s 中选出 最大 的字符，将它 接在 结果字符串的后面。

&nbsp;  &nbsp;  &nbsp;  &nbsp;从 s 剩余字符中选出 最大 的字符，且该字符比上一个添加的字符小，将它 接在 结果字符串后面。

&nbsp;  &nbsp;  &nbsp;  &nbsp;重复步骤 5 ，直到你没法从 s 中选择字符。

&nbsp;  &nbsp;  &nbsp;  &nbsp;重复步骤 1 到 6 ，直到 s 中所有字符都已经被选过。

&nbsp;  &nbsp;  &nbsp;  &nbsp;在任何一步中，如果最小或者最大字符不止一个 ，你可以选择其中任意一个，并将其添加到结果字符串。

&nbsp;  &nbsp;  &nbsp;  &nbsp;请你返回将 s 中字符重新排序后的 结果字符串 。

 <br>


### （二）	示例

```cpp
输入：s = "aaaabbbbcccc"
输出："abccbaabccba"
解释：第一轮的步骤 1，2，3 后，结果字符串为 result = "abc"
第一轮的步骤 4，5，6 后，结果字符串为 result = "abccba"
第一轮结束，现在 s = "aabbcc" ，我们再次回到步骤 1
第二轮的步骤 1，2，3 后，结果字符串为 result = "abccbaabc"
第二轮的步骤 4，5，6 后，结果字符串为 result = "abccbaabccba"
```
 <br>



## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp;很显然，首先按照字典序升序取字符，然后按照字典序的降序取字符，每次取完字符都需要将该字符的计数值减一。由于，所有的字符共26个，我们需要计数每个字符出现的次数，因此创建一个容量为26的数组来计数，然后，每次迭代中有两个循环，一个循环是按照字典序的升序取字符，因此直接从数组的下标0开始迭代到25即表示字典序的升序；反之，表示字典序的逆序。
 <br>



### （二）	代码

Java:


```java
class Solution {
    public String sortString(String s) {
        // 桶计数
        int[] num = new int[26];
        for(int i = 0; i < s.length(); ++i){
            num[s.charAt(i)-'a']++;
        }
        // 由于第一种排序是按照字典序升序排列
        StringBuilder sb = new StringBuilder();
        while(sb.length() < s.length()){
            for(int i = 0; i < 26; ++i){
                if(num[i] != 0){
                    sb.append((char)('a' + i));
                    num[i]--;
                }
            }

            for(int i = 25; i >= 0; i--){
                if(num[i] != 0){
                    sb.append((char)('a'+i));
                    num[i]--;
                }
            }
        }
        return sb.toString();
    }
}
```




