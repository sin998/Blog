[TOC]

# 【Houdini 18 零基础到实际案例】笔记 25_Lattice

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 使用Lattice来控制几何体大小

![image-20211206223135086](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062231535.png)

![image-20211206223225322](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062232470.png)

选择好之后：

![image-20211206223257811](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062232818.png)

按下两次回车，会发现节点网络里面多了这几个节点：

![image-20211206223414398](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062234382.png)

最主要的就是最后一个节点，它的名字叫网格变形，也有人叫晶格变形。大意就是用这个正方体网格来对这个几何体进行变形：

![image-20211206223521915](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062235762.png)

输入一：要变形的几何体。

![image-20211206223639962](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062236449.png)

输入二：要变形之前的几何体的边界框。

![image-20211206223744609](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062237468.png)

输入三：使用哪个Transform对Bound进行变形。即先取得Bound，再取得Bound的Transform，最后再调整Transform输入Lattice达到变形几何体的目的。

![image-20211206223845673](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062238879.png)

其实我们这里也只需要一段细分：

![image-20211206224029439](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062240799.png)

我们做一个简单的线性变换，即上端细，下端粗。

![addddsdsddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062245693.gif)

下端和上端的操作是一样的，选下面的点，然后做同样的操作就可以。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
