[TOC]

# 【Houdini 地形制作】 08_塑山的几种方式

# 罩波塑山

我们一般用这个罩波来塑形。

![image-20211219192202946](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191922352.png)

不过这个罩波大部分是在平面以下的，如果直接画的话我们就会得到这种地形：

![image-20211219192318979](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191923953.png)

全在地平面以下了，这是不太理想的，所以我们用remap把它提起来：

![image-20211219193242679](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191932502.png)

展示最后一个节点，然后选中paint节点，在视口中按下回车出操作手柄然后开始画：

![image-20211220202931558](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202029566.png)

这样我们就能快速弄一个山出来了：

![ssddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191933202.gif)

# Curve塑山

除了这样还可以用一个曲线来塑山。

通过调整曲线就可以塑山了。

![image-20211219195218364](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191952270.png)

# Copy一个Object作为山

注意把tube调大点，不然看不见。

![image-20211219195744441](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191957411.png)