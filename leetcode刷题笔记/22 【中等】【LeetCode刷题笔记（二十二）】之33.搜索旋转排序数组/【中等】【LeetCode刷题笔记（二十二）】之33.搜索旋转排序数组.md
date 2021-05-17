## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	题干

&nbsp;  &nbsp;  &nbsp;  &nbsp;假设按照升序排序的数组在预先未知的某个点上进行了旋转。

&nbsp;  &nbsp;  &nbsp;  &nbsp;( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。

&nbsp;  &nbsp;  &nbsp;  &nbsp;搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

&nbsp;  &nbsp;  &nbsp;  &nbsp;你可以假设数组中不存在重复的元素。

&nbsp;  &nbsp;  &nbsp;  &nbsp;你的算法时间复杂度必须是 O(log n) 级别。

<br>


### （二）	示例

示例 1:

```cpp
输入: nums = [4,5,6,7,0,1,2], target = 0
输出: 4
```

示例 2:

```cpp
输入: nums = [4,5,6,7,0,1,2], target = 3
输出: -1
```
<br>



## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp;仔细读题，原有的数组为升序，在某个点进行旋转以后，变为了两个分开的升序数组，如【0, 1, 2, 4, 5, 6, 7】在数字4的位置进行了旋转，4,5,6,7的位置移到了前面，0,1,2的位置移到了后面，变为了两段升序数组【4,5,6,7,0,1,2】
<br>



&nbsp;  &nbsp;  &nbsp;  &nbsp;那么，如果用时间复杂度为O(logn)进行查找呢指定的数呢？

&nbsp;  &nbsp;  &nbsp;  &nbsp;从logn的复杂度，我们联想到了二分法，但是二分法是依靠于有序数列进行的，这里是两段有序序列，是没办法直接进行二分法的。但是，如果我们选取了中间值7后发现，左边的数列为4， 5， 6.右边的数列为0， 1， 2，这里恰巧两边都是有序的；但是不论怎么取中间值，就算是假设中间值为6，左边的数列为4，5，右边的数列为7,0,1,2。左右两段数列总会有一段为有序的，因此可以在这一段中使用二分法。

&nbsp;  &nbsp;  &nbsp;  &nbsp;在有序的一端数列中进行二分法后，判断target在不在这一段数列中，如果在，则继续搜索，如果不在，则到另一端数列中查找。

<br>


### （二）	代码实现

C++：

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = nums.size();
        if (!n)
            return -1;
        if (n == 1) {
            return nums[0] == target ? 0 : -1;
        }
        //二分法
        int l = 0, r = n - 1;
        while (l <= r) {
            int mid = (l + r) / 2;
            if (nums[mid] == target)
                return mid;
            //如果左边为有序的
            if (nums[0] <= nums[mid]) {
                //target在有序数列中
                if (nums[0] <= target && target < nums[mid]) {
                    r = mid - 1;
                }
                //target不在有序数列中，则在右边的数列中查找
                else {
                    l = mid + 1;
                }

            }
            //右端数列为有序的
            else {
                //如果target在右端数列中
                if (nums[mid] < target && target <= nums[n - 1]) {
                    l = mid + 1;
                }
                //target不在右端数列中，则在左端数列中查找
                else {
                    r = mid - 1;
                }
            }
        }
        return -1;
    }
};
```



