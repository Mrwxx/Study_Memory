## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 	题目
### （一）	题干
&nbsp;  &nbsp;  &nbsp;  &nbsp; 给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

注意:

&nbsp;  &nbsp;  &nbsp;  &nbsp; 假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回0。
<br>


### （二）	示例

示例 1:

&nbsp;  &nbsp;  &nbsp;  &nbsp; 输入: 123
&nbsp;  &nbsp;  &nbsp;  &nbsp; 输出: 321

 示例 2:

&nbsp;  &nbsp;  &nbsp;  &nbsp; 输入: -123
&nbsp;  &nbsp;  &nbsp;  &nbsp; 输出: -321

示例 3:

&nbsp;  &nbsp;  &nbsp;  &nbsp; 输入: 120
&nbsp;  &nbsp;  &nbsp;  &nbsp; 输出: 21

<br>



## 二．思路
&nbsp;  &nbsp;  &nbsp;  &nbsp; 反转整数，这种做过很多次了，但是这次要限定在 [−231,  231 − 1]范围内，**因此需要考虑反转后的正数或者负数是否依然在这个范围内。**

&nbsp;  &nbsp;  &nbsp;  &nbsp; 当整数溢出该范围时，即正数大于整数最大值MAX_VALUE，负数要小于整数的最小值MIN_VALUE。当我们要将整数的个位数弹出时设为pop，反转后的整数设为ans。

&nbsp;  &nbsp;  &nbsp;  &nbsp; **正数溢出条件：** 

```cpp
ans * 10 + pop > MAX_VALUE
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 则，当ans > MAX_VALUE / 10时必定溢出

&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp; 或者
 
&nbsp;  &nbsp;  &nbsp;  &nbsp; ans == MAX_VALUE / 10且 pop > 7时，必定溢出。**（7 是 MAX_VALUE的个位数）**
<br>


&nbsp;  &nbsp;  &nbsp;  &nbsp; 同理，**负数溢出条件：**

```cpp
ans * 10 + pop < MIN_VALUE
```

&nbsp;  &nbsp;  &nbsp;  &nbsp; 当 ans < MIN_VALUE / 10 时必定溢出 ，

&nbsp;  &nbsp;  &nbsp;  &nbsp; &nbsp;  &nbsp;  &nbsp;  &nbsp; 或者

&nbsp;  &nbsp;  &nbsp;  &nbsp; ans = MIN_VALUE / 10 且 pop < -8 时，必定溢出。**（8是MIN_VALUE的个位数）**

<br>



## 三．代码实现
<br>



```cpp
#include<string>
using namespace std;

class Solution {
public:
	int reverse(int x) {
		//结果值
		int ans = 0;
		//如果为0输出0
		while (x != 0) {
			//弹出个位数
			int pop = x % 10;
			//剩余的数
			x /= 10;
			//正数时，判断溢出条件，INT_MAX是int32正数最大值
			if (ans > INT_MAX / 10 || (ans == INT_MAX / 10 && pop > 7))
				return 0;
			//负数时，判断溢出条件，INT_MIN是int32负数最小值
			if (ans < INT_MIN / 10 || (ans == INT_MIN && pop < -8))
				return 0;
			ans = ans * 10 + pop;

		}
		return ans;
	}

	
};
```


