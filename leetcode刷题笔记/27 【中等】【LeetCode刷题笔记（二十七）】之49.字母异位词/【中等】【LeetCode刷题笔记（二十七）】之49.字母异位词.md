## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	题干

 &nbsp;  &nbsp;  &nbsp;给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。
<br>


### （二）	示例

```cpp
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```
<br>




## 二．	题解
### （一）	思路
 &nbsp;  &nbsp;  &nbsp;这个题就是判断多个字符串在排序后是否是相同的，那么首先我们将每个字符串都进行排序，如果有相同的，就把把相同的字符串放到一起即可。通过循环字符串数组，每个循环对字符串进行排序，通过创建一个map，map的键为已排序的字符串，值为当前未排序的字符串的链表，链表可以将多个字符串连接起来。

 &nbsp;  &nbsp;  &nbsp;每个循环首先对原有的字符串排序，若map中已经有了当前字符串排序后对应的键，说明之前已经有字母异位词存进去了，我们只需要将当前未排序的字符串连接到该键对应的链表后面；若map中没有当前字符串排序后对应的键，说明还没有字母异位词存进去，那么我们将当前未排序的字符串作为键存进map中即可。
<br>



### （二）	代码实现

Java：

```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        if(strs.length== 0){
            return  new ArrayList<>();
        }
        Map<String, List> ans = new HashMap<String, List>();
        for(String s : strs){
            char[] ca = s.toCharArray();
            Arrays.sort(ca);
            String key = String.valueOf(ca);
            if(!ans.containsKey(key)){
                ans.put(key, new ArrayList());
            }
            ans.get(key).add(s);
        }
        return new ArrayList(ans.values());
    }
}
```


