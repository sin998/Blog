[TOC]

# 【Houdini 18 零基础到实际案例】笔记 15_ 符文模型

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 导入符文模型

![image-20211204104422603](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041044834.png)

可以看到有非常多的点，这个图很圆润：

![image-20211204104542556](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041045804.png)

仔细看的话会发现这里其实应该是一个空心的才对：

![image-20211204104759166](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041048351.png)

用Hole节点扣一下。这个节点的作用就是检查一下里面的面是否是被外面的面包围着的，如果是那就把里面的面扣掉。

![image-20211204104931982](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041049724.png)

# 减少点的数量

前面已经说过了点的数量非常多，然而这在Houdini里面是一个巨大的负担，我们不需要这么大的负担，同时还希望能够维持它原来的样子。

使用resample节点。

![image-20211204105349159](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041053765.png)

可以看到这个节点是比较猛的，直接把八万多个点干到100多个。负担是剔除了，但是原来的形状也变了，我们要调节一下参数把点稍微弄多一点，把Length调节为0.005：

![image-20211204105713842](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041057744.png)

这是第一种调节方式，调节最大段长度。用length来指定每个点的距离是多少就能达到调节点的数目的目的。

第二种方式是指定最大分段数，分段数越多则越细致。

![addddsd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041100400.gif)

我们还是以第一种调节方式来，length大约为0.005，此时点的数量是3000多个，差不多。

# 其他调整

再挤出一点宽度：

![image-20211204110443239](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041104370.png)

接下来我们把颜色清掉，建模的时候不要保留颜色，最后在贴图的时候统一用贴图的颜色。

![image-20211204110534342](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041105308.png)

用attribute delete节点来删除特定的属性。

![image-20211204110847281](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041108215.png)

还是再用polybevel做一个倒角：

![image-20211204111302422](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041113108.png)

这个模型好像不是在原点：

![image-20211204111503677](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041115813.png)

用transform移动过去先：

![image-20211204111609397](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112041116245.png)







# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
