## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp; 编写一个函数来查找字符串数组中的最长公共前缀。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 如果不存在公共前缀，返回空字符串 ""。
<br>


### （二）示例
示例 1:

```cpp
输入: ["flower","flow","flight"]
输出: "fl"
```
<br>



示例 2:

```cpp
输入: ["dog","racecar","car"]
输出: ""
```
<br>



解释: 输入不存在公共前缀。
说明: 所有输入只包含小写字母 a-z 。

<br>



## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp; 两个字符串的最长公共前缀比较好找，直接逐位比较两个字符串的每个字符即可，如果相同，则比较下一位，知道两个字符不相同为止。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 那么，多个字符串如何寻找最长公共前缀呢？可以先对两个字符串找出最长公共前缀，记录下这个前缀，再用这个前缀与下一个字符串进行比较，迭代循环。如果前缀的长度为0，则说明这些字符串没有公共前缀，则直接跳出循环即可。
<br>



### （二）	代码实现
<br>




```cpp
class Solution {
public:
	string longestCommonPrefix(vector<string>& strs) {
		if (!strs.size())
			return "";
		string prefix = strs[0];
		int count = strs.size();
		for (int i = 1; i < count; ++i) {
			//迭代循环
			prefix = longestCommonPrefix(prefix, strs[i]);
			if (!prefix.size())
				break;
		}
		return prefix;
	}

	//两个字符串的最长公共前缀
	string longestCommonPrefix(const string& str1, const string& str2) {
		int length = min(str1.size(), str2.size());
		int index = 0;
		while (index < length && str1[index] == str2[index])
			index++;
		return str1.substr(0, index);
	}

};
```

