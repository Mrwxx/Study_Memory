## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. Canny算法步骤：
1. 高斯模糊-GaussianBlur
2. 灰度转换-cvtColor
3. 计算梯度-Sobel/Scharr
4. 非最大信号抑制
5. 高低阈值输出二值图像

## 二. 高低阈值输出二值图像
&nbsp;  &nbsp;  &nbsp;  &nbsp;T1,T2为阈值，凡是高于T2的都保留，凡是低于T1的都丢弃，从高于T2的像素出发，凡是大于T1且相互连接的都保留。最终得到一个输出二值图像。
&nbsp;  &nbsp;  &nbsp;  &nbsp;推荐的高低阈值比值为T2:T1= 3:1/2:1，其中T2为高阈值，T1为低阈值。

## 三. Canny API介绍：

```cpp
Canny(
	InputArray src,		//8-bit的输入图像
	OutputArray edges,	//输出边缘图像
	double threshold1,	//低阈值
	double threshold2,	//高阈值
	int aptertureSize,		//Sobel算子的Size，通常取3
	bool L2gradient		//选择true表示用L2来归一化，否则用L1
)
```

## 四. **下面是代码：**
```cpp
/*****Canny算子*****/

#include<iostream>
#include<opencv2/opencv.hpp>
#include<string>
using namespace std;

string in_title = "input image";
string ou_title = "output image";
int t1_value = 50;				//最小阈值
int max_value = 255;			//最大阈值
cv::Mat src, dst;
cv::Mat gray_src;
void Canny_Demo(int, void*);
int main() {
	src = cv::imread("1.jpg");
	cv::namedWindow(in_title, cv::WINDOW_NORMAL);
	cv::imshow(in_title, src);
	
	cv::cvtColor(src, gray_src, cv::COLOR_BGR2GRAY);
	cv::namedWindow(ou_title, cv::WINDOW_NORMAL);
	cv::createTrackbar("Threshold Value: ", ou_title, &t1_value,
		max_value, Canny_Demo);
	Canny_Demo(0, 0);

	cv::waitKey(0);
	return 0;
}

void Canny_Demo(int, void*) {
	cv::Mat edge_image;
	//先模糊操作，再Canny操作
	cv::blur(gray_src, gray_src, cv::Size(3,3), cv::Point(-1, -1),
		cv::BORDER_DEFAULT);
	cv::Canny(gray_src, edge_image, t1_value, t1_value*3, 3, false);

	dst.create(src.size(), src.type());
	//掩膜操作
	//src.copyTo(dst, edge_image);
	
	cv::imshow(ou_title, edge_image);
}
```


