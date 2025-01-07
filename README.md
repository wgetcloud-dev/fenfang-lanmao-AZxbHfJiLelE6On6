[合集 \- C\+\+之OpenCV入门到提高(6\)](https://github.com)[1\.C\+\+之OpenCV入门到提高001：使用 Visual Studio2022 配置 OpenCV 环境2024\-10\-28](https://github.com/PatrickLiu/p/18499247)[2\.C\+\+之OpenCV入门到提高002：加载、修改、保存图像2024\-10\-29](https://github.com/PatrickLiu/p/18511288)[3\.C\+\+之OpenCV入门到提高003：矩阵的掩膜（Mask）处理2024\-11\-01](https://github.com/PatrickLiu/p/18512814)[4\.C\+\+之OpenCV入门到提高004：Mat 对象的使用2024\-11\-08](https://github.com/PatrickLiu/p/18513728):[西部世界westwingy加速器](https://www.yicheer.com)[5\.C\+\+之OpenCV入门到提高005：图像操作2024\-11\-15](https://github.com/PatrickLiu/p/18540141)6\.C\+\+之OpenCV入门到提高007：调整图像亮度和对比度01\-07收起
**一、介绍**　　　　今天是这个系列《C\+\+之 Opencv 入门到提高》的第七篇文章。这篇文章也不难，介绍如何调整图像的亮度。图像本质上也是数据，既然是数据，我们就可以针对图像的每个像素进行增加或者减少的的操作，这样的结果就是图片变暗或者变亮了。这个调整的过程不是随便操作、无限制的，因为有些结果并不是我们想要的，我们为了让图片每个像素变化的更合理，就可以使用【saturate\_cast】函数进行控制，好好的体会一下吧。这都是基础，为以后的学习做好铺垫。虽然操作很简单，但是背后有很多东西需要我们深究，才能做到知其然知其所以然。OpenCV 具体的简介内容，我就不多说了，网上很多，大家可以自行脑补。　　　　OpenCV 的官网地址：[https://opencv.org/](https://github.com)，组件下载地址：[https://opencv.org/releases/](https://github.com)。　　　　OpenCV 官网学习网站：[https://docs.opencv.ac.cn/4\.10\.0/index.html](https://github.com)　　　　我需要进行说明，以防大家不清楚，具体情况我已经罗列出来。　　　　　　　　操作系统：Windows Professional 10（64位）　　　　　　　　开发组件：OpenCV – 4\.10\.0　　　　　　　　开发工具：Microsoft Visual Studio Community 2022 (64 位) \- Current版本 17\.8\.3　　　　　　　　开发语言：C\+\+（VC16）**二、知识学习**　　　　接口很简单，不用多说，仔细研究一下原理更有用。


```
 1 #include 
 2 #include 
 3 #include 
 4 
 5 using namespace std;
 6 using namespace cv;
 7 
 8 
 9 int main()
10 {
11     //图像变换可以看做如下：
12     //1、像素变换，也就是点操作，修改像素的值，获取像素的值，调整图像的亮度和对比度等类似操作。
13     //2、领域操作，也是区域操作。可以做图像的卷积，图像特征的提取，图像梯度的计算，也可以对图像进行深度处理，比如：角点检测，模糊处理，平滑处理。
14     //
15     //调整图像亮度和对比度属于像素变换，也就是点操作。
16     //g(i,j)=af(i,j)+b,其中 a >0 ,b 是增益变量。f(i,j)表示输入图像的每个像素点，g(i,j)表示输出图像的每个像素点。
17     //a > 0，对于常规图像来说必须大于零，因为 opencv 是基于 RGB 格式来处理图像的，它的值是【0-255】，所以必须大于零。如果是 tiff 格式的图像，这个值就有可能是负值。
18     //a 这个参数的作用就是用于提高对比度的，它是乘法，是倍数增加的，所以，新的图像像素值就会比原始图像像素值有更大的变化，效果就是对比度提高了。
19     // b 就是提高图像的亮度，越亮，值越接近 255，也就是越接近白色。说白了如果我们想提高图像的亮度，就要提高 b(增益变量)的值。
20 
21 
22     //2、重要API
23     //    Mat image=Mat::zeros(src.size(),src.type())创建一张和原图一项大小和类型的空白图像，像素初始值是0.
24     //  saturate_cast(value)确保值在【0-255】之间。
25     //    Mat.at(x,y)[index]=value 给指定通道每个像素赋值。
26 
27 
28     //3、代码演示
29 
30     Mat src, dst;
31     src = imread("D:\\360MoveData\\Users\\Administrator\\Desktop\\TestImage\\3.jpg", IMREAD_COLOR);
32     if (src.empty())
33     {
34         cout << "图像加载失败！！！" << endl;
35         return    -1;
36     }
37 
38     namedWindow("原始图像", WINDOW_AUTOSIZE);
39     imshow("原始图像", src);
40 
41     int width = src.cols;
42     int height = src.rows;
43     int channels = src.channels();
44 
45     dst = Mat::zeros(src.size(),src.type());
46     double alpha = 2;
47     double beta = 40;
48     for (int row = 0; row < height; row++)
49     {
50         for (int col = 0; col < width; col++)
51         {
52             //RGB 三通道的
53             if (channels == 3)
54             {
55                 float b = src.at(row, col)[0];
56                 float g = src.at(row, col)[1];
57                 float r = src.at(row, col)[2];
58 
59                 dst.at(row, col)[0] = saturate_cast(alpha * b + beta);
60                 dst.at(row, col)[1] = saturate_cast(alpha * g + beta);
61                 dst.at(row, col)[2] = saturate_cast(alpha * r + beta);
62             }
63             else//Gray 单通道的
64             {
65                 float v = src.at(row, col);
66                 dst.at(row, col) = saturate_cast(alpha * v + beta);
67             }            
68         }
69     }
70 
71     namedWindow("对比度图像",WINDOW_AUTOSIZE);
72     imshow("对比度图像",dst);
73 
74     waitKey(0);
75     return 0;
76 }
```


　　　　图像亮度调整效果的对比：　　　　![](https://img2024.cnblogs.com/blog/1048776/202501/1048776-20250106164315409-1677485312.png)


　　　　调整后的效果：　　　　![](https://img2024.cnblogs.com/blog/1048776/202501/1048776-20250106164343667-245290219.png)


　　　　效果很明显。


**三、总结**　　　　这是 C\+\+ 使用 OpenCV 的第七篇文章，其实也没那么难，操作很简单，函数也不难理解，最重要是活学活用。初见成效，继续努力。皇天不负有心人，不忘初心，继续努力，做自己喜欢做的，开心就好。


