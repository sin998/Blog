[TOC]

# 【Houdini 18 零基础到实际案例】笔记 18_拷贝符文

# 参考资料&原文链接

本篇系列博客来自于B站UP：[老胡特效](https://space.bilibili.com/324928136)。教程链接是：[点击前往](https://www.bilibili.com/video/BV1Hi4y187Ww)。

# 贴符文

这一步有两种方法。

一是只处理一边（四个面），然后用镜像过去即可，就是这一步：

![image-20211205151740338](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051517660.png)

二是直接在最下面选中8个面来操作，就是在细分之前：

![image-20211205152011068](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051520008.png)

# 处理符文

现在我们得到符文是这样的：

![image-20211205152208048](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051522120.png)

这肯定是不对的，我们要把符文转一面，不然没有封口的这一部分就显示在外面了。我们给他掉个头：

![image-20211205152416770](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051524579.png)

# 新建数据流

![image-20211205151740338](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051517660.png)

选中之后直接按下（此时鼠标在视口）键盘的delete键：

会发现多出来一个节点blast节点，并且把选中的面删除了：

![image-20211205152815361](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051528676.png)

blast节点是delete的节点的简化版本，参数比delete少一些。

我们把它搞出来，新建一个数据流，不要影响下面的：

![image-20211205152913512](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051529731.png)

然后保留我们选中的面，删除其他的面：

![image-20211205152957704](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051530411.png)

查看面的法线可以发现面确实是有法线的，不过我们只能拷贝到点上面，没办法拷贝到面上面。

![image-20211205153139767](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051531787.png)

先给这几个点添加一下法线：

![image-20211205153444268](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051534347.png)

可以发现一个平面内的法线都是一样的，要是我能把这几个点揉到一起变成一个点，就能用copy to point了，问题解决。

没错，我们先用primitive节点将几个点缩到一起：

![image-20211205154042811](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051540697.png)

![addddsdsdddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051540850.gif)

注意：

- 缩放到0时法线会消失。

- 现在还是一个面有8个点，我们还并没有融合。

  ![image-20211205154149393](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051541363.png)

现在我们融合一下，使用fuse节点：

![image-20211205154347349](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051543103.png)

好像视口啥都没了。我们要设置一下，去掉这个勾选：

![image-20211205154429788](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051544563.png)

现在的话就只有8个点了：

![image-20211205154452661](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051544723.png)

但是开了法线显示还是没有显示出法线：

![image-20211205154948843](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051549794.png)

这是因为还要去除这个选项，fuse节点会重新计算法线，我们就用以前的法线，不让他重新计算：

![image-20211205165139577](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051651437.png)

然后就是使用copy to point啦：

![image-20211205165326849](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051653045.png)

不过好像有点大，先缩小一点：

![image-20211205165413659](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051654426.png)

现在发现好像有点问题，我们没法控制它的方向。一样的，我们还需要一个变量up来控制，所以还是搞一个attribute wrangle节点，里面输入：

```
vector v_temp_right = {1, 0, 0};
v@up = cross(v_temp_right,@N);
```

![image-20211205171345398](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051713057.png)

好像还是差一点点，似乎旋转个90°就OK了：

![image-20211205171426925](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051714722.png)

merge看一下：

![image-20211205171544723](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112051715877.png)

效果完美。

# 本文标签

`游戏开发`、`Houdini`、`Houdini基础`、`程序化生成`、`笔记`、`视频笔记`。
