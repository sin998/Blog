# 【Houdini 地形制作】 07_又闻HeightField Erode

# 参数Overview

Erode节点有几个关键的节点：

![image-20211219161118230](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191611655.png)

认识一下Erode节点：

![image-20211219161250160](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191612606.png)

在高级面板下也能看见一些参数：

![image-20211219161818513](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191618641.png)

要注意某些节点需要解算特别久，我们可以手动指定更新方式（在右下角），如果不小心已经点到了，可以按下ESC来退出解算：

![image-20211219161924022](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191619157.png)

# 共用参数

侵蚀率Erodeability：开的越大地形就被侵蚀的越狠。

侵蚀速率Erodeability：开的越高单位时间内地形就被侵蚀的越快。

冻结帧Freeze at Frame：浏览指定第几帧下的地形被侵蚀的样子。

![image-20211219173217874](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191732144.png)

# 水力侵蚀和热力侵蚀比较

水力侵蚀就是在被雨水侵蚀之后带下一些泥沙，在山与山之间产生一些沟壑，形成山谷。如果完全关闭热力侵蚀，则不会产生一个山体滑坡的一个效果。

![image-20211219162136579](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191621565.png)

像热力侵蚀就是控制的山体滑坡的一个角度。

![image-20211219162457006](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191624476.png)

就是基本参数中的Cut Angle。当山体超过设定的这个角度时，山上的碎石和泥沙就会往下滚，产生热力侵蚀的效果。

如果同时开水力侵蚀和热力侵蚀的话就会又有沟壑又有滑坡。

# 精度的不同会对山体造成不同的影响

简单来说就是精度越高，地形的细节也就越多。

但是高精度的话就是这种：

![image-20211219163144651](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191631582.png)

如果是做电影当一个背景来说是可以的，但是不能用作与游戏，这个导入UE里面就会有各种小凸起，又丑人又不能在上面跑，但是这确实是在真实世界里面存在的。

所以一般制作流程是先用降低采样来规划地形，然后再用erode节点来添加一些细节。

## 250*250

![image-20211219163411209](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191634252.png)

## 500*500

![image-20211219163439791](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191634607.png)

## 1000*1000

![image-20211219163454480](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191634562.png)

## 2000*2000

![image-20211219163144651](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191635422.png)

# 关闭水力侵蚀产生的沟壑

这个也是2000*2000的地形，但是没有沟壑。

![image-20211219163720930](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191637172.png)

这个沟壑是可以关闭的。

用的是在Advanced-Debris Flow中的Water Absorption，即水的吸收率。

泥沙和碎石从山坡上滑下来的时候里面是带着水分的，当碎石滑下来的过程中水如果干掉的话那么碎石就会停留在半山坡，形成这种沟壑：

![image-20211219163144651](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191635422.png)

Water Absorption的取值是0~1。

- 0代表不吸收水分，碎石就不会停留在半山坡，而是直接滑落到山脚，就不会形成这种沟壑。
- 1代表吸收所有水分，碎石就会停留在半山坡，产生沟壑。

# 沟壑的强度和深度

控制沟壑的强度和深度有三种方式：降雨量、沟壑深度、反保护速率。

## 降雨量

降雨量：Advanced-Precipitation。

取值是0~1。值为0的时候几乎没有水力侵蚀的沟壑效果，因为没有降水也就不会带动碎石和泥沙滚动。

数值为0时：

![image-20211219165638229](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191656514.png)

数值为1时：

![image-20211219165701991](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191657841.png)

一般来说0.2就要好一点：

![image-20211219165723120](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191657059.png)

## 沟壑深度

Advanced - Hydro Erosion 中的 Max Debris Depth参数。

初始值是5：

![image-20211219165934166](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191659324.png)

调为0：

![image-20211219165952492](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191659496.png)

## 反保护速率

Advanced - Hydro Erosion - Riverbed（注意点箭头展开） 中的Deposition Rate。

此值越大，反而越不容易出现沟壑。

# 碎石数量

Advanced - Thermal Erosion的Remove Rate参数。

从山上滑下来的泥沙变成碎石这种单位的数量的比例。

取值是-1~1。作用于debris层。

值为-1时：代表从山上滑下来的1单位的石头变成2单位的泥沙，增加了。

![image-20211219170537572](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191705806.png)

值为0时：代表从山上滑下来的1单位的石头变成1单位的泥沙。

![image-20211219170628213](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191706200.png)

值为1时：代表从山上滑下来的1单位的石头变成0单位的泥沙，所以就没了，直接消失。

![image-20211219170609568](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191706479.png)

这个参数与Advanced - Thermal Erosion的Max Debris Depth参数相互作用。

例如：我现在这个值是5，那么泥沙再多也就不会再往下堆积了。换言之Grid Bias节点的值的泥沙数量深度如果≥5则就还是表现出5的效果，即泥沙为5和泥沙为10的效果是一样的。

# 坡度的朝向偏移

Advanced - Thermal Erosion的Grid Bias参数可以控制坡度的朝向。取值是-1~1。

为-1时：

![image-20211219171533547](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191715676.png)

为0时：

![image-20211219171542715](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191715025.png)

为1时：

![image-20211219171552336](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191715107.png)

# 把砂砾变大

Advanced - Water Flow的Quantization参数。

变大之后能够清楚的看到砂砾，这是值为2时：

![image-20211219172731232](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191727231.png)

是0时就是一种很平滑的效果：

![image-20211219172822978](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191728470.png)

当后面需要做碎的Mask的时候就可以用这一层，这一层比较碎：

![image-20211219172908839](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191729760.png)

# erode节点有很多层

其实erode节点有很多层的，可以用HeightField Copy Layer查看。

erode之前：

![image-20211219173526793](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191735528.png)

erode之后：

![image-20211219173538112](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191735041.png)

## bedrock

一般用不到。

![image-20211219173902007](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191739155.png)

## Sediment

如果需要丰富一点地形的话可以用这一层。

![image-20211219173935476](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191739747.png)

## Debris

主要是热力侵蚀计算出来的Mask。

![image-20211219174024320](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191740559.png)

## Water

Water层则是水力侵蚀计算出来的Mask。

![image-20211219174044930](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191740808.png)

## 重置或者清除这些层

如果不重置这些层的话那么下一个Erode节点就会直接用这些层的值，可能会有不预期的情况出现，可以在这里重置层。

![image-20211219174206039](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191742189.png)

# 缓存内存的占用大小

如果这个值大于5000MB的话最后这个渲染条就会向后偏移，即不存储前面的渲染结果，这个别调大了，容易崩。

![image-20211219174345040](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191743000.png)

![image-20211219174429072](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191744760.png)







