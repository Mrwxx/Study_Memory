## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一. 图像边缘问题
&nbsp;  &nbsp;  &nbsp;  &nbsp;图像做卷积操作时，边缘像素无法处理到，原因在于边缘像素没有完全和kernel重叠。因此，当3 X 3滤波时，边缘有1个像素没有被处理，5 X 5时有2个像素没有被处理。
## 二. 处理边缘
&nbsp;  &nbsp;  &nbsp;  &nbsp;在卷积之前增加边缘像素，填充像素值。比如在3 X 3的kernel作用的图像上添加1个像素的边缘，这样就能确保图像的边缘被处理，在卷积处理之后再去掉这些添加的边缘。

## 三. 处理边缘的方式
&nbsp;  &nbsp;  &nbsp;  &nbsp;**OpenCV中默认的边缘处理方式是**: BORDER_DEFAULT,此外还有以下几种：
1. **BORDER_CONSTANT**--用指定像素值填充边缘
2. **BORDER_REPLICATE**--用已经存在的边缘像素值填充边缘
3. **BORDER_WRAP**--用另一边的像素来填充边缘

## 四. 添加边缘API
&nbsp;  &nbsp;  &nbsp;  &nbsp;**cv::copyMakeBorder**
```cpp
copyMakeBorder(
	Mat src,	//输入图像
	Mat dst,	//输出图像
	int top,		//边缘尺寸
	int bottom,
	int left, 
	int right,
	int borderType,	//边缘类型
	Scalar color
)
```
## 下面是代码：

```cpp
/*****处理边缘*****/

#include<iostream>
#include<opencv2/opencv.hpp>
#include<string>
using namespace std;

string in_title = "input image";
string ou_title = "output image";
int main() {
	cv::Mat src, dst;
	src = cv::imread("1.jpg");
	cv::namedWindow(in_title, cv::WINDOW_NORMAL);
	cv::imshow(in_title, src);
	//边缘的尺寸
	int top = (int)(0.05 * src.rows);
	int bottom = (int)(0.05 * src.rows);
	int left = (int)(0.05 * src.cols);
	int right = (int)(0.05 * src.cols);
	//随机数
	cv::RNG rng(12345);
	int border_type = cv::BORDER_DEFAULT;

	int c = 0;
	while (true) {
		c = cv::waitKey(500);
		//ESC
		if ((char)c == 27)
			break;
		//各种边缘类型
		else if ((char)c == 'r')
			border_type = cv::BORDER_REPLICATE;
		else if ((char)c == 'w')
			border_type = cv::BORDER_WRAP;
		else if ((char)c == 'd')
			border_type = cv::BORDER_DEFAULT;
		else if ((char)c == 'c')
			border_type = cv::BORDER_CONSTANT;
		else if ((char)c == 'i')
			border_type = cv::BORDER_ISOLATED;
		
		//颜色
		cv::Scalar color = cv::Scalar(rng.uniform(0, 255), rng.uniform(0, 255),
			rng.uniform(0, 255));
		//添加边缘
		cv::copyMakeBorder(src, dst, top, bottom, left, right,
			border_type, color);
		cv::namedWindow(ou_title, cv::WINDOW_NORMAL);
		cv::imshow(ou_title, dst);

	}
	return 0;
}
```
！！​**
