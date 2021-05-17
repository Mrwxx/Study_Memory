## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	题干

&nbsp;  &nbsp;  &nbsp;  &nbsp;数轴上放置了一些筹码，每个筹码的位置存在数组 chips 当中。

&nbsp;  &nbsp;  &nbsp;  &nbsp;你可以对 任何筹码 执行下面两种操作之一（不限操作次数，0 次也可以）：

&nbsp;  &nbsp;  &nbsp;  &nbsp;将第 i 个筹码向左或者右移动 2 个单位，代价为 0。
&nbsp;  &nbsp;  &nbsp;  &nbsp;将第 i 个筹码向左或者右移动 1 个单位，代价为 1。
&nbsp;  &nbsp;  &nbsp;  &nbsp;最开始的时候，同一位置上也可能放着两个或者更多的筹码。

&nbsp;  &nbsp;  &nbsp;  &nbsp;返回将所有筹码移动到同一位置（任意位置）上所需要的最小代价。
<br>


### （二）	示例

示例 1：

```cpp
输入：chips = [1,2,3]
输出：1
解释：第二个筹码移动到位置三的代价是 1，第一个筹码移动到位置三的代价是 0，总代价为 1。
```

示例 2：

```cpp
输入：chips = [2,2,2,3,3]
输出：2
解释：第四和第五个筹码移动到位置二的代价都是 1，所以最小总代价为 2。
```

 

提示：

&nbsp;  &nbsp;  &nbsp;  &nbsp;1 <= chips.length <= 100
&nbsp;  &nbsp;  &nbsp;  &nbsp;1 <= chips[i] <= 10^9

<br>



## 二．	题解
### （一）	思路

&nbsp;  &nbsp;  &nbsp;  &nbsp;从题目来看，要想用最小的开销来将所有的筹码移动到一个位置，就要尽量使用开销为0的移动方式，在真的无法继续使用开销0的方式时，再使用开销为1的方式。开销为0的方式要求我们间隔两个格子来移动，那么我们可以将所有的奇数位置的筹码都通过这种方式移动到一个奇数位置Odd， 将所有的偶数位置的筹码都移动到跟Odd位置相邻的偶数Even位置，这样移动的开销就为0。此时Odd位置中的就有position数组中所有奇数个数的筹码，Even位置就有position数组中所有偶数个数的筹码。之后，就必须使用开销为1的移动方式了，两个位置中，哪个位置的筹码数少就移动哪个位置的筹码到另一个位置中，问题解决！！！！
<br>



### （二）	代码实现

Java：

```java
class Solution {
    public int minCostToMoveChips(int[] position) {
        int even = 0, odd = 0;
        for(int pos : position){
            if(pos % 2 == 0){
                even++;
            }else{
                odd++;
            }
        }
        return Math.min(even, odd);
    }
}
```


