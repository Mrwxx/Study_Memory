## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一.  图像阈值（Threshold）
### (一). 阈值类型
#### 1. 阈值二值化（Threshold Binary）
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先指定像素的灰度值的阈值，遍历图像中像素值，如果像素的灰度值大于这个阈值，则将这个像素设置为最大像素值(8位灰度值最大为255)；若像素的灰度值小于阈值，则将该像素点像素值赋值为0。公式以及示意图如下：
![蓝色水平线代表阈值](https://img-blog.csdnimg.cn/20200528213636393.png)
#### 2. 阈值反二值化（Threshold Binary Inverted）
&nbsp;  &nbsp;  &nbsp;  &nbsp;首先也要指定一个阈值，不同的是在对图像进行阈值化操作时与阈值二值化相反，当像素的灰度值超过这个阈值的时候为该像素点赋值为0；当该像素的灰度值低于该阈值时赋值为最大值。​公式及示意图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528213810371.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
#### 3. 截断（Truncate）
&nbsp;  &nbsp;  &nbsp;  &nbsp;给定像素值阈值，在图像中像素的灰度值大于该阈值的像素点被设置为该阈值，而小于该阈值的像素值保持不变。公式以及示意图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528213844749.png)
#### 4. 阈值取零（Threshold To Zero）
&nbsp;  &nbsp;  &nbsp;  &nbsp;与截断阈值化相反，像素点的灰度值如果大于该阈值则像素值不变，如果像素点的灰度值小于该阈值，则该像素值设置为0.公式以及示意图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528213924799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01yd3h4eHg=,size_16,color_FFFFFF,t_70)
#### 5. 阈值反取零（Threshold To Zero Inverted）
&nbsp;  &nbsp;  &nbsp;  &nbsp;像素值大于阈值的像素赋值为0，而小于该阈值的像素值则保持不变，公式以及示意图如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200528214007166.png)
## 二. API介绍

&nbsp;  &nbsp;  &nbsp;  &nbsp;**阈值化API：  cv::threshold​**

```cpp
double cv::threshold  ( 
	InputArray  src,  
  	OutputArray  dst,  
	double  thresh,  
	double  maxval,  
	int  type  
 )
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;**src**：输入图像，须为单通道灰度图。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**dst**：输出的边缘图像，为单通道黑白图。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**threshold**：表示阈值
&nbsp;  &nbsp;  &nbsp;  &nbsp;**max_value**：表示最大值。
&nbsp;  &nbsp;  &nbsp;  &nbsp;**threshold_type**：表示二值化类型（即对图像取阈值的方法）




&nbsp;  &nbsp;  &nbsp;  &nbsp;**下面是代码展示：**
```cpp
/*****图像阈值化*****/

#include<iostream>
#include<opencv2/opencv.hpp>
#include<string>
using namespace std;

cv::Mat src, dst;
string input_title = "input image";
string output_title = "binary image";
//阈值设置和最大值
int threshold_value = 127;
int threshold_max = 255;
void Threshold_Demo(int, void*);
//阈值类型设置
int type_value = 2;
int type_max = 4;

int main() {
	
	src = cv::imread("1.jpg");
	cv::namedWindow(input_title, cv::WINDOW_NORMAL);
	cv::namedWindow(output_title, cv::WINDOW_NORMAL);
	cv::imshow(input_title, src);

	//阈值调节
	cv::createTrackbar("Threshold Value:", output_title, &threshold_value,
		threshold_max, Threshold_Demo);
	//阈值类型调节
	cv::createTrackbar("Type Value:", output_title, &type_value,
		type_max, Threshold_Demo);
	Threshold_Demo(0, 0);

	cv::waitKey(0);
	return 0;
}

void Threshold_Demo(int, void*) {
	cv::cvtColor(src, dst, cv::COLOR_BGR2GRAY);
	cv::threshold(dst, dst, threshold_value, threshold_max,
		type_value);
	cv::imshow(output_title, dst);

}
```
！**
