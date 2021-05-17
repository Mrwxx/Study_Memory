## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 题目：两数之和
&nbsp;  &nbsp;  &nbsp;  &nbsp;定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

&nbsp;  &nbsp;  &nbsp;  &nbsp;你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

 

&nbsp;  &nbsp;  &nbsp;  &nbsp;示例:

```cpp
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```
## 二. 解法
### （一）暴力解法
#### 1.思路：
&nbsp;  &nbsp;  &nbsp;  &nbsp;直接用两个for循环，取出两个数相加等于target的两个索引值，并返回

#### 2.代码：

```cpp
class Solution
{
public:
	vector<int> twoSum(vector<int>& nums, int target)
	{
		int len = nums.size();
		
		for (int i = 0; i < len - 1; ++i)
		{
			for (int j = i + 1; j < len; ++j)
			{
				if (nums[i] + nums[j] == target)
					return { i, j };
			}
		}
		return {};
	}
};
```


### （二）两次哈希
#### 1.思路： 
&nbsp;  &nbsp;  &nbsp;  &nbsp;先循环一边将值为键值，索引为值存储到map中，第二遍循环map查找是否有对应于nums[i]的target-nums[i[值。
&nbsp;  &nbsp;  &nbsp;  &nbsp;并且题目有限制，数组中的一个值不能用两次，则 m[target-nums[i]] != i。
&nbsp;  &nbsp;  &nbsp;  &nbsp;如果nums[i[有对应的值target-nums[i]，且target-nums[i]的索引不是i，则返回{ i, m[target - nums[i]] }

#### 2.代码

```cpp
class Solution
{
public:
	vector<int> twoSum(vector<int>& nums, int target)
	{
		unordered_map<int, int> m;
		
		//第一次哈希，将所有vector元素,对应的索引输入map
		for (int i = 0; i < nums.size(); ++i)
		{
			m[nums[i]] = i;
		}

		//第二次哈希
		for (int i = 0; i < nums.size(); ++i)
		{
			//限制，数组中的同一个数不能用两次
			if (m.find(target - nums[i]) != m.end() && m[target - nums[i]] != i)
			{
				return{ i, m[target - nums[i]] };
			}
		}
		return {};
	}
};


