[TOC]

# 【Houdini 18 零基础到实际案例】笔记 12_使用Clip添加细分

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 确认细分数量

如果是一步一步做的话，那么细分数量如下：

![image-20211202215438969](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022154964.png)

我们还需要一些细分。

# 添加细分数量

弄一个clip节点，然后先这样：

![image-20211202215804578](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022158606.png)

切换一下模式，保留所有就能的到细分：

![image-20211202215900744](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022159374.png)

同样的，在横向上也搞一个：

![image-20211202220029724](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022200700.png)

水平搞一个的话要先把clip搞上来，不然是切割不到的：

![image-20211202220418149](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022204485.png)

调节center就能搞上来：

![image-20211202220528466](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022205277.png)

不过这只是手动调的，并不精确，所以我们要用一个表达式来搞定：centroid（质心）。

直接看图：

![image-20211202222727428](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022227368.png)

最终表达式：

![image-20211202222840554](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022228352.png)

那这个引脚的box是谁呢？没错就是我们的锤头- -。

![image-20211202223411209](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022234031.png)

然后效果是：

![image-20211202222914612](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022229241.png)

这个数值是绝对精确的。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
