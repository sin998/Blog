[TOC]

# 【Houdini 18 零基础到实际案例】笔记 17_叉积

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 观察向上方向

![image-20211205110337717](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051103803.png)

默认猪头是指向Y轴的。我们搞一个sphere来copy一下呢：

![image-20211205145119796](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051451692.png)

我们好像只能控制猪脸这一面朝向法线方向，旋转好像控制不了。

![image-20211205145057555](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051450916.png)

可以看见向上向量都和法线不垂直：

![image-20211205145504219](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051455554.png)

# 使用叉积来计算向上方向

使用公式来计算每个点的向上方向，确保和法线垂直：

![image-20211205145600201](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051456564.png)

效果：

![addddsds](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051458695.gif)

可以看见确实是垂直的：

![image-20211205150222969](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051502199.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
