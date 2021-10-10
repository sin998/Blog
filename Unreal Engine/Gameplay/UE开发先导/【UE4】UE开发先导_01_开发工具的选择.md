[TOC]

# 【UE4】UE开发先导_开发工具的选择

# 参考资料&原文链接

[Microsoft-Visual Studio](https://visualstudio.microsoft.com/zh-hans/downloads/)

[虚幻官方文档 - Setting Up Visual Studio for Unreal Engine](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/DevelopmentSetup/VisualStudioSetup/)

[Visual Studio中常用快捷键](https://www.cnblogs.com/kangao/p/5254738.html)

[VS快捷键使用技巧（超全）](https://blog.csdn.net/kangjielearning/article/details/106432189)

[VS小番茄使用指南](https://blog.csdn.net/Littlehero_121/article/details/105338338)

[Visual Assist X（小番茄）快捷键](https://blog.csdn.net/u013271921/article/details/45580157)

[番茄助手快速添加注释+快捷键](https://blog.csdn.net/u011135902/article/details/52336430)

[JetBrains-Rider For Unreal](https://www.jetbrains.com/lp/rider-unreal/)

[【UE4】Rider For Unreal体验报告](https://zhuanlan.zhihu.com/p/379911259)

# 前言

开发工具有很多，不过大家比较认可比较流行的就俩：VS和Rider。

这里假设您对搜索引擎足够熟练，使用搜索引擎就能够获得关于这两款开发工具的很多信息。如果您实在是找不到的话，那么本文的参考资料部分会有上手的链接。

这两款开发工具各有千秋，不一定谁比谁强，也不一定是谁就一定比谁好用。实际上「强」和「好用」都是个人的主观感受。

所以本文只是简单的将它们二者的特点罗列比较之，给新手朋友或者是想尝试另一个编辑器的朋友一个参考，大致了解一下其功能。

# Visual Studio

## 简介

众所周知VS是宇宙第一IDE(网友说的)。VS也确实不负众望，确实是一款很强大的Dev工具。相信大家也多多少少接触过，不过要和UE一起使用的话还是有一些设置会有点不一样。

VS的下载地址在这里：[Microsoft-Visual Studio](https://visualstudio.microsoft.com/zh-hans/downloads/)。

虚幻的官方指引：[虚幻官方文档 - Setting Up Visual Studio for Unreal Engine](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/DevelopmentSetup/VisualStudioSetup/)。

快捷键：[Visual Studio中常用快捷键](https://www.cnblogs.com/kangao/p/5254738.html)、[VS快捷键使用技巧（超全）](https://blog.csdn.net/kangjielearning/article/details/106432189)。

## VS插件

### Visual Assist(小番茄)

VS加上小番茄简直如虎添翼。有了小番茄，就可以拥有许多强大又常用的功能：

- 辅助重命名。
- 快速重写函数。
- 快速引入头文件。
- 快速创建函数实现。
- 找到所有的文件引用。
- 在声明和定义之间快速跳转。
- 全局查找文件、查找某个变量名。
- 自定义并快捷添加方法注释和类注释模板。
- 当类过于复杂时查看类的结构图。
- 快捷键的支持。

您可以通过以下链接来学习VS的用法：[VS小番茄使用指南](https://blog.csdn.net/Littlehero_121/article/details/105338338)、[Visual Assist X（小番茄）快捷键](https://blog.csdn.net/u013271921/article/details/45580157)、[番茄助手快速添加注释+快捷键](https://blog.csdn.net/u011135902/article/details/52336430)。

![image-20211010173234985](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110101732751.png)

### ForceUTF-8

这个插件是VS必要的。因为VS默认的保存编码格式是GB2312，这样的话传到Git上，中文注释都会变成乱码，因为Git的编码是UTF-8。别人的话打开您的文件要么是一堆问号要么是乱码，这会给多人协作带来烦恼。

而每次保存都需要用高级保存来保存为UTF-8（现在可以设置了）这样也未免太麻烦了一点。

所以我需要一个插件来帮我每次自动保存为UTF-8，那么ForceUTF-8可以帮助您做到。

## VS的不足

那VS有些什么缺点呢？

- 加载项目要的时间比较久，特别是对于电脑配置暂时不高的开发人员或者学生党来说就不是这么友好了。
- 某些时候要跳转到某个变量或者函数的定义/声明会卡住，有些时候还会等待到打断编程/Debug思路。

# Rider For Unreal

## 简介

这个软件一打开的时候界面就给我一种很熟悉很舒服的感觉，不会像VS那样有一点点生硬。

这是JetBrains专门为Unreal打造的专属IDE。

## 特点

这个工具的特点是：

- 界面简洁。

- 大量的主题、字体可以更换。

- 有很多很快捷常用方法：

  ![image-20211010184718028](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110101847838.png)

- 方便。VS的快捷键在Rider中都有直接的映射。

- 快快快。不论是加载项目（只有第一次加载慢一点）还是查找符号都飞快。

- 对UE代码风格的严格检查。例如需要驼峰大写方法名、继承于UObject的类必须以U开头等等。

这里面有个VS没有的功能，就是对蓝图的支持。这个功能太强了，比如说在编写用户界面的时候这个功能简直就是神器，我忘记我的C++（UserInterface）是被哪个UMG继承的了，那么你就可以在这里看到：

![image-20211010184548783](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110101845976.png)

还有：

![image-20211010200825556](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110102008533.png)

注意：如果要触发断点，那么这两个东西必须对应：

![image-20211010183914747](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110101839027.png)

快捷键就不说了，相信各位读者的搜索引擎用的是很熟悉的。

# 总结

反正两款IDE各有千秋，萝卜青菜各有所爱，我最开始用的是VS（然后没跑起来），换到Rider之后才发现是真的香。

我的电脑暂时还没有换，配置是16(RAM) + 500(ROM,SSD) + I5-7300HQ + GTX1050。

然后，我发现我的电脑跑VS跑不起来- -艹。迫不得已换到Rider，然后发现居然跑起来了....

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE开发先导`。

