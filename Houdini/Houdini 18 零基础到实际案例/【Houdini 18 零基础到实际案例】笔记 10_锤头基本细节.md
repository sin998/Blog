[TOC]

# 【Houdini 18 零基础到实际案例】笔记 10_锤头基本细节

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 添加一边的细节

![image-20211129225945012](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292259157.png)

先操作出一边，再镜像操作出另一边。

同样用polyextrude，选择四个面，插入0.02.

![](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292233440.png)

再向内挤出一点点：

![image-20211129223526604](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292235679.png)

现在再搞一个polysplit，来切割。

![image-20211129223745087](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292237628.png)

注意：默认是以点来切割：

![image-20211129223904457](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292239650.png)

这不是我们想要的模式，所以改成边以后再切割：

![image-20211129224739508](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292247678.png)

按下Q键重复动作一次：

![image-20211129225045733](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292250613.png)

切割一下下面：

![image-20211129225320209](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292253969.png)

另一边做同样的操作，得到两边这种：

![image-20211129225550987](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292255029.png)

选中这几个，做挤压操作，用polyextrude：

![image-20211129225701163](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292257477.png)

使用的值是-0.0025：

![image-20211129225839931](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292258862.png)

# 镜像到另一边

使用节点clip（大剪刀）：

![image-20211129230203777](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292302104.png)

按下回车看一下它的操作手柄，操作一下瞬间就明白了：

![asdaddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292305573.gif)

从另一个方向切也可以：

![image-20211129230805743](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292308854.png)

所以这是它的介绍：

![image-20211129231008329](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292310285.png)

灵活使用轴向和选择保留的面就可以达到我们想要的效果，切割掉另一半，保留我们编辑的一半。

![image-20211129231201539](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292312727.png)

用mirror节点进行镜像：

![image-20211129231445737](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292314903.png)

由于是镜像，所以担心会不会有显示问题，最简单的办法就是看点的编号有没有重复：

![image-20211129231811525](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292318669.png)

没有重复则说明是OK的。

查看一下法线：

![image-20211129232138513](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292321654.png)

调一下数值，看一下表现：

![asdaddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111292325079.gif)

这个值调到30就差不多了，现在我们初步有了一个锤头了。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
