[TOC]

# 【Houdini】Houdini和其他三维软件的区别

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 区别一：Context

可以理解为不同的环境、层级。

不同的Context处理不同的数据，有点像编程中的数据类型，什么int、float、double、char啊之类的。

Context是以「网络」的形式来组织的，每一个Context都是一个节点网络。

它们的关系就像Windows的文件系统一样。

Windows是以文件夹来组织文件的：

![image-20211127181403160](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111271814537.png)

文件夹下面有不同的子文件夹，子文件夹下面又有不同的文件。

![无标题](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111271816696.png)

## 常见的Context介绍

在它的公众号里面有：[Houdini新手上路路障清理 - Contexts](https://mp.weixin.qq.com/s/DViNaHoMsujEJziaqsBh-w)。

`Objects`

这是最高层级。

`Geometry`

它包含所有的SOPs（Surface OPerators），定义的是你模型的样子，用来构建和修改几何体的，包含从多边形到体积。

`Particles`

它包含所有的的POPs（Particle OPerators），定义的是粒子模拟。

`Dynamics`

它包含所有的DOPs（Dynamic OPerators），用来创建模拟类效果的解算器节点，这些节点用来计算对象在各种力的影响下的运动。模拟的时候从SOPs中读取几何体，然后把数据传递到DOP解算器。

`Shaders`

它包含所有的SHOPs（Shader OPerators），定义的是物体表面的样子（材质纹理等）。有些是属于vex硬编码的，有些是文件夹，你可以潜入进去修改里面的VOPs。

`VEX Builder`

它包含所有的VOPs（VEX OPerators），节点式编程工具，可以在VOP层级创建你自己的操作符。

`CVEX `

它已经替换了Houidni中大部分VEX相关的contexts。它是Houdini中的一种广义的语言，使用跟Houdini中其他任意地方相同的环境和函数。

`Motion And Audio Channel`

它包含所有的CHOPs（Channel OPerators），用来创建和修改任意类型的raw channel（原始通道）数据，从动画到声音以及二者之间的任何东西。大多数用户选择无视CHOP context，初学你也可以这样。但是在学习Houdini的过程中，一定要把它列入侯学名单中，因为它真的很重要。

`Compositing`

它包含所有的COPs（Compositing OPerators），主要用来合成渲染出来的图像通道。

`Render`

它包含所有的ROPs（Render OPerators），它控制的是合成和渲染的图像的输出。通常用于生成几何体序列，模拟数据和触发渲染任务生成图像序列到硬盘。

# 区别二：点和顶点

Houdini中的点point（存储单位）：

![image-20211127194533806](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111271945063.png)

Houdini中的顶点vertex（运算单位）：

![image-20211127194554379](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111271945870.png)

具体的介绍可以看：https://www.sohu.com/a/154160159_729554。

# 区别三：Houdini开放了所有的节点

只要是可以点进去的节点都能进去查看，可以把节点或者代码粘贴出来做出自己想要的效果。

![image-20211127195205025](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111271952895.png)

![image-20211127195243901](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111271952727.png)

#  本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`。

