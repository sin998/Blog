[TOC]

# 【Houdini 18 零基础到实际案例】笔记 16_CopyToPoints

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 测试猪头

先搞一个经典的测试猪头。

![image-20211205105543696](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051055938.png)

再搞一个Grid。

# Copy to point

最后用一个Copy to point把他们连接起来：

![image-20211205105910835](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051059686.png)

看一眼需要的参数：

![image-20211205110042657](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051100658.png)

![image-20211205110054750](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051100833.png)

把猪头缩小一点就能发现猪头是朝上的：

![image-20211205110149193](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051101139.png)

很明显，猪头的朝向就是Grid的法线方向：

![image-20211205110337717](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051103803.png)

![image-20211205110245010](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051102995.png)

你也可以拷贝源（几何体）特定的组的点到目标点上：

![image-20211205110541035](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051105283.png)

# Group

添加一个Group节点：

![image-20211205110715570](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051107235.png)

默认的是选择一个面作为一个组，我们现在改成选择点为一个组：

![image-20211205110749155](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051107026.png)

然后选一些点，选好后按下回车：

![image-20211205110852882](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051108227.png)

最后再修改一下使用Group来Copy：

![image-20211205111029968](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051110107.png)

一样的，源可以打组拷贝，目标也可以打组拷贝：

![image-20211205111316916](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051113894.png)

![image-20211205111408587](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051114579.png)

# 拷贝属性设置

可以指定拷贝的属性：

![image-20211205112436443](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051124516.png)

我们修改一下法线，让猪头的指向变化。使用attribwrangle节点修改属性，用Houdini内置的语言表达式来修改，修改好之后用回车+Ctrl确认表达式。

![image-20211205113119303](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051131107.png)

![image-20211205113259928](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051133897.png)

这时我们可以看下它有哪些属性：

![image-20211205113833086](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051138187.png)

你也可以给他修改一下方向，用向上方向来指定：

![image-20211205113812581](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051138036.png)

这时会发现又增加了一个向上属性：

![image-20211205114011333](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051140765.png)

另一个节点（Copy to curve）也是类似的，不在赘述。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
