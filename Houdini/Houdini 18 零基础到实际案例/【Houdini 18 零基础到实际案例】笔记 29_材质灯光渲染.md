[TOC]

# 【Houdini 18 零基础到实际案例】笔记 29_材质灯光渲染

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

先创建一个摄像机：

![image-20211210212248469](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102122751.png)

取消显示、调整分辨率：

![image-20211210212559008](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102126211.png)

视口一下子就有了变化：

![image-20211210212844792](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102128663.png)

这几个选项是灯光相关的：

![image-20211210212936356](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102129584.png)

我们用这个：

![image-20211210213052932](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102130269.png)

如果不想看见这个环境贴图的话可以屏蔽掉：

![image-20211210213214031](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102132388.png)

它的作用是在的，只不过没有显示。

进入到渲染器：

![image-20211210213308624](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102133630.png)

使用内置的渲染器mantra，速度较慢，但是很稳定，可以对接Houdini中所有的渲染。

设置一下，摄像机是默认的：

![image-20211210213507073](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102135941.png)

切换到RenderView：

![image-20211210213820534](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102138536.png)

看一下默认渲染的结果：

![image-20211210213930422](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102139878.png)

我们现在开始创建材质：

![image-20211210214009790](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102140020.png)

创建一个principledshader节点，或者你也可以直接拖：

![image-20211210214306502](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102143318.png)

先改个名字：

![image-20211210214350569](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102143979.png)

把Metal赋给我们的锤子：

![image-20211210214512594](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102145869.png)

这是渲染的结果：

![image-20211210214619596](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102146862.png)

回到Mat。在Surface下面，IOR表示折射率。是控制物体反射强度的另外一种方式。为1则是表示不反射，高于1或者低于1都表示反射。

一般来说：

- 0.45是金子。
- 1.33是水。
- 1.5或1.6是玻璃。

我们用很高的值：3。

然后基础颜色用0.1。

其他的，例如高光和粗糙度都可以不用改，如下：

![image-20211210215050281](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102150183.png)

在拖拽出来再搞个皮革材质，先把它的基础颜色降到0.01：

![image-20211210215241685](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102152487.png)

由于我们是分了组的，所以不再整体给锤子添加材质了，先去掉：

![image-20211210215317388](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102153130.png)

我们直接进入锤子指定材质：

![image-20211210215435104](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102154503.png)

另外一个也是一样。

皮革材质：

![image-20211210215637974](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102156191.png)

灯光强度：

![image-20211210215836629](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102158856.png)

灯光角度，自己调：

![image-20211210215915253](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102159589.png)

按住Shift再鼠标拖拽就能选择区域，这部分区域会及时更新，其他的不会：

![image-20211210220022416](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102200845.png)

想要切回完整的可以点击画框外的任一点就行。

这些按钮可以点来玩一下：

![image-20211210220230742](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102202769.png)

你可以调高一点分辨率，先暂停下：

![image-20211210220357948](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102203831.png)

然后提高质量，渲染出结果：

![image-20211210220720534](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102207750.png)

渲染会比之前慢很多，但是质量会比较高，渲染完成直接百分百显示查看最佳效果：

还是很不错的：

![image-20211210221441082](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102214145.png)

![image-20211210221454884](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102214654.png)

![image-20211210221515174](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102215990.png)

保存：

![image-20211210221813181](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102218316.png)

![image-20211210222009173](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112102220132.png)

完成了！

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
