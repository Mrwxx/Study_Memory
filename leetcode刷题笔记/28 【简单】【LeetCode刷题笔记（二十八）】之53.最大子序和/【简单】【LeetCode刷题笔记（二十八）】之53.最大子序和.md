## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	题干
&nbsp;  &nbsp;  &nbsp;  &nbsp; 给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
<br>


### （二）	示例

```cpp
输入: [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

<br>



## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp; 从题目来看，这是要找连续的子数组。不像之前做的找不连续的子数组，那个题目可以通过递归和回溯来解决，这种连续的子数组只能通过循环来解决。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于数组必须是连续的，因此用ai代表nums[i]，用f(i)代表以第i个数结尾的【连续子数组的最大和】。每次循环中比较f(i-1)+ai与ai的大小，如果单独的ai比f(i-1)+ai大，那么就取ai作为f(i)，将f(i)与之前的最大值比较。
<br>



### （二）	代码实现

C++：

```cpp
class Solution{
public:
	int maxSubArray(vector<int>& nums){
		int pre = 0, maxAns = nums[0];
		for(x : nums){
			pre = max(pre+x, x);
			maxAns = max(maxAns, pre);
		}
		return maxAns;
	}
};
```

