[TOC]



# 【GAMES101-现代计算机图形学入门-闫令琪】 Lecture 06 Rasterization 2 (Antialiasing and Z-Buffering)

# 这节课的目标

![image-20210908095156283](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080951442.png)

![image-20210908095324817](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080953982.png)

# 采样的类型

## 位置采样

![image-20210908095447118](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080954552.png)

## 时间采样

![image-20210908095500791](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080955610.png)

# 采样的瑕疵(失真)

![image-20210908095608893](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080956878.png)

## 锯齿

![image-20210908095735647](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080957724.png)

## 摩尔纹

![image-20210908095753478](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109080957573.png)

把左边的奇数行和奇数列全部去掉，这个去掉的图肯定比原先的要小，但是把这个图再用原来的大小拼起来就是右边那个图的效果。仔细观察左右图的领带，会发现原先的格子已经全部乱掉了。用手机拍摄显示器的屏幕也会有这种问题。

![image-20210908100208504](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081002694.png)

这个是个GIF动图，建议看原视频。这个还是有一个现象与这个一样，就是有些时候汽车在跑的时候那个轮子就像是在倒转一样。

这个现象出现的原因是，人眼在时间上的采样跟不上运动速度。

## 出现的原因

![image-20210908100744043](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081007383.png)

所有的采样的瑕疵出现的原因都是因为信号的变化太快了，以至于采样的速度跟不上。

# 采样瑕疵的解决办法

## 采样前模糊

在采样前做一个模糊。

![image-20210908101048334](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081010666.png)

先对图像进行模糊，然后再采样，靠近边缘的颜色呈现一个半红半白的状态。

![image-20210908101137438](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081011411.png)

在模糊之前：

![image-20210908101439095](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081014519.png)

在模糊之后：

![image-20210908101455669](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081014756.png)

## 先模糊还是先采样

这是先模糊再采样得到的结果

![image-20210908101610244](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081016171.png)

这是先采样再模糊得到的结果

![image-20210908102150752](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081021399.png)

所以先采样再模糊是不可以的，因为这相当于是信号频率不变，采样频率变更慢了。

# 很多的为什么

![image-20210908102930631](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081030479.png)

## 频域(Frequency Domain)

### 余弦和正弦

先看简单的余弦和正弦

![image-20210908103429428](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081034948.png)

它们只是相位不一样。

![image-20210908103516524](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081035367.png)

但是当考虑了前面的系数以后，通过调整这个系数就能够得到不同的余弦波。它们的不同在于频率不同。

除了定义频率还可以定义周期T，T=1/f。

### 傅里叶级数展开

任何一个周期函数，都可以写成一系列正弦和余弦函数的线性组合以及一个常数项。

要描述的函数就是一个方波（左），现在用另一个函数来描述它，它相当于一个cos函数往上面提了（右），这样算是一个近似的描述：

![image-20210908114620362](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081146330.png)

现在的f(x)是：

![image-20210908114824525](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081148837.png)

当然还可以用更多的函数来描述：

![image-20210908114851236](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081148498.png)

f(x)：

![image-20210908114910486](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081149633.png)

继续近似：

![image-20210908114936998](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081203129.png)

f(x)：

![image-20210908114950045](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081149434.png)

最后越加越多就会越来越像：

![image-20210908115017536](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081150601.png)

f(x)：

![image-20210908115027617](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081150597.png)

这样一直加上去就会无限接近于要描述的方波。

这样的话用傅里叶级数展开就可以把一个函数描述成很多的正弦余弦的组合。

### 傅里叶级数展开与傅里叶变换

![image-20210908120442356](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081204136.png)

傅里叶变换：给定一个函数，用一个相当复杂的一个操作，变成了另一个函数，即f(x) -> F(w)。

逆傅里叶变换：即上述变换的逆变换，F(w) -> f(x)。

### （逆）傅里叶变换和正弦余弦函数有什么联系呢

不同的余弦函数有不同的频率：

![image-20210908120924197](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081209393.png)

通过傅里叶变换可以将不同的函数分解为不同的频率。

![image-20210908121555237](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081215204.png)

## 走样

高频信号采样不足表现就会发生错误

![image-20210908141050876](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081410857.png)

