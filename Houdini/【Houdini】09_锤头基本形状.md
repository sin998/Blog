[TOC]

# 【Houdini】锤头基本形状

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 锤头基本形状

在建模之前这样设置，以获得强烈的对比便于观察。

![image-20211128114541746](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281145279.png)

接下来建一个锤子几何体，然后同时也把参考的图片打开。

![image-20211128121233866](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281212118.png)

调整BOX，让锤头的位置和大小基本和蓝图匹配（直接输入蓝图上标的数值也可以）。

![image-20211128122347504](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281223527.png)

我是直接输入的值：Size（0.23,0.17,0.17）。位置是自己调的。

# 锤头倒角

倒角是一种很常见的形状。

![image-20211128122846048](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281228095.png)

在视口按下S进入选择模式，进入选择模式后默认是选择面，这里我们需要选择边，所以切换到选择边模式。

![image-20211128145036915](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281450545.png)

进入选择模式后按下快捷键123就是对应的选择模式。

我们一共要选中4条边来做倒角，你可以一条一条的选择，也可以用快捷键来选择。这里我们用Shift+A+MMB来选择。

![image-20211128145427676](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281454682.png)

选择几何图形，然后选择要执行的操作。

按住A或Ctrl+A或Shift+A或Ctrl+Shift+A选择完整(MMB)或部分(LMB)循环。

单击“双lmb”选择边缘环，单击“双mmb”选择边缘环。

按住H或CtrltH或Shift+H或Ctrl+Shift+H来选择填充。

按下C会出现快捷选项，这个选项是跟你当前选择的模式相关的，即不同的模式下按下C会出现不同的面板。

![image-20211128145924033](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281459184.png)

我们这里还是以Main模式。在Main模式下点击C键选择Model。

![image-20211128150940894](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281509890.png)

然后选择Model后，再在Model里面选择Polygons。

![image-20211128151101928](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281511979.png)

最后再在Polygons的三级菜单中选择PolyBevel。

![image-20211128151217948](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281512963.png)

然后就会发现节点网络中多了一个PolyBevel节点：

![image-20211128151307614](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281513818.png)

个人觉得还是直接在节点网络里面输入PolyBevel来得快一点- -。

有了这个节点以后，我们只需要调整Distance即可，这里的值精确一点是用的0.024。

你也可以自己调整一下玩一下：

![asdaddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281523548.gif)

这次弄一下旁边的突起的东西，这次使用polyextrude节点。

先弄出一个轮廓吧，insert调到0.05差不多。

![image-20211128163133538](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281631422.png)

然后这时候将鼠标移动到视口，按下Q键，Houdini就会重复上一个步骤，只是不修改参数，这时候我们可以发现多了一个相同的节点，我们这次调节Distance挤出来，差不多大小就行：

![image-20211128164427319](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281644628.png)

这一步Distance用的是0.026，Insert是0.011。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`。
