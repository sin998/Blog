[TOC]

# 【Houdini 18 零基础到实际案例】笔记 14_添加硬边倒角

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 硬边倒角

用polybevel来做倒角，前面已经用过一次了，这是它的效果：

![adddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041024088.gif)

但是这不是我们想要的效果，我们不希望平的边做个倒角，只希望侧边做倒角。

所以把忽略平边勾上，给定一个角度即可，大于这个角度就不做倒角，反之则做倒角：

![adddds](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041029142.gif)

我们使用的角度是40，Distance是0.0005，Divisions是3，然后把shape改为solid：

![image-20211204103244438](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041032486.png)

添加并查看一下新边的法线：

![image-20211204103340772](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041033731.png)

# 标识现在的工作

使用null节点来标识现在的进度，也方便查找。

![image-20211204103720168](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041037013.png)

给他名个名字，取名为Hammer_head_complete。大写的话在后面查找节点是会优先列出。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
