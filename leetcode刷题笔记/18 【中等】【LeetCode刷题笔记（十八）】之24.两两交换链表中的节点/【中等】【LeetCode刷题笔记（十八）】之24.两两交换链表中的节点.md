## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	题干


&nbsp;  &nbsp;  &nbsp;  &nbsp; 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

&nbsp;  &nbsp;  &nbsp;  &nbsp; 你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
<br>


### （二）	示例

```cpp
给定 1->2->3->4, 你应该返回 2->1->4->3.
```
<br>




## 二．	题解
### （一）	思路
&nbsp;  &nbsp;  &nbsp;  &nbsp; 从题目来看，题目给出一个链表，两两交换相邻的节点，那些已经交换过的节点不需要再次交换。这又是一个从头开始，一直循环到链表的结尾的问题，又可以使用递归来解决，每次递归交换两个节点，返回递归中的第二个节点，之后再进入下一个递归，每个递归函数传入一个节点，直到链表中没有节点了。最后返回的是原始链表的第二个节点。
<br>



### （二）	代码实现
#### 1. Java 


```java
public class ChangeList24 {
    public ListNode swapPairs(ListNode head){
        //如果链表为空或者链表只有头结点
        if((head==null)|| (head.next==null)){
            return head;
        }

        //交换传入的节点和下一个节点
        ListNode firstNode = head;
        ListNode secondNode = head.next;
        //进入递归函数
        //传入的节点的下一个节点是下一个递归函数的返回值
        firstNode.next = swapPairs(secondNode.next);
        secondNode.next = firstNode;

        //返回交换后的第二个节点
        return secondNode;
    }
}
```


