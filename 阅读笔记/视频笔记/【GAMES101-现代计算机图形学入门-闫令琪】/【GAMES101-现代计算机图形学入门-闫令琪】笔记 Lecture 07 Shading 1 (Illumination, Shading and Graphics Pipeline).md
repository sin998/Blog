[TOC]

# 【GAMES101-现代计算机图形学入门-闫令琪】笔记 Lecture 07 Shading 1 (Illumination, Shading and Graphics Pipeline).md

# 参考资料&原文链接

[GAMES101-现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744)

# 今日目标

![image-20210913165344108](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131653191.png)

# 画家算法

![image-20210913170846926](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131708910.png)

（油画家）先把远处的东西画好，然后依次画近处的东西，这时近处的东西会覆盖远处的物体，得到正确的图像。例如：

![image-20210913165554242](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131655175.png)

![image-20210913165609327](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131656312.png)

![image-20210913165621287](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131656260.png)

而在计算机图形学中也可以用类似的算法，例如，我要画一个正方体：

先画最远处的：

![image-20210913170050102](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131700151.png)

再考虑画周围四个面，左下右上：

![image-20210913170146507](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131701803.png)

![image-20210913170214027](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131702059.png)

最后再补上正面就能得到正确的效果：

![image-20210913165924882](C:/Users/71080/Desktop/202109131659355.png)

但是如果是在画了最远的面之后，画周围的面不是这个顺序（左下右上），因为我觉得周围这个面左右和上下都差不多，我要是画（右上下左）这个面的话最后结果应该是这样的：

![dsdadadapng](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109132058375.png)

这个就是深度的定义，就是离我们观测点的距离，这个定义不容易。

用画家算法在一定程度上还是能起作用，那么我们按照远近把三角形一个一个的光栅化到屏幕上就可以了，有n个三角形就需要nlog~n~的时间来画：

![image-20210913171035378](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131710362.png)

但是有种很复杂的情况：

![image-20210913171107034](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131711240.png)

这种情况下哪个三角形在前面哪个在后面呢？仔细观察会发现它们是相互覆盖的，这时候它们形成了一个环，没有办法定义深度关系，不能先排序再用画家算法了。

# Z-Buffer

改变策略，不对每个三角形进行排序，对每个像素进行深度排序。

为每个样本(像素)存储当前最小z值需要一个额外的深度值。即摄像机（原点）到像素的距离（z轴）。

缓冲帧缓冲存储颜色值-深度缓冲区(z-buffer)存储深度重要：

为了简单起见，我们假设Z总是正的(小z ->更近，大z ->更远)

![image-20210913171442374](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131714220.png)

我们最终会生成图像和另一个图像，这个的另一个图像只存像素的几何物体的最浅的深度信息缓存。如图：

![image-20210913172342311](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131723217.png)

右边的解释：

- 离我们越近，深度越小，颜色越深。
- 离我们越远，深度越大，颜色越浅。

工作流程：

先画个地板，把地板所在的三角形的像素的深度记录下来。然后往上面放物体，物体上面的三角形会覆盖刚才那个三角形，像素也会覆盖。这个时候一比较，发现新的像素的深度（放上去的物体）比原来那个像素记录的深度（地板）要浅，这意味着新的物体会遮挡地板，所以将新值更新到右边那副图，再更新到左边那副图。用代码表示为：

![image-20210913173627986](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131736135.png)

过程示意图：

![image-20210913173921890](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131739062.png)

要是先画紫色的三角形，再画红色的三角形，结果是一样的，它和先后顺序无关。

深度缓冲算法：

![image-20210913174458516](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131745250.png)

一般来说深度用浮点型来表示，而判定浮点型完全一样的情况是比较少的，当然也不完全排除这种情况。当深度真的完全一样的时候（有些时候也会出现这种情况），就会造成闪烁，一般在建模的时候就会对齐模型，让模型的深度不一样。透明物体处理不了。这里不做介绍。

# 当前我们学了什么

![image-20210913175210473](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131752838.png)

# 着色

## 定义

![image-20210913175457304](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131754719.png)

着色：通过绘画上引入颜色和明暗的不同。

图形学上的着色：把材质应用到物体上的过程。

## 一个简单的着色模型(Blinn-Phong模型)

有三个不同的部分：

高光、漫反射、环境光。

![image-20210913180010312](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131800186.png)

## 着色就是自己

![image-20210913180349508](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131803328.png)

shading point：着色点。

v、n、l都是单位向量。

![image-20210913180601379](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131806251.png)

例如图中这个点，可以猜测光源在左上角，按理来说鼠标指着的这个点应该有被前面物体遮挡而产生的阴影才对。但是我们只考虑这个点，不考虑光线是否被被挡住而产生的阴影。即着色！=阴影。

## 漫反射

![image-20210913180925767](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131809480.png)

有一个光源打到物体上，然后被反射到各个方向。

![image-20210913181121876](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109131811792.png)

给定一个shading point，它能接受到的光的大小是与它自己和光的夹角的大小决定的。

























# 本文标签

`计算机图形学`、`游戏开发`、`GAMES101-现代计算机图形学入门-闫令琪`、`Unreal Engine`、`游戏开发基础`、`视频系列笔记`、`笔记`。

