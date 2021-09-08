[TOC]

# 【GAMES101-现代计算机图形学入门-闫令琪】笔记 Lecture 05 Rasterization 1 (Triangles)

![image-20210907114759822](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071148238.png)

# 如何定义视锥

![image-20210907141340132](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071415065.png)

宽高比(aspect) = 宽 / 高。

Vertical Field Of View（vFOV）：垂直可视角度，即在垂直方向（上下两条线）能看到的角度范围。

Horizontal Field Of View（hFOV）：水平可视角度，即在水平方向（左右两条线）能看到的角度范围。

定义一个视锥的话只需要一个宽高比和一个垂直/水平可视角度，其他的东西都能够算出来。

![image-20210907142045701](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071420651.png)

包括摄像机到n面的距离|n|、r、t等。

# MVP变换之后再把图像画出来

前面说到MVP变换之后物体都会落到[-1,1]^3^这个范围内，我们成为标准立方体，现在就需要把它画出来。

![image-20210907142542344](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071425513.png)

# 把标准立方体画出来

## 如何定义一个屏幕

![image-20210907143736027](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071437289.png)

- 像素数组：我们抽象的认为屏幕就是一个二维的数组，里面的每一个元素就是一个像素。
- 分辨率：像素的长度，就是屏幕上有多少个这种像素。例如：1920*1080（1080P）。
- 屏幕是一个典型的光栅成像设备。

### 光栅化

![image-20210907143744260](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071437768.png)

Raster（德语）。Rasterize的意思就是在屏幕上绘图，即光栅化。

### 像素

![image-20210907143759192](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071438926.png)

即Picture element，简写为Pixel。

- 我们认为像素就是一个正方形的小方块，上面有颜色。是最小的表示单位。
- 颜色填充整个小方块。R,G,B范围在（0-1）中，或者是（0-255）中。

### 定义屏幕空间

![image-20210907145156654](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071451640.png)

- 本课程将原点定义在左下角，向上是Y，向右是X（右手坐标系）。也有软件将原点定义在左上角的，向下是Y，向右是X（左手坐标系）。

- 每个像素都写成(x,y)的形式，并且用一个整数的坐标来描述它。
- 像素范围即是（0,0）到（width -1, height - 1）。
- 像素中心就是（x + 0.5， y + 0.5）。
- 屏幕就包括（0,0）到（width，height）这么多个像素。

## 变换立方体到屏幕

### 变换XY

![image-20210907145324787](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071453858.png)

- 先忽略Z。
- 变换物体x、y平面[-1,1]^2^到[0,width] ×[0,height]。

### 写出变换矩阵

![image-20210907145950252](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071459172.png)

![image-20210907145742399](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071457304.png)

# 光栅化

把多边形“打碎”成像素点。

![image-20210907153850300](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071538310.png)

## 在程序控制下的物理画图

![image-20210907150348892](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071503338.png)

## 不同的光栅化设备

### 示波器

![image-20210907150448627](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071504534.png)

### CRT显示器

![image-20210907150643331](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071506436.png)

早期的电视就是用阴极射线管发射电子，然后控制电子偏转控制电子打在屏幕上的位置。我们就用它画一条线（从上到下，从左到右），画一条就隔一行，直到线足够多到覆盖掉屏幕。

隔行扫描：

就是说我在前面一个时间点只画奇数行、然后后一个时间点只画偶数行，这样的话在画每张图的时候工作量都减少了一半。这利用了人眼的视觉暂留效应，也不会发现有瑕疵。

现在的视频压缩也用到了这个思想，就是这一帧我只记录奇数行，下一帧我只记录偶数行，这样视频就又被压缩了一半。

当然这个技术会造成严重的画面撕裂，特别是对于高速运动的物体来说。

### 显存显示器

![image-20210907151438996](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071514829.png)

在显存（显示器的内存）中有若干块区域，每块区域都存着一副图像的信息，然后告诉显示器显示这块区域的图像，即将这块内存的图像信息映射到屏幕上。

### LCD（液晶显示器）

![image-20210907151740795](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071517923.png)

从计算器的屏幕到手机的屏幕乃至到现在某些超高分辨率的屏幕（超过人眼分辨率）。

![image-20210907152908934](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071529203.png)

利用液晶来控制一个像素的显示。液晶通过改变自己的排布影响光的极化，即光的偏振方向。光经过一个光栅，光只能通过光栅所允许的颜色。

图中就是垂直方向的光通过光栅的作用逐渐变成水平方向的光再出去。

### LED（发光二极管）

![image-20210907153034853](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071530722.png)

二极管好理解，要么开要么关。即要么发光，要么不发光，由不同的二极管阵列构成。

