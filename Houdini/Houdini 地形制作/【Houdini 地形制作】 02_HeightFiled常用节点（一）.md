[TOC]



# 【Houdini 地形制作】 02_HeightFiled常用节点（一）

我们需要重点关注的属性：

![image-20211212173041712](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121730918.png)

# HeightField

是一个基本面，类似于Grid。有一些基本参数可以调节：

![image-20211212174330431](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121743340.png)

包括地形的朝向哪个面、地形的高度、精度、大小、中心点等等。

![image-20211212173213154](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121732415.png)

Houdini里面是一米为一个单位，这里相当于就是两米为一个单位，所以这个地形是由500个这么大的单位组成的，分辨率就是500*500。

![image-20211212173854431](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121738047.png)

# HeightField Noise

是一个罩波，作用于Mask或者Height，预制了一些地形的大概模样。

![image-20211212173618878](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121736744.png)

![image-20211212173627776](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121736101.png)

同样可以对这些地形进行大概的参数调整。

![image-20211212174410096](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121744038.png)

# HeightField Resample

这个就是对地形进行重采样，作用于Height。值越高精度越高，注意值不能调太高，小心内存爆炸。

![image-20211212174710362](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121747557.png)

# HeightField File

导入一张图，一般来说是Mask或者Height。可以一张图既可以做Mask也可以做Height。

一般来说只用File和Scale这两个参数。第一个是要导入的图片，第二个是缩放，看不清的时候可以放大看一下。

# HeightField Paint

顾名思义，就是画，作用于Mask或者Height。有点像PS里面的画笔工具，可以调节半径、透明度、硬度等等。 

![image-20211212175710296](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121757193.png)

# HeightField Visualize

可视化地形的高度并且根据地形高度的不同着不同的颜色，作用于Height。

![image-20211212180055533](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121800397.png)

![image-20211212180119327](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121801055.png)

# HeightField Transform

这个简单，就是对地形的变换进行调整，例如缩放、偏移、旋转等，作用于Height。

![image-20211212180320621](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121803337.png)

注意：Houdini的up轴是Y轴，UE的up是Z轴。

# HeightField Remap

这个节点作用很大，作用于Mask或者Height，例如我现在有一个这种地形：

![image-20211212181953122](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121819026.png)

先重新计算，得到默认的设置：

![image-20211212182145753](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121821616.png)

然后我希望高的地方平一点或者低的地方平一点，那么我就可以直接调整那个函数图像的点就行了：

![image-20211212182255862](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121822612.png)

![image-20211212182314543](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121823582.png)

还有一个就是输入输出的映射，例如输入改成0~1，输出不变，那么中间的值就会被直接拉伸，可以自己上手感受一下。

![image-20211212182545788](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121825688.png)

# HeightField Project

把模型映射到地形上，作用于Height或Mask。

例如：

![image-20211212183014527](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121830828.png)

稍微调整一下Sphere的属性：

![image-20211212183042864](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121830087.png)

现在默认是采样到地形以上（Maxnum）的部分，效果如下：

![image-20211212183217524](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121832205.png)

如果将采样改为Minnum，并调整Sphere的高度：

![image-20211212183305023](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121833205.png)

如果是Replace的话就地上和地下都采样。

可以用这个做地形，例如我想要一座山，那么我直接搞一个BOX上去，然后调整参数就完了。

# HeightField Output

把地形输出成文件，一般来说输出成hda。

也可以输出成一张图片，mask和height都可以地方在想要的层上：

![image-20211212183654549](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121836532.png)

# Convert HeightField

转换节点，可以把HeightField转换成需要的东西。

例如我转换成模型。这是之前的：

![image-20211212194641183](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121946202.png)

![image-20211212194655203](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121946218.png)

我将它转换成多边形：

![image-20211212194716636](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121947828.png)

![image-20211212194609340](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121946452.png)

会发现这个内存涨了几十倍，但是其实他们的精度都是差不多的，不推荐转换。

# HeightField Layer

这个节点接收三个参数，第一个是基础地形，第二个是地形，第三个是Mask。

使用不同的模式来产生不同的叠加效果。

例如我第一个地形是这样的：

![image-20211212195203726](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121952643.png)

第二个地形是这样的：

![image-20211212195214471](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121952361.png)

然后添加上节点以后：

![image-20211212195252939](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121952969.png)

看起来没有变化，这是因为模式是选的Replace，即用第二个地形替换第一个地形。

如果我们换成Add，那么立马会看到效果，像是把第一个地形的特点加到第二个地形的表面一样：

![image-20211212195414531](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121954439.png)

此外还有相减、相乘、最大值、最小值等。

这个节点很像PS里面的不断叠加的层级，例如人的照片带有一个背景，我要替换成一个新的背景，思路是类似的。

我这里用paint节点搞一个Mask：

![image-20211212200230261](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122002010.png)

然后连上以后：

![image-20211212200303249](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122003322.png)

# HeightField Blur

这个节点也简单，顾名思义就是模糊（平滑）地形。

![image-20211212200457385](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122004616.png)

加上节点以后，然后稍微调整一下平滑值：

![image-20211212200520403](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122005659.png)

# HeightField Clip

一样的，顾名思义，裁剪。

还是这个地形：

![image-20211212200457385](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122006977.png)

连接节点看一下效果：

![image-20211212200731303](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122007360.png)

大于Max Clip的值都被切掉了，或者你也可以切下面，把低的值用Min Clip切掉。

# HeightField Crop

地形切块，可以把大地形切成小地形。

例如我这个地形是1000*1000的：

![image-20211212201017854](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122010570.png)

连接上节点，切成500*500的，就只剩这么大点了：

![image-20211212201033610](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122010768.png)

还可以处理切割的边缘。

# HeightField Cutout by Object

根据Object来裁剪地形。

![image-20211212201303742](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122013718.png)

![image-20211212201309915](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122013570.png)

只有在球半径内的地形才会显示出来。

反之如果你想挖洞的话那你就可以设置为Subtract：

![image-20211212201436185](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122014299.png)

# HeightField Distort by Layer

不常用，比较耗。泡泡节点。

自行感受：

![image-20211212201903089](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122019548.png)

连上节点，然后把Displace Scale稍微调大点：

![image-20211212201940561](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122019844.png)

# HeightField Distort by Noise

这个节点常用，可以给地形添加很多细节，有点像resample。

![dddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122025783.gif)

# HeightField Patch

这个节点很好用，可以直接把一个地形的一部分移动到另一个地形上。

比如这个是第一个地形：

![image-20211212202820838](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122028031.png)

这是第二个地形：

![image-20211212202834732](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122028882.png)

用一个Height Paint节点画一个罩波：

![image-20211212202854744](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122028879.png)

最后连接起来：

![image-20211212202916570](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122029868.png)

会发现第二个山的罩波部分的山直接被搬过来了。

# HeightField Pattern

这个节点可以做出一些比较奇特的效果，它同样有很多预设。

![image-20211212203329528](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122033862.png)

![image-20211212203355025](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122033891.png)

![image-20211212203407907](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122034672.png)