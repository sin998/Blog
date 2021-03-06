[TOC]

# 【Houdini 地形制作】 03_HeightFiled常用节点（二）

# HeightField Terrace

做梯田用的。

连上以后：

![image-20211212204958076](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122049884.png)

记得要重新计算然后再调参数。

![image-20211212205059297](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122051212.png)

别忘了和Paint作为Mask配合使用。

![image-20211212205228808](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122052518.png)

# HeightField Layer Property

这个节点不常用，用来平滑地形边缘的，还不如Blur好用。

# HeightField Tile Splice

地形分块，通常和Split配合使用。

例如我现在有个地形：

![image-20211212205636480](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122056634.png)

分块以后：

![image-20211212205649999](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122056130.png)

![image-20211212205717729](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122057884.png)

这个地形被分为了row：4，col：4。所以一共被分为了16块。

默认值是第0块，所以就显示的第0块。

那我们显示第一块看看：

![image-20211212205826945](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122058876.png)

# HeightField Tile Split

地形拼接，就是把两个地形拼到一起。

我现在把地形调整为2*2块。

第一个显示第0块，第二个显示第1块，然后用merge把他们拼起来：

![image-20211212210238211](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122102001.png)

![image-20211212210300777](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122103788.png)

但是仔细看会发现的话两块地形是没有合到一起的，还是各自的边界：

![image-20211212210324323](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122103205.png)

为了效果更明显，我再将其中一个平移一点点：

![image-20211212210613756](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122106894.png)

然后再拼起来：

![image-20211212210646281](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122106278.png)

# HeightField Draw Mask

这个节点和Paint差不多，只是画的方式不太一样。

![image-20211212210909642](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122109447.png)

# HeightField Copy Layer

复制/添加层。

比如我的noise现在只有两个层。

![image-20211212211201524](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122112377.png)

然后把这个节点放上去，给层起一个名字，再次查看信息就会发现多了一个层：

![image-20211212211312871](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122113159.png)

# HeightField Mask Clear

直接将Mask这层的值置为0，并不是删除图层。

![image-20211212211813234](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122118954.png)

# Blast

删除层。

![image-20211212211929531](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122119605.png)

# HeightField Layer Clear

和Mask Clear差不多，只不过前者是Clear的Mask，后者是需要自己指定一个层来Clear。

![image-20211212212135469](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122121188.png)

# HeightField Isolate Layer

可视化层，Height和Mask都可可视化。

![image-20211213213117924](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132131264.png)

# HeightField Mask Noise

和HeightFiledNoise很像，只不过它的初始值设置的不一样而已。

这是HeightFiledNoise：

![image-20211213213315986](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132133982.png)

这是HeightFiledNoise：

![image-20211213213226925](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132132691.png)

# HeightField Mask by Feature

做Mask非常常用，可以根据条件筛选出区域。

例如我要筛选出高度在20-80之间得到区域：

![image-20211213213859886](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132139761.png)

还可以筛选其他的东西：

![image-20211213214153945](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132141783.png)

如果单个勾的话就是单独筛选。

如果勾选多个的话就是将Mask相乘，就是符合条件的。

# HeightField Mask Blur

这个节点就是模糊遮罩。

# HeightField Mask by Object

和HeightField Cutout by Object很像，只不过作用的对象不一样。

例如HeightField Mask by Object：

![image-20211213214720095](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132147100.png)

HeightField Cutout by Object：

![image-20211212201309915](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122013570.png)

# HeightField Mask by Occlusion

和HeightField Mask by Feature中的Occlusion选项功能一致，是向内收缩的。

# HeightField Mask Expand

显示HeightField Mask by Feature中筛选的内容，是向外扩张的。

![image-20211213215701057](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132157107.png)

以下节点算是比较高级的操作了。

# HeightField Scatter

撒点。一般来说撒树、石头、房屋建筑等。一般不撒草，草太多了，是直接做到UE的材质里面的。

输入1：基本地形。输入2：要撒点的Mask。输入3：要种的东西。

例如：

![image-20211213220615299](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132206275.png)

你会发现这个是歪的，这样设置一下就对了：

![image-20211213220701074](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132207913.png)

可以调覆盖范围、点之间的距离啊等等。

# *HeightField VOP

对体积的操作。

# *HeightField Wrangle

和其他的Wrangle一样，是用代码对HeightField进行操作。

# HeightField Flow Field

HeightField Flow Field、HeightField Slump、*HeightField Erode是做地形解算的节点，很费。

![image-20211213221351826](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132213723.png)

这个节点会把水力侵蚀的地形画出来。

有两个参数需要关注，第一个是水的数量，另一个是水的密度。

水的数量：控制的是水波纹的深浅，直接加深红色。

![image-20211213221703169](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132217075.png)

水的密度：控制的是这些小点：

![image-20211213221614227](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132216296.png)

# HeightField Slump

沉积地形。例如这样，就像雨水真的从山上流下来一样：

![ss](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132220872.gif)

# *HeightField Erode

这个节点很强，但是很难把控。根据时间推算出来每个东西。

![ssdd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132226829.gif)

热力侵蚀、水力侵蚀的相关参数都可以控制。

