[TOC]

# 【GAMES101-现代计算机图形学入门-闫令琪】笔记 Lecture 08 Shading 2 (Shading, Pipeline and Texture Mapping)

# 参考资料&原文链接

[GAMES101-现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744)

# 上节课

![image-20210914104809913](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141048458.png)

# 本节课目标

![image-20210914104824724](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141048680.png)

# 复习：漫反射

![image-20210914105114382](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141051529.png)

# 高光

高光的产生是因为材质像镜子一样反射，反射出来的方向只有一个方向。当这个反射出来的方向跟我观察的方向接近的时候就能观察到高光。即R和v的方向接近：

![image-20210914105438900](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141054721.png)

## 高光的表示

当R和v的方向接近的时候，说明了法线方向和半程向量接近。那么就能算出v和I的半程向量h（即v和l形成的角平分线）：

![image-20210914110033975](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141100112.png)

解释：

- 加上高光的话只需考虑半程向量方向是不是和法线相接近即可。
- 这里已经考虑了高光就不考虑能量被吸收，因为它是简化模型。
- 前面那个用n · l的叫做Phong模型。这个用 n · h的叫做Blinn-Phong模型，Blinn-Phong模型是对Phong模型的改进。因为用v和l计算h好算，但是用l和h计算v可不好算。
- 那个指数p是高光指数，用来控制高光的范围：

## 高光范围的控制

![image-20210914112222916](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141122838.png)

从图中可以看出，当夹角为45°的时候，这时候用这个夹角去生成高光的话就会看到一个超级大的高光，即很大一块区域都是高光，这个就不太合理。

因为我们在现实生活中的时候一般是视线稍微偏离高光一点点的时候（大概3°~5°）高光就消失了，这个才是高光，很亮并且集中在很小的区域。

我们给这个cos加上一个指数，就能达到想要的效果。从图中也能看出来指数的越高，说明移动的距离越小高光就消失了，符合现实生活。

在Binn-Phong模型里面这个指数一般是用的100-200。

从下图中也能看出来：

![image-20210914112809634](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141128923.png)

# 环境光

我们认为任何一个点接收到环境的光永远都是相同的，即强度为I~a~。而K~a~是环境光的系数。那么就能得到近似的环境光L~a~ = K~a~I~a~：

![image-20210914112930065](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141129507.png)

解释：

- 环境光不讲究从哪里进来，跟直接的实际光照方向没有关系。
- 环境光是环境，不管从哪里看结果都是一样的，并且和法线也没有关系，基本上环境光就是一个常数，这意味环境光就是着一种颜色。
- 环境光的作用就是保证场景不黑，物体自身的亮度加上环境光的亮度能让玩家看到就行。

# Blinn-Phong反射模型

![image-20210914114258755](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141143189.png)

# 着色频率

即把着色应用到哪些点上。下图是同一个模型，分别把着色应用到平面（平面用法线）、平面顶点（平面法线的均值，即插值）、像素着色（先求出每个平面的顶点法线，最后将法线与每个像素进行插值。即能对每一个像素进行不同的着色，效果非常好）。

![image-20210914114538759](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141145695.png)

这三种着色方式分别为：Flat着色（逐多边形）、Gourand着色（逐顶点）、Phong着色（逐像素）。

## Flat着色（逐多边形）

![image-20210914115102415](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141151809.png)

## Gourand着色（逐顶点）

![image-20210914115148207](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141151903.png)

## Phong着色（逐像素）

![image-20210914115403268](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141154809.png)

