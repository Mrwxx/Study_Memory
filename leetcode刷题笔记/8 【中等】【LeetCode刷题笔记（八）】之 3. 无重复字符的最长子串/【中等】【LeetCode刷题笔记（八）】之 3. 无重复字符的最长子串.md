## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 题目
### （一）概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。
<br>


### （二）示例
示例 1:

```cpp
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```
<br>



示例 2:

```cpp
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```
<br>



示例 3:

```cpp
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     
```
<br>




## 二．题解
### （一）思路
&nbsp;  &nbsp;  &nbsp;  &nbsp;要找一个字符串的最长无重复子串，则可以通过枚举每个字符作为起始位置的最长无重复子串，来比较得出最长的那个子串。

&nbsp;  &nbsp;  &nbsp;  &nbsp;但是，如果全部枚举的话，时间复杂度会很高，可以来找找规律。如果我们选择字符串中的第k个字符作为起始位置，且得到无重复的最长子串的结束位置为rk，则当我们选择第k+1个字符作为起始位置时，从k+1到rk的字符串定是不重复的，因此可以不断增大rk，知道出现右端出现重复字符。
<br>



### （二）技术实现
&nbsp;  &nbsp;  &nbsp;  &nbsp;使用双指针法，左指针指向子串的起始位置，右指针指向结束位置rk。两个指针构造了一个滑动窗口的假象，这是一个动态的窗口，窗口的大小会随着左右指针的移动而变化，左指针每次右移减小了窗口的大小，右指针的右移又增大了窗口的大小。

&nbsp;  &nbsp;  &nbsp;  &nbsp;我们通过不断地右移左指针，每移一次代表着以下一个字符作为起始位置，右指针在保证不重复的情况下右移。如果右指针遇到了重复的字符，则停止移动右指针，计算子串的长度与目前的最大长度进行比较。

&nbsp;  &nbsp;  &nbsp;  &nbsp;当左指针移动到了字符串的末尾时，所有的情况就都考虑到了，最大的长度即为答案。
<br>



### （三）代码实现

C++：

```cpp
class Solution {
public:
	int lengthOfLongestSubstring(string s) {
		//存储的是一个动态的字符串，随着左右指针的移动而变化
		unordered_set<char> occ;
		int n = s.size();
		int rk = -1, ans = 0;
		//i代表着左指针
		for (int i = 0; i < n; ++i) {
			//左指针左移
			if (i != 0) {
				occ.erase(s[i - 1]);
			}
			//右指针右移
			while (rk + 1 < n && !occ.count(s[rk + 1])) {
				occ.insert(s[rk + 1]);
				++rk;
			}
			//判断该次的最长字符串长度
			ans = max(ans, rk - i + 1);
		}
		return ans;

	}
};
```
<br>



Java：

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        // 哈希集合，记录每个字符是否出现过
        Set<Character> occ = new HashSet<Character>();
        int n = s.length();
        // 右指针，初始值为 -1，相当于我们在字符串的左边界的左侧，还没有开始移动
        int rk = -1, ans = 0;
        for (int i = 0; i < n; ++i) {
            if (i != 0) {
                // 左指针向右移动一格，移除一个字符
                occ.remove(s.charAt(i - 1));
            }
            while (rk + 1 < n && !occ.contains(s.charAt(rk + 1))) {
                // 不断地移动右指针
                occ.add(s.charAt(rk + 1));
                ++rk;
            }
            // 第 i 到 rk 个字符是一个极长的无重复字符子串
            ans = Math.max(ans, rk - i + 1);
        }
        return ans;
    }
}
```


