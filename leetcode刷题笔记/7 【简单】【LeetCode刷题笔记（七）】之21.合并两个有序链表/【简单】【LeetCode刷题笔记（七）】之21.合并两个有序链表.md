## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	题目
### （一）	概述
 &nbsp;  &nbsp;  &nbsp;  &nbsp;将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 
<br>


### （二）	示例

```cpp
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

<br>



## 二．	题解
### （一）	思路
 &nbsp;  &nbsp;  &nbsp;  &nbsp;迭代的思路，通过遍历两个链表，比较两个链表对应的每一项，取出较小的一项，生成一个新的链表。
<br>



### （二）	代码实现
<br>



```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* pHead = new ListNode(-1);

        ListNode *pre = pHead;
        while(l1 != nullptr && l2 != nullptr){
            if(l1->val < l2->val){
                pre->next = l1;
                l1 = l1->next;
            }
            else{
                pre->next = l2;
                l2 = l2->next;
            }
            pre = pre->next;
        }

        pre->next = l1 == nullptr ? l2 : l1;
        return pHead->next;

    }
};
```


