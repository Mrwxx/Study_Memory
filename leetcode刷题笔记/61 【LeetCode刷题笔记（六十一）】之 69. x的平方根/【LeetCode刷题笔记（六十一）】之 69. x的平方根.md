## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一．	题目
### （一）	题干

&nbsp;  &nbsp;  &nbsp;  &nbsp; 实现 int sqrt(int x) 函数。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 计算并返回 x 的平方根，其中 x 是非负整数。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

<br>


### （二）	示例

示例 1:

```cpp
输入: 4
输出: 2
```

示例 2:

```cpp
输入: 8
输出: 2
说明: 8 的平方根是 2.82842..., 
     由于返回类型是整数，小数部分将被舍去。
```

<br>



## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp; 实现平方根函数，返回平方根的整数部分。这种题目使用二分法来解决，不断地寻找中间数来比较它的平方与x的关系，如果它的平方大于x，说明要寻找的数比该数还要小，因此缩小右边界，继续寻找；如果它的平方小于x，说明要寻找的数可能比该数要大，注意我这里说的是可能，因为该数有可能就是x的平方根的整数部分，因此，只要是该数的平方<=x的，都要当做最后的输出记录下来；如果它的平方等于x，则直接找出了最终结果。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 按照这种思路进行迭代，最终得到输出的结果。
<br>



### （二）	代码实现


Java：

```java
class Solution {
    public int mySqrt(int x) {
        // x >= 0
        //使用二分法来解决, 在 0 - x的范围内
        int left = 0, right = x, ans = -1;
        while(left <= right){
            int mid = left + (right-left)/2;
            if((long)mid*mid < x){
                ans = mid;
                left = mid+1;
            }else if((long)mid * mid == x){
                ans = mid;
                return ans;
            }else{
                right = mid-1;
            }
        }
        return ans;

    }
}
```


