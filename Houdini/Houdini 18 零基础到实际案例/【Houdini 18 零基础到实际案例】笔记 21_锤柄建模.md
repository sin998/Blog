[TOC]

# 【Houdini 18 零基础到实际案例】笔记 21_锤柄建模

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 锤柄建模

先搞个锤柄，注意参数。

![image-20211205205300959](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052053146.png)

还是一样的先画个曲线，注意Z轴是0，保证线都在某一个平面：

![image-20211205210018077](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052100074.png)

再加几个点：

![image-20211205210316212](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052103361.png)

一样的，来个grid：

![image-20211205212132429](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052121487.png)

注意sweep需要法线和切线，所以也搞个polyframe：

![image-20211205213542152](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052135105.png)

改成up之后发现跟原来的切线一样：

![image-20211205213638657](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052136722.png)

再用一个sweep扫描一下：

![image-20211205214347325](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052143352.png)

![image-20211205213839381](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052138360.png)

同样的，调整一下中心：

![image-20211205214326270](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052143392.png)

裁剪X轴，保留下半部分：

![image-20211205214803981](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052148097.png)

然后直接镜像：

![image-20211205214825089](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052148057.png)

看到这种深蓝色的就说明是法线方向反了。

![image-20211205215042721](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052150625.png)

我们需要用一个reverse来翻转过来：

![image-20211205215129410](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112052151057.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
