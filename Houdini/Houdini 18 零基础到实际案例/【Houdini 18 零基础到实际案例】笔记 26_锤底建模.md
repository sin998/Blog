[TOC]

# 【Houdini 18 零基础到实际案例】笔记 26_锤底建模

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 锤底建模

![image-20211206232535870](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062325822.png)

这里有两种定位方法，一是用bbox+ch相对于锤柄定位。二是直接用tube的center参考于的height的ch一半定位。我们就用简单一点的，就用第二种。

搞个圆柱体，调整好参数：

![image-20211206233008492](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062330428.png)

![image-20211206233030963](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062330150.png)

中间那几层挤一挤，只是最后一个参数稍微调整一下：

![image-20211206233715137](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062337281.png)

再到最后多搞一个polyextrude，方便做倒角：

![image-20211206233918873](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062339881.png)











# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
