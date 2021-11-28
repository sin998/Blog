[TOC]

# 【Houdini】项目设置

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 新建项目

在File中选择New Project以新建项目。

![image-20211128102154546](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281021016.png)

1. 项目路径，注意里面有个HONE，它是一个全局变量，3中的文件都会用此项目的相对路径来查找和保存文件。
2. 项目在此机器上的位置。
3. 文件结构。

![image-20211128102526907](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281025058.png)

保存一下场景。

![image-20211128102835937](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281028939.png)

这将会保存在项目的根目录。

# 项目设置

先设置一下单位。

![image-20211128103156932](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281031809.png)

可以看出Houdini的默认单位是1米，即网格线的一格。默认重量是1KG。

![image-20211128103400141](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281034238.png)

# 导入素材

把素材放到项目的reference下。

![image-20211128104622779](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281046882.png)

新建一个Geometry。

![image-20211128103935587](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281039915.png)

在这下面再新建一个COP2Net节点，然后再在COP2Net节点下新建一个File节点，会自动带入一张图片：

![image-20211128104319970](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281043937.png)

修改成我们自己的图片：

![image-20211128104822647](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281048914.png)

回到COP2Net节点看一眼，其实是帮我们创建了一个Mesh，然后把图片贴在上面了。

![image-20211128104944849](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281049248.png)

不过这个参考图太糊了，没什么用。所以我们要把参考图弄清晰一点。

![image-20211128105259901](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281053953.png)

UVQuickShader这个节点是快速的在3D中检查一个物体的贴图是否正常。

![image-20211128105701754](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281057802.png)

然后把它换成我们的图片，这样就很清晰了：

![image-20211128105740072](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281057387.png)

再把另一张参考图弄进来：

![image-20211128105928712](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281059937.png)

复制节点：直接Ctrl+CV或者按住Atl再拖动节点都可以。

# 正确显示素材

创建一个Transform节点，再旋转它：

![image-20211128110322049](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281103387.png)

再回顾一下模板显示，这里就能很容易的看出区别。

![image-20211128110541448](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281105522.png)

旋转10°时。

![image-20211128110606669](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281106656.png)

旋转20°时。

![image-20211128110656906](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281106010.png)

现在把两张图片一起显示出来：

![image-20211128110955963](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281109083.png)

# 用Box参考大小

放大查看参考图片可以发现雷神之锤的长度是62cm。

先搞一个一样高的Box：

![image-20211128111418401](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281114469.png)

再利用BOX的中心把BOX弄到Y的正半轴。0.62的一半就是0.31嘛，那这个坐标指定就在Y上半轴。

![image-20211128111506369](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281115498.png)

但是有个问题，就是只是在这个情况下才能很完美的贴合。我要是稍微改一点点它的长度就立马不行了：

![image-20211128112024804](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281120313.png)

所以可以这样做，先右键复制Size这个参数：

![image-20211128112133198](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281121282.png)

然后在这里右键，选择参考Size：

![image-20211128112230722](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281122852.png)

这时它的Center的Y就和他的长度Y一样了，不管你怎么变都是一样的：

![image-20211128112337739](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281123036.png)

但是我们只希望它是Size的一半。

![image-20211128112409333](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281124634.png)

然后打开模板显示就能参考大小了，方便我们调节。

![image-20211128112653442](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281126314.png)

![image-20211128113713603](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281137699.png)

如果使用比较精确的数值的话就是0.258，缩放是0.95，直接填也可以。

![image-20211128114035053](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281140338.png)

要是模板显示不方便那就直接合并也可以，不过要注意设置。

![image-20211128114337040](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111281143076.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`。
