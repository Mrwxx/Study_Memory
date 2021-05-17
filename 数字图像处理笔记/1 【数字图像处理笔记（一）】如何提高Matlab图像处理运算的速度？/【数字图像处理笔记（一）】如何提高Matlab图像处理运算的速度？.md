## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 预先分配内存
&nbsp;  &nbsp;  &nbsp;  &nbsp;即预先分配要存储数据的空间，比如说提前创建0元素数组
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710210517525.gif#pic_center)
&nbsp;  &nbsp;  &nbsp;  &nbsp;预分配还可以减少存储器碎片，因为动态存储器的分配与释放会使碎片不断出现，因此可能有些空间明明是存在的，但因为过于碎片化导致没有足够的连续空间来存储大的值。

## 二.计算向量化
&nbsp;  &nbsp;  &nbsp;  &nbsp;向量化运算意味着简单地将for循环和while循环转换为同样作用的向量或矩阵运算，即增强了代码的可读性，同时极大地加速了计算的速度。

## 三. Matlab的向量化
&nbsp;  &nbsp;  &nbsp;  &nbsp;Matlab中使用函数meshgrid实现二维函数的向量化。可以将一维向量转换为二维向量。如
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710210537531.gif#pic_center)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;注意， meshgrid的输入和输出总是将列放在前面。**
&nbsp;  &nbsp;  &nbsp;  &nbsp;c是n 维的行向量，r是m维的行向量。meshgrid将行向量c和r都转换为 m x n 大小的矩阵，比如说 c = [0, 1], r = [0, 1, 2], 则输出就是 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710210831972.gif#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710210844919.gif#pic_center)
**&nbsp;  &nbsp;  &nbsp;  &nbsp;如何理解呢？可以看到c是 2维的行向量，要构成 3  x 2的矩阵，只能用扩充3行的方式构成；而r是3维的行向量，要构成 3 x 2的矩阵，只能用转为列向量并扩充两列的方式，此方法仅供记忆参考。**

## 三. 举例：
&nbsp;  &nbsp;  &nbsp;  &nbsp;因此，如果我们想设计一个二维的函数，它的功能是求两个坐标变量x= 0，1,2和y=0,1的平方和，怎么做呢？
&nbsp;  &nbsp;  &nbsp;  &nbsp;当然，可以直接用for循环，或者while循环都可以做，但是速度会很慢，之后的代码会看到。我们想到用向量化的方式解决，将x和y都转换为矩阵的形式来计算，速度会大大提升。代码如下：

```python
function [rt,f,g] = twodsin(A, u0, v0, M,N)
%   The comparison is based on implementing the function
%   f(x,y) = Asin(u0x+v0y) for x = 0,1,2,..M-1 and
%   y  = 0,1,2,...N-1. The inputs to the funcion are
%   M and N and the constants in the function.
 
%   First implement using for loops
tic    %start timing
for r = 1:M
    u0x = u0*(r-1);
    for c = 1:N
        v0y = v0*(c-1);
        f(r,c) = A*sin(u0x + v0y);
    end
end
t1 = toc;   %End timing
 
%   Now implement using vectorization
tic     %start timing
r = 0:M-1;
c = 0:N-1;
[C,R] = meshgrid(c,r);
g = A*sin(u0*R + v0*C);
t2 = toc;   %End timing
 
%Compute the ration of the two times
rt = t1/(t2+eps);   % use eps in case t2 is close to zero
f = mat2gray(f);
imshow(f);
g = mat2gray(g);
imshow(g);
```

