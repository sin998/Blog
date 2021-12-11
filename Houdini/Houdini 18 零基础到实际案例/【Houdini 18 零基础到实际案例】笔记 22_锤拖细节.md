[TOC]

# 【Houdini 18 零基础到实际案例】笔记 22_锤拖细节

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 合并

先Boolean一下，发现结果还是挺有意思的：

![image-20211206202313000](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062023318.png)

把Boolean模式改一下：

![image-20211206202408145](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062024427.png)

我们只对这个形状感兴趣，然后再挤出一点点即可。

然后再：

![image-20211206202801432](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062028633.png)

![image-20211206202917638](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062029504.png)

任务完成，但是没有这么简单。打开点显示，会发现有很多冗余的点：

![image-20211206202956150](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062029273.png)

先把距离近的点融合了：

![image-20211206203430007](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062034288.png)

再把距离远的点删掉：

![image-20211206203504999](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062035738.png)

这下冗余的点都没了，给他一个法线，然后选择所有的面挤出一点点：

![image-20211206203957730](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062039044.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