这部分可以参考[【3D数学基础：图形与游戏开发】笔记 第15章 图形数学](https://www.cnblogs.com/sin998/p/15269782.html#%E7%9D%80%E8%89%B2)有很详细的解释。

注意区分Phong是一种着色模型，而Blinn-Phong是一种反射模型。

![image-20210914115511584](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141155511.png)

## 着色模型的选择

着色模型的选择取决于顶点和面的多少：

![image-20210914115718555](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141157054.png)

解释：

- 左边第一幅从上到下，面越多，我用最简单最快的Flat效果也不会太差。
- 当模型足够复杂的时候，那么逐多边形和逐顶点着色得到的效果不一定比逐像素差。
- 反之，效果差不多的时候也不代表逐像素会比逐多边形和逐顶点着色的工作量要大。例如三角形面的面数已经超过像素的时候。

## 顶点法向量的计算

即相邻的多边形的法向量求一个平均。

![image-20210914120721805](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141207170.png)

这里的话用给这个平均加一个权值，例如用三角形的面积加一个权值来最终求出顶点的法向量会更好。

## 逐像素的法线的计算

![image-20210914121019765](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141210767.png)

在一个多边形内部：知道左边顶点的法线的知道右边的法线，中间变化的法线可以用插值求出来，但是别忘了归一化。

# 渲染管线

## 概览

现在更愿意叫做实时渲染管线。

可编程的渲染管线则指的是可以自定义某些功能和实现顺序。

![image-20210914121936787](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141219083.png)

顶点处理->三角形处理->光栅化->着色->输出。

## 各个部分的例子

### 顶点处理

MVP变换。

![image-20210914122240202](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141222219.png)

### 光栅化

![image-20210914142037830](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141420852.png)

### 深度缓存

![image-20210914142059688](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141421761.png)

### 着色

顶点带着颜色信息，所以在顶点处理的时候就可以着色。

也可以光栅化之后再进行像素着色。

现代GPU可以允许大家自定义某些行为，比如顶点或者是像素的着色。

![image-20210914142134129](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141421193.png)

### 纹理映射

![image-20210914142444146](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141424922.png)

## Shader编程

- shader是每一个顶点或者像素都会执行一次，它是通用的，不用写循环指定每一个像素都执行一遍。所以只需要关注一个顶点或者像素就行了。
- 如果是顶点操作，那么这个Shader就是vertex shader(顶点着色器)，如果是像素操作，那么这个Shader就是Fragment或者picks a shader(像素着色器)。例如下面就是一个像素着色器的例子，告诉GPU怎么着色，输出的颜色是多少，并且把它输出过去（这里是OpenGL的着色语言，简称为GLSL）

![image-20210914145243002](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141452852.png)

里面有几个全局变量：

一个纹理myTextture

一个光照方向lightDir

一个uv

一个法线方向norm

这个函数的意思是： 

- 先拿到k~d~值。
- 使用Phong模型执行漫反射计算。
- 将gl_fragColor的值设置好，这就是最后的值。

![image-20210914150026250](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109141500251.png)

### 推荐网站：Shadertoy

![image-20210914205453647](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914205453647.png)

可以在网页上执行的Shader。

![image-20210914205549963](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914205549963.png)

### 现代高质量3D场景实时渲染

现代的GPU可以高速并行着色、处理多边形。

![image-20210914205627110](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914205627110.png)

随着GPU的发展，现在出来了几何着色器，可以定义几何的操作；通用GPU（GPGPU）等。

GPU分为两种：一个是独立的GPU，另一个集成显卡。

![image-20210914205800401](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914205800401.png)

GPU的并行度很高，高出于CPU，可以执行很快的并行计算。

![image-20210914210017279](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914210017279.png)

# 纹理映射

如果只考虑两个点光源和环境光，那么这个L_d是可以写出来的：

![image-20210914210138977](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914210138977.png)

我们希望在物体的不同位置（即任何一个点）定义不同的属性。

## 简介

任何一个物体的三维其实都是二维的，即将三维的表面撕下来铺展开：

![image-20210914210401319](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914210401319.png)

纹理就是一张图。可以将图铺在物体的表面（即映射，从图上的纹理映射到物体的表面）。

注意图中的三角形：

![image-20210914210624242](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914210624242.png)

## UV展开

定义一个UV坐标，可以把纹理映射到物体表面。

![image-20210914211052453](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914211052453.png)

U和V的范围都是在0~1之内，约定俗成，方便处理。

物体表面每一个顶点都对应一个UV。

![image-20210914211243387](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914211243387.png)

## 无缝贴图

![image-20210914211331925](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914211331925.png)

它的纹理如下：

![image-20210914211348649](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914211348649.png)

不断重复的纹理可以贴满物体。可以观察到图中的这个纹理设计的很不错：

![image-20210914211513836](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914211513836.png)

在重复的时候没有空隙，即左边的纹理能自动接上右边。无缝贴图有几种算法，其中一种叫wang tired。

## 三角形内的像素插值

用三角形的重心坐标做三角形内的插值。

![image-20210914211730790](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/image-20210914211730790.png)

# 本文标签

`计算机图形学`、`游戏开发`、`GAMES101-现代计算机图形学入门-闫令琪`、`Unreal Engine`、`游戏开发基础`、`视频系列笔记`、`笔记`。