### 电子墨水

![image-20210907153211667](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071532441.png)

有黑的和白的两种“墨水”。加之不同的电压在上面，就可以控制是白的在上面还是黑的在上面。就控制了像素能否可见，再采取一定的排列就能成像。

它有一个问题，就是刷新率很低，你要把白的和黑的交换位置就需要一定的时间，这个时间肉眼可见。

# 三角形

## 图形学的基础图形

三角形在图形学中非常应用广泛。

![image-20210907154227349](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071542312.png)

- 最基础（简单）的多边形。
- 其他的多边形都可以拆成三角形。

三角形的独特性质：

- 保证了在一个平面。
- 清晰的内外定义。
- 在三角形顶点上插值值的良好定义的方法(重心插值)，即从一个顶点的属性渐变到另一个顶点的属性。

## 判断三角形和像素中心点的关系

![image-20210907154518019](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071545888.png)

前面说到一个像素只能显示一个颜色，并且要么就显示整个颜色，要么就不显示。那么像图中这种三角形穿过了像素格子但是没占满的，整个像素到底是显示还是不显示呢？这就需要判断一下三角形和像素中心点的关系来决定。

## 最简单的方法：采样

![image-20210907160130543](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071601692.png)

采样：给你一个函数，它是连续的，然后我在不同的地方去问整个值是多少。即将一个函数离散化的过程。

用代码来表示就是给你一个x，用这个函数根据x求出一个值。然后又很多个x都需要连续求值，所以是将函数离散了。

采样是图形学中一个非常重要的概念。

这里的采样是像素中心对屏幕的采样。即需要一个函数，用它来算出在每一个像素中心定义的值是多少。

![image-20210907160203997](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071602942.png)

定义一个函数，判断像素中心是否在三角形内。

![image-20210907161817499](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071618473.png)

给定一个函数，传入一个三角形，传入屏幕空间中的一个像素点的位置（x,y），判断这个像素（中心）点是否在三角形内。函数原型是：bool inside(tri,x,y);

![image-20210907161955334](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071619679.png)

示例代码：考虑屏幕上的所有点。

![image-20210907162220422](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071622325.png)

如何判断？用向量叉积的几何意义，内容和前面章节的TODO一模一样，这里不再赘述。

![image-20210907162426686](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071624975.png)

如果碰到像素中心点刚好和三角形的两边重合，那么条变是在三角形1上还是在三角形2上还是说同时在两个三角形上。这种情况要么不做处理要么特殊处理（自己定义标准） 。

例如OpenGL规定若点在上边或左边则认为该点在三角形内，若在下边或右边则认为该点不在三角形内。

![image-20210907163056530](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071630676.png)

其实我们不必检测屏幕中的每个像素，即如果我们知道左边第一列绝对不会碰到三角形，那么就不必去检测它了。我们用一个盒子将三角形包围起来，这个正方形盒子的范围是三角形的覆盖上下边和左右边。我们称为AABB盒，详情见TODO。

![image-20210907163621962](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071636468.png)

更快的方法是用左和右的检测盒，但是这并不容易。

![image-20210907163748936](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071637008.png)

左：条状排列。

左：bayer patter，可以让像素快均匀的分布到屏幕上。并且绿色更多，因为人眼对绿色更为敏感。绿：蓝：红 = 2:1:1。

![image-20210907164128154](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071641575.png)

彩色印花:观察半色调图案。RGB越高就越亮，表示它越靠近白色。

![image-20210907164308669](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071643933.png)

液晶像素实际上并不能在一个统一颜色的正方形中发光，但这种近似足以满足当前的讨论，所以我们认为液晶像素就是在统一颜色的正方形中发光，影响不大。

![image-20210907164546008](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071645941.png)

现在我们能知道哪些该显示颜色哪些不显示颜色，结果如下图：

![image-20210907164609783](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071646818.png)

但我们想要的是下图：

![image-20210907164647567](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071646932.png)

锯齿是光栅化图形学中一致致力于解决的一个严重问题。它发生的原因是：

- 像素本身具有一定的大小。
- 采样率对信号来说是比较低的，发生了信号走样。

![image-20210907164832302](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109071648421.png)

我们需要抗锯齿（反走样）。

# 参考资料&原文链接

[GAMES101-现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744)

[猎豹网校：游戏开发之3D数学基础](https://www.bilibili.com/video/BV1ib411K7TK)

# 本文标签

`计算机图形学`、`游戏开发`、`GAMES101-现代计算机图形学入门-闫令琪`、`Unreal Engine`、`游戏开发基础`、`视频系列笔记`、`笔记`。