和上面那个图一样，也是一个高频信号（蓝色的线）采样，恢复出来（黑色的线）就表现出来是一个低频信号的样子。

现在换一个思路，将这两个低频（黑）和高频（蓝）都看出两个信号，现在就变成了我用一样的采样频率去采样两个完全不同的信号，得到的结果确是完全相同的，我们无法区分它。这就是“走样”的定义。

## 滤波和傅里叶变换

去掉一些频率的信号。

![image-20210908141918611](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081419890.png)

傅里叶变换的作用：可以把一个函数从时域变到频域。

例如下图，左边是一张图片，右边是变换后的图片。

![image-20210908144201776](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081442813.png)

虽然原图（左）不代表任何时间信息，但是我们也认为它在自然界中的在不同时间的不同位置，也算是时域信息。

右边那副图怎么理解呢。

- 我们将中心定义为最低频的区域，照片周围定义成高频区域，所以从中心到周围的频率会越来越高。
- 在不同频率的区域有多少信息我们通过亮度来表示。通过看图发现这张图大多数信息主要集中在中间，因为中间比较亮，相较于中心的低频信息高频信息就显得少的了很多。事实上在自然界中的图片都是这样，不论是自己手写还是用现成的库最终的效果都是这样。
- 垂直和水平的那个特别明显的线是什么呢？我们在分析信号的时候，一般认为信号都是周期性重复的信号，但如果图片不重复的时候（如原图），我们就认为在水平和垂直方向都平铺了无数张这样的图。
- 正常情况下很少会有图片会左边界和右边界一样的，所以在图片平铺的边缘的时候就会产生及其剧烈的变化，即极高的高频，所以就会产生这两条线。但我们为了分析其内部，一般忽略这两条线。

所以傅里叶变换可以让我们看到图像在不同的频率/信号下长什么样，我们称为频谱。

### 高频滤波

![image-20210908144513714](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081445959.png)

这幅图相比于上一幅图，傅里叶变换后（右）将低频信息全部抹去（中间是低频，全部涂黑），这种滤波叫高通滤波，即只有高频的信号可以通过。

这时候剩下的都是高频的东西，将它逆傅里叶变换还原回去后发现高频信息表示的就是图像的边缘。

什么是边缘呢？

![image-20210908144928653](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081449771.png)

即图像中频率变化剧烈的地方，比如鼠标指着的衣服袖口和后面模糊的背景。

### 低频滤波

与对信号应用高通滤波相反，下面那副图（右）是对图像应用低通滤波后的样子，然后再将图片还原成回去就得到了左边那副图。

![image-20210908145312252](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081453068.png)

很明显，没有了边缘信息，图片变得无比模糊。图片的边缘看起来像一个水波纹，它是一个不完美的低通滤波产生的问题。

### 中通滤波

![image-20210908145630175](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081456944.png)

中间偏高频的信息：

![image-20210908145744237](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081457355.png)

## 滤波=卷积=平均

![image-20210908150110355](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081501661.png)

### 卷积

![image-20210908150358237](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081503265.png)

上面那个是信号，可以看出是一个数组。下面那个是过滤器，可以看成是一个权值。

最下面那个结果就是一个新的数组，由过滤器和信号经过一定的运算得来。

运算方法是对应的信号值与对应的过滤器的值相乘相加（即加权平均，这个结果就是周围的数加上一个权值然后求平均），然后填入中间（红）对应的格子中，格子再往下移动，继续计算，现在继续移动：

![image-20210908150735603](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081507838.png)

这样就会得到一个新的用过滤器处理过的信号。

### 卷积的结论

时域上我想对两个信号进行卷积，那它对应到两个信号各自的频域上就是两个信号各自的乘积。即**时域卷积等于频域乘积，反过来时域上的乘积等于频域上的卷积也是成立的。**

![image-20210908151305430](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081513382.png)

这是个例子：

![image-20210908151703181](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081517997.png)

将卷积盒分成9份，一共是3×3的像素格。先将卷积盒归一化再乘以1/9，这样就不会引起亮度改变。然后把这个图像和卷积盒做一个点乘，任何一个像素都是周围像素3×3的一个平均，得到的自然是一个模糊的图像像。

