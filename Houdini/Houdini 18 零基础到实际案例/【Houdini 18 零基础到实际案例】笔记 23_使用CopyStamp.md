[TOC]

# 【Houdini 18 零基础到实际案例】笔记 23_使用CopyStamp

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# resample

回到上一个节点，打开这个选项，会多出一个东西：

![image-20211206204422866](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062044353.png)

点一下看一眼会发现有一个颜色的渐变：

![image-20211206204709242](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062047321.png)

我们只需要在特定的区间中控制厚度就可以了：

![image-20211206204842708](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062048488.png)

 ![image-20211206205313513](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062053472.png)

![image-20211206205331252](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062053081.png)

# copy stamp

我们如何让每个sample的点相对于Grid不一样呢？使用copy stamp节点（但是官方更推荐用for each。并且copy stamp和copy points十分类似，后者是前者的最新版，但是前者功能稍微要丰富些）：

![image-20211206210349106](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062103011.png)

看起来效果还是不错的，但是好像没有达到我们想要的厚度控制的目的。

我们可以通过抓取点的某些属性修改它们然后以影响拷贝的结果。

在copy stamp节点：

![image-20211206211845520](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062118819.png)

在Grid中我如何拿到这条数据流中的my_thick这个属性呢？

![image-20211206211958436](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062119548.png)

在Grid中输入表达式：

![image-20211206212050176](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062120462.png)

stamp：需要三个参数。第一个是指定的节点。第二个是拿指定节点中的哪个属性。第三个是如果没有找到这个属性的话默认返回多少。

好像OK了，有点像Sweep，只是差一个皮，我们加个皮试试：

![image-20211206212346261](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062123358.png)

# sweep = copystamp + skin？

卧槽，实在是太像了，sweep = copystamp + skin？

现在是比较规整的线性厚度渐变，如果可以自由控制厚薄的话就更好。

![image-20211206212826098](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062128898.png)

我们可以用chramp来做。在attribute wrang的时候，修改一下代码，然后创建一个chramp：

![image-20211206213638598](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062136522.png)

就可以很灵活的调节参数了：

![addddsdsddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062137661.gif)

其实我们也只需要稍微调节一下第一个点就行：

![image-20211206213838285](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062138636.png)

最后用polyfill封个口就行：

![image-20211206214046423](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112062140636.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
