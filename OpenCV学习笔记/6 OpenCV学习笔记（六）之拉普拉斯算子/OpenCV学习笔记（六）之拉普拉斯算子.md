## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">


## 一. 拉普拉斯算子的理论：
&nbsp;  &nbsp;  &nbsp;  &nbsp;最大变化处的二阶微分值为0，即边缘在二阶微分时为零值、通过二阶微分计算，可以计算图像二阶微分，提取边缘。

## 二. 用拉普拉斯算子处理问题的流程：
1. 高斯模糊-去噪 GaussianBlur()
2. 转换为灰度图像 cvtColor()
3. 拉普拉斯-二阶导数计算 Laplacian()
4. 取绝对值  convertScaleAbs()

## 三. **下面是代码：**

```cpp
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

	cv::Mat gray_src;
	//先高斯模糊
	cv::GaussianBlur(src, dst, cv::Size(3, 3), 0, 0);
	//再转成灰度图
	cv::cvtColor(dst, gray_src, cv::COLOR_BGR2GRAY);
	cv::imshow("gray image", gray_src);

	cv::Mat edge_image;
	cv::Laplacian(gray_src, edge_image, CV_16S, 3);
	cv::convertScaleAbs(edge_image, edge_image);

	//二值化，更清晰
	cv::threshold(edge_image, edge_image, 0, 255, cv::THRESH_OTSU | cv::THRESH_BINARY);
	cv::namedWindow(ou_title, cv::WINDOW_NORMAL);
	cv::imshow(ou_title, edge_image);

	cv::waitKey(0);
	return 0;
	
}
```


