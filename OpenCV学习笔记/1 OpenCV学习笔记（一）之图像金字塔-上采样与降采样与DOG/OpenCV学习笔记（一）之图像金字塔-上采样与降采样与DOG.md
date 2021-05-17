## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 图像金字塔
&nbsp;  &nbsp;  &nbsp;  &nbsp;一个图像金字塔式一系列的图像组成，最底下的图形尺寸最大，最上方的图像尺寸最小。
高斯金字塔是从底向上，逐层降采样得到的。
高斯金字塔的生成过程分为两步：
1. 对当前层进行高斯模糊。
2. 删除当前层的偶数行与列。
3. 即可得到上一层的图像，这样上一层跟下一层相比，都只有它的1/4大小。
## 二. 上采样与降采样
&nbsp;  &nbsp;  &nbsp;  &nbsp;降采样之后的图像大小是原图像M × N 的 M/2 × N/2，就是对原图像删除偶数行与列，即得到降采样之后的上一层的图片。

1. 上采样（cv::pyrUp）-zoom in 放大,生成的图像是原图的宽和高各放大两倍

```cpp
pyrUp(Mat src, Mat dst, Size(src.cols*2, src.rows*2))
```

2. 降采样（cv::pyrDown）-zoom out 缩小,生成的图像是原图的宽和高各缩小两倍。

```cpp
pyrDown(Mat src, Mat dst, Size(src.cols/2, src/rows/2))
```


## 三. 高斯不同（Difference of Gaussian-DOG）
&nbsp;  &nbsp;  &nbsp;  &nbsp;**定义**  ：就是把同一张图像在不同的参数下做高斯模糊之后的结果**相减**，得到的输出图像。成为高斯不同。高斯不同是图像的内在特征，在灰度图像增强，角点检测中经常用到。

**下面是代码：**

```cpp
/*****图像金字塔*****/
/*上采样，降采样*/

#include<iostream>
#include<opencv2/opencv.hpp>
using namespace std;

int main() {
	cv::Mat src, dst;
	src = cv::imread("1.jpg");
	cv::namedWindow("input image", cv::WINDOW_NORMAL);
	cv::imshow("input image", src);

	cv::pyrUp(src, dst, cv::Size(src.cols * 2, src.rows * 2));
	cv::namedWindow("up image", cv::WINDOW_NORMAL);
	cv::imshow("up image", dst);

	cv::Mat s_down;
	cv::pyrDown(src, s_down, cv::Size(src.cols / 2, src.rows / 2));
	cv::namedWindow("down image", cv::WINDOW_NORMAL);
	cv::imshow("down image", s_down);

	//DOG
	cv::Mat gray_src, g1, g2, dogImg;
	cv::cvtColor(src, gray_src, cv::COLOR_BayerBG2GRAY);
	//两次 高斯模糊
	cv::GaussianBlur(gray_src, g1, cv::Size(3, 3), 0, 0);
	cv::GaussianBlur(g1, g2, cv::Size(3, 3), 0);
	//高斯模糊的结果相减
	cv::subtract(g1, g2, dogImg, cv::Mat());
	//将DOG的显示效果归一化到0-255范围内，更加明显
	cv::normalize(dogImg, dogImg, 255, 0, cv::NORM_MINMAX);

	cv::namedWindow("DOG Image", cv::WINDOW_NORMAL);
	cv::imshow("DOG Image", dogImg);
	cv::waitKey(0);
	return 0;
}
```