也可以先把原图和卷积盒做一个傅里叶变换再相乘，得到的结果在逆傅里叶变换回去也能的到一个模糊的图像。

![image-20210908154020116](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081540319.png)

先将卷积盒归一化再乘以1/9，这样就不会引起亮度改变。如果不归一化的话图像就会越来越亮，这样是不对的。

### 盒子滤波器=低通滤波器

![image-20210908154432637](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081544570.png)

一个盒子从时域变到频域上。

如果这个盒子在时域上变大了/变小，那么对应的频域如何变化呢？

![image-20210908154722967](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081547300.png)

在刚才那个例子中，用的是3×3的盒子，如果用越大的盒子那么图像就会越模糊，因为加权的像素格更多了嘛，盒子越大就会把越高的频率筛掉，所以结果会越模糊。

相反用更小的盒子，比如比一个像素还小，这相当于根本就没有对信号做过滤。

### 采样=重复频率的内容

![image-20210908155037580](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081550295.png)

![image-20210908162553503](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081625468.png)

在时域上：

a：原来的信号。

c：冲激函数。即只在箭头上有值（为1），其余部分没有值（为0）。

e：a × e的结果。即采样。

在频域上：

注意前面的结论：时域上的乘积等于频域上的卷积。

b：a的时域对应的频域。

d：c的时域对应的频域。

f：b卷积d的结果。即频域。

这样就会发现一个函数采样之后变成了离散的点，其实就是把原始函数的对应的频谱给复制粘贴了很多份，即采样就是重复原始的频谱。

## 为什么会走样

采样的不同的间隔会引起频谱以不同的间隔移动。

![image-20210908163618716](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081636692.png)

上图是采样的频率和频谱的频率相等。

下图是采样频率小于频谱频率就会发生混叠，就会产生走样。

## 如何反走样

![image-20210908164028202](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081640121.png)

砍掉高频信号再采样，即先模糊。

![image-20210908164132060](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081641506.png)

如图，我们认为虚线之外就是高频信号，我们这时就不要了，直接砍掉，然后对剩下的信号再采样，这样就不会发生混叠。

回顾：

![image-20210908164345737](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081643481.png)

就是先把原本的三角形模糊，即卷积（对像素周围的点用一个盒子来平均）。

![image-20210908164532652](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081645455.png)

先模糊再采样。

最简单的就是用一个像素格来作为盒子，即只对自己那个像素格来进行平均：

![image-20210908164748632](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081647129.png)

然后就能的到卷积的值：

![image-20210908164808692](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081648831.png)

其实求出它的平均还挺难的。所以人们就用了一个近似的方法来处理，但是近似并不能真正解决这个问题：MSAA。

### MSAA

![image-20210908164918848](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081649806.png)

将一个像素划分成很多小的像素，比如这里划分的是4×4：

![image-20210908165446912](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081654894.png)

然后对每个像素的中心点判断三角形是不是中心点在三角形内，然后再把这些判断平均起来，就是对三角形像素的覆盖区域的近似。

假设现在是这样：

![image-20210908165637064](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081656346.png)

然后对这个像素多加一些采样点（例如这里用的是2×2的）：

![image-20210908165710718](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081657844.png)

然后一直判断直到：

![image-20210908165753781](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081657804.png)

即这个三角形在这个像素的平均覆盖率是75%：

![image-20210908165833969](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081658872.png)

然后得到所有点的平均覆盖率：

![image-20210908165924556](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081659833.png)

然后再对这个结果进行采样：

![image-20210908165954478](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081659436.png)

示例：

![image-20210908170106718](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081701443.png)

### MSAA的代价和其他抗锯齿的方法

代价就是计算量变大。

![image-20210908170712233](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202109081707071.png)

# 参考资料&原文链接

[GAMES101-现代计算机图形学入门-闫令琪](https://www.bilibili.com/video/BV1X7411F744)

[猎豹网校：游戏开发之3D数学基础](https://www.bilibili.com/video/BV1ib411K7TK)

# 本文标签

`计算机图形学`、`游戏开发`、`GAMES101-现代计算机图形学入门-闫令琪`、`Unreal Engine`、`游戏开发基础`、`视频系列笔记`、`笔记`。