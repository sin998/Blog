[TOC]

# 【Houdini 18 零基础到实际案例】笔记 11_锤头微小细节

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 小曲线细节

![image-20211201210223447](C:/Users/71080/AppData/Roaming/Typora/typora-user-images/image-20211201210223447.png)

![image-20211201210842358](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112012108588.png)

使用Curve节点：

在视口回车可以看到操作手柄，然后在视口中点击鼠标即可绘制曲线。

![image-20211201210650021](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112012106421.png)

结束之后再次按下回车以确认，每个点以空格隔开：

![image-20211201210756681](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112012107724.png)

现在就可以打开参考图然后开会绘制连线：

![image-20211202144942076](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021449076.png)

注意，点肯定都在某一个平面上，所以有一个轴的值必然是一样的。

如果是在弄不好，那么就输坐标吧，直接整四个点，然后输入坐标即可。

![image-20211202145221653](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021452748.png)

可以使用polywire节点来给这个线段一个厚度：

![image-20211202145257370](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021452845.png)

可以看见默认它是非常厚的，我们需要调整一下它的参数，将Wire Radius调整为0.003差不多。默认可以看到有四条边：

![image-20211202145426512](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021454957.png)

这个节点不错，但是不能得到我们想要的效果，所以不用。

添加一个Grid节点，默认是非常大的：

![image-20211202145608510](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021456530.png)

把Size设置为0.0005和0.0025。细分面Rows和Columns设置为2即可。

接下来添加一个sweep节点，把鼠标停留在输入上面就可以知道它这个节点想要什么：

![image-20211202145932940](C:/Users/71080/AppData/Roaming/Typora/typora-user-images/image-20211202145932940.png)

而这种就是必须要输入一个数据，不然节点不能正常工作。

![image-20211202150131491](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021501421.png)

连接好节点，看一下效果：

![image-20211202151834191](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021518230.png)

根据自己的实际把Grid的面立起来，贴合蓝图这一面：

![image-20211202210234881](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022102828.png)

可以看到默认是用的法线来作为它的向上向量：

![image-20211202152450207](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021524491.png)

查看Curve节点的信息可以发现并没有Normal：

![image-20211202152615845](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112021526818.png)

接下来用Polyframe搞一下：

![image-20211202204824524](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022048525.png)

信息窗口可以按住Ctrl+MMB弹出。

使用firstedge可以看到更精确的指示：

![image-20211202204957882](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022049035.png)

然后就能的到扫描出来的东西：

![image-20211202205851569](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022058011.png)

咱给这个封个口：

![image-20211202210346198](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022103274.png)

![image-20211202210412706](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022104593.png)

# 制作另一半曲线

直接复制过来：

![image-20211202210850143](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022108010.png)

![image-20211202211028641](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022110737.png)

注意Clip的参数修改，是要减哪一半，不然镜像得不到正确的结果。

如果稍微歪了一点的话可以有两种方法调整，一种是用Transform：

![image-20211202211530444](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022115284.png)

另一种是调节Grid，先看一眼：

此时的Grid是一半在上另一半在下。然后骨架线正好在中心：

![image-20211202211819647](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022118355.png)

一样的，用相对参数把它弄起来：

![image-20211202212040801](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022120257.png)

然后再看一下骨架线就会发现骨架线是在下面：

![image-20211202212109207](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022121444.png)

这时候就能够沿着底边调节高度啦：

![image-20211202212154551](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022121787.png)

先把他们合并，便于观察：

![image-20211202212446249](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022124483.png)

然后把做好的东西用transform移出来：

![image-20211202212538711](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022125572.png)

![image-20211202212702738](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022127826.png)

这里有个小技巧，就是我的节点现在非常乱：

![image-20211202212826901](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022128908.png)

我想把它排直，但是一个一个点肯定又非常麻烦。所以可以选中希望排序的节点，然后鼠标放在一个节点上，按下a键，然后按下鼠标左键不松动直接往下滑动：

![asdaddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022133019.gif)

你也可以往左右拉水平对齐也可以：

![asdaddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022135923.gif)

也可以选中节点，按下shift+s，弯曲连接线，平滑一点要好看：

![image-20211202213743276](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022137291.png)

![image-20211202213757123](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022137883.png)

再在下面搞一个mirror，把这个操作也镜像过去：

![image-20211202213904945](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022139811.png)

然后用boolean把两个条和锤头相减：

![image-20211202214235627](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022142853.png)

观察发现最好切换到Smooth Shade：

![image-20211202214405372](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022144683.png)

可以看见明显有个黑块，这个东西现在没有法线：

![image-20211202214435490](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022144647.png)

添加一个normal就有了：

![image-20211202214925893](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022149026.png)

![image-20211202214852339](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022148359.png)

注意，要是在这一步加了polyframe的话就是有法线的，此时表现就是正常的，那下面就不用再添加normal了：

![image-20211202215047584](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112022150933.png)

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
