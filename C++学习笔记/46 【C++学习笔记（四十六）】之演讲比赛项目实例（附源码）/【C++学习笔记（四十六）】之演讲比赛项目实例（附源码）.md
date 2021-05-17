## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 比赛规则：

 &nbsp;  &nbsp;  &nbsp;  &nbsp;某市举行一场演讲比赛（ speech_contest ），共有24个人参加。比赛共三轮，前两轮为淘汰赛，第三轮为决赛。


 &nbsp;  &nbsp;  &nbsp;  &nbsp;**比赛方式：分组比赛，每组6个人；选手每次要随机分组，进行比赛；**


 &nbsp;  &nbsp;  &nbsp;  &nbsp;第一轮分为4个小组，每组6个人。比如编号为: 100-123.  整体进行抽签（draw）后顺序演讲。当小组演讲完后，淘汰组内排名最后的三个选手，然后继续下一个小组的比赛。

		
 &nbsp;  &nbsp;  &nbsp;  &nbsp;第二轮分为2个小组，每组6人。比赛完毕，淘汰组内排名最后的三个选手，然后继续下一个小组的比赛。

	
 &nbsp;  &nbsp;  &nbsp;  &nbsp;第三轮只剩下1组6个人，本轮为决赛，选出前三名。
	
 	       
 &nbsp;  &nbsp;  &nbsp;  &nbsp; **比赛评分：10个评委打分，去除最低、最高分，求平均分每个选手演讲完由10个评委分别打分**。该选手的最终得分是去掉一个最高分和一个最低分，求得剩下的8个成绩的平均分。选手的名次按得分降序排列。

**用STL编程，求解这个问题**

 &nbsp;  &nbsp;  &nbsp;  &nbsp;1.	请打印出所有选手的名字与参赛号，并以参赛号的升序排列。

 &nbsp;  &nbsp;  &nbsp;  &nbsp;2.	打印每一轮比赛后，小组比赛成绩和小组晋级名单

<br>

## 二. 需求分析：
<br>

1.	生成选手： 姓名，编号，得分
2.	第一轮比赛： 选手抽签，进行比赛，展示结果
3.	第二轮比赛： 选手抽签，进行比赛，展示结果
4.	第三轮比赛： 选手抽签，进行比赛，展示结果

<br>

## 三. 实现思路：
<br>


 &nbsp;  &nbsp;  &nbsp;  &nbsp;1. 选手封装成类 Speaker，存储姓名(string name)，得分信息(数组score[3]，因为可能有三轮比赛)

 &nbsp;  &nbsp;  &nbsp;  &nbsp;2. 选手的编号存在vector容器v1中

 &nbsp;  &nbsp;  &nbsp;  &nbsp;3. 用map<int, Speaker>将选手的编号和选手信息映射起来

 &nbsp;  &nbsp;  &nbsp;  &nbsp;4. 第一轮晋级编号放在vector容器v2中

 &nbsp;  &nbsp;  &nbsp;  &nbsp;5. 第二轮晋级编号放在vector容器v3中

 &nbsp;  &nbsp;  &nbsp;  &nbsp;6. 第三轮晋级编号放在vector容器v4中

 &nbsp;  &nbsp;  &nbsp;  &nbsp;7. 每个小组的比赛得分信息，按照从大到小放在multimap中

 &nbsp;  &nbsp;  &nbsp;  &nbsp;8. 选手的打分，用deque容器排序，删除最高最低值


## **公号回复 “演讲”，获取源码！！！**
