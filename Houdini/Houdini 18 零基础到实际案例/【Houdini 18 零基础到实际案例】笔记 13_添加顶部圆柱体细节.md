[TOC]

# 【Houdini 18 零基础到实际案例】笔记 13_添加顶部圆柱体细节

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 头部圆柱体

通过示例图可以看到锤子头上还有一个圆柱体的小细节：

![image-20211202223212150](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022232178.png)

# 添加圆柱体

搞一个Tube节点：

![image-20211203223742747](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032237087.png)

其实在Houdini中是没有圆柱体这个概念的。

接下来我们调整一下它的大小，然后把模式切换为多边形，封一下口，会发现多了几个细分面：

![image-20211203224046834](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032240863.png)

接下来就是老规矩了，玩一玩先：

![ad](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032242804.gif)

接下来自然就是将它移动上去，很容易联想到的是transform节点，但是要移动到那个坐标呢？

可以先看一下boolean的信息，找一下有没有我们需要的：

![image-20211203224731800](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032247964.png)

我们需要的就是Vertices的0.457277这个信息，即边界盒Y轴上最大Y值（物体的边界盒可以用bound节点查看）。

![image-20211203225941089](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032259061.png)

我就以上次的锤顶的clip分割线作为标准了：

![image-20211203230245031](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032302252.png)

但是要注意到的是有一半在下面，你可以调整Tube的center和height来实现，可以参考前面几节课。

调整好大小。我用的Radius是0.0495。Height是0.018。

一样的，给顶部整一个细节，用两个polyextrude节点，第一个向内挤（调节insert），另一个向下挤出形状（调节Distance）：

![image-20211203232008970](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032320319.png)

最后用一个Boolean给它们合起来：

![image-20211203233004307](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032330436.png)

从内部来看，中间没有多余的，把它们合起来了：

![image-20211203233126827](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112032331012.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`。
