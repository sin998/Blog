[TOC]

# 【Houdini 18 零基础到实际案例】笔记 07_Houdini基础操作

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 放置常用几何体

![image-20211127201113296](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272011896.png)

![image-20211127201345589](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272013054.png)

接下来放置一个圆环。

# 变换操作模式

放置以后会默认进入到变换操作模式，这时Houdini会将你所有可能需要的操作都列出来：

![image-20211127201749036](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272017238.png)

退出操作模式：按下ESC。

或者在选中物体后按下回车也会进入到综合变换操作模式。

# 选中物体

要选中视口中的物体有两个方法：直接点击Selecte，然后再到视口中进行选择：

![image-20211127201943601](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272019017.png)

也可以在节点网络里面进行选择：

![image-20211127202113569](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272021589.png)

# 聚焦物体

Ctrl + F是以当前视角聚焦到物体。

![image-20211127202604412](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272026682.png)

Ctrl + G是以Home视角聚焦到物体。

![image-20211127202514897](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272025979.png)

Home视角是固定的。

以Home视角来显示视口中所有的物体：Home + H。

# 参数调整

![image-20211127202839473](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272028350.png)

# 几何体操作栏

针对几何体的操作栏：

![image-20211127203200725](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272032157.png)

# 删除物体

选中物体再按下Delete即可。

# Ctrl创建到中心

![image-20211127203432155](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272034604.png)

这个操作是很方便的，假设我现在准备好了物体，我想以一个特定的视角去观察它，那么我们需要一个摄像机，这时直接按住Ctrl再点击摄像机，Houdini就会自动帮我们把摄像机放置到我们想要的特定的位置和角度来观察物体，这就很方便了：

![image-20211127203758236](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272037171.png)

![image-20211127203919410](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272039492.png)

在树视图和网络节点界面也能看到：

![image-20211127204110349](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272041490.png)

# 节点的进入退出

![image-20211127204305798](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272043650.png)

![image-20211127204451431](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272044402.png)

层级选择：

![image-20211127204517354](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272045385.png)

# 节点内部创建节点

按下Tab或者点击鼠标右键都可以呼出面板。

![image-20211127204835416](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272048550.png)

![image-20211127204906069](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272049901.png)

![image-20211127204914195](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272049596.png)

注意：在节点中创建的子节点默认是不可见的。

![image-20211127205252804](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272052548.png)

# 节点标签

项目开始的第一个步骤几乎都是新建几何体。

![image-20211127221656570](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272216749.png)

然后在这个下面又创建了一个Line、一个Box、一个Sphere。

## Display

是否在视口中显示。此标签将影响自己和子节点。

注意：点小眼睛和点节点上最左边那个蓝条效果是一样的，只是更方便点。

![image-20211127225842719](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272258685.png)

![image-20211127225901520](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272259524.png)

## Selectcable

是否能在视口中选中此物体。

![image-20211127230133784](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272301754.png)

关了的话就不能选中物体：

![image-20211127230254629](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272302898.png)

## Node Info

查看此节点的详情信息。

![image-20211127230333614](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272303685.png)

![image-20211127230403033](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272304886.png)

这个Geo容器有这几个标签。现在进入一层看下Box有什么标签。

## ByPass

![image-20211127230733944](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272307297.png)

如果有数据流的话就表示从这里穿过去，即不考虑这个节点对数据的处理：

![image-20211127230918214](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272309558.png)

## Lock

锁定节点。锁定之后不能够对改节点进行操作，比如调整位置和参数，开关其他标签等。

![image-20211127231022454](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272310769.png)

## Template

打开后可以做一个参考。

![image-20211127231656633](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272316601.png)

这样的话就知道Box相对于Sphere的信息。

![image-20211127231749082](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272317984.png)

若是关闭了就看不到相对信息了：

![image-20211127231910554](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272319516.png)

同样的若是以自己为参照物也只能看到自己，看不到其他的几何体。

![image-20211127231944662](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272319886.png)

## 鼠标悬停获取帮助

在鼠标悬停到Houdini的任意一个图标上时会提示有关信息。

![image-20211127232053971](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272320396.png)

![image-20211127232102794](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272321528.png)

# 平滑/线框显示

平滑显示：

![image-20211127232443369](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272324729.png)

线框显示：

![image-20211127232453994](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272324642.png)

切换的方式是Shift + W。

这只是它们的快捷键，真正点的地方在视口的右上角：

![image-20211127232609782](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272326121.png)

# 属性恢复默认值

要是不小心改崩了可以恢复默认值，在对应的属性右键再选择恢复即可。

![image-20211127232912133](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272329609.png)

这个快捷键是Ctrl + 鼠标中键，MMB就是鼠标中键（Mouse Middle Button）。

# 综合操作

前面说过选择物体之后Houdini会列出所有的操作，并将它们综合到一起，显示操作手柄供我们操作：

![asdads](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111272334049.gif)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
