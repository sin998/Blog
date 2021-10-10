[TOC]

# 【UE4】UE开发先导_UE项目目录、资源、代码规范及管理

# 参考资料&原文链接

[虚幻官方文档 - 代码规范](https://docs.unrealengine.com/4.27/zh-CN/ProductionPipelines/DevelopmentSetup/CodingStandard/)

[Gamemakin UE4工程规范](https://github.com/skylens-inc/ue4-style-guide/blob/master/README.md)

[【UE4】UMG 使用C++对蓝图进行操作](https://www.cnblogs.com/sin998/p/15390903.html)

[ue4.style guide工程示例](https://gamemak.in/)

[Linter下载地址](https://www.unrealengine.com/marketplace/zh-CN/product/linter)

# 代码规范

官方的这篇文章写得明确且易懂，我看网上好多人都是直接赋值粘贴过来的，那么我就直接放链接吧：[虚幻官方文档 - 代码规范](https://docs.unrealengine.com/4.27/zh-CN/ProductionPipelines/DevelopmentSetup/CodingStandard/)。

请尽可能的熟悉这些规范，这会对您养成良好的编程习惯有很大的帮助。因为代码不仅仅是给机器执行的，更重要的是写给人看的。简单易懂语义明确的代码一定比一行就写完的代码更受人欢迎，在与他人沟通的时候也能一眼明白您代码的意图。

# 资产管理

大的要来了：

[Gamemakin UE4工程规范](https://github.com/skylens-inc/ue4-style-guide/blob/master/README.md)

请一定要去阅读。

这篇文章基于UE的代码规范、实际开发中的宝贵经验等总结出来了一套针对Unreal Engine很健全的规范。不论是代码和文件管理、资产和资产管理规范、工程目录规范等等都解释的十分的详细。你想知道的里面都有。

如果是新手，还没有团队的经验积累，那么这个工程规范会帮助你极大的减少所走的弯路。

如果是已经在开发但是尚未遵守这些规范或者有自己规范的团队，也可以看一下这个规范，取其精华，查漏补缺，从而完善自己的规范也只有益而无害。

# 亲身举例

说一下我目前接触到的几个规范，在这里面都有体现：

### 事件的响应函数和派发函数都应该以`On`开头

![image-20211010211657286](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110102116319.png)

在[【UE4】UMG 使用C++对蓝图进行操作](https://www.cnblogs.com/sin998/p/15390903.html)中，能看到发现我的响应按钮的点击事件的函数名是：

![image-20211010211606791](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110102116181.png)

### 变量的访问权限

![image-20211010211927115](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110102119169.png)

这个感受也很明显，例如你在C++代码中定义了一个变量，它是private的：



那么编译器在编译的时候会发出Warning，警告你这个变量是私有的：



# 工程示例

https://gamemak.in/

# 插件

最后，已经上架了一款插件，用来检查这些您项目中的内容与ue4.style中的样式指南相对应。

它的名字叫Linter。下载地址是：[点击这里](https://www.unrealengine.com/marketplace/zh-CN/product/linter)。

> 可让您扫描您项目中的内容与ue4.style中的样式指南相对应。
>
> 如果您的蓝图变量设置正确，Linter 会告诉您资产是否命名不正确，如果级别文件放错了位置，等等。
>
> 只需在内容浏览器中的任何文件夹中右键单击，您就可以运行 Linter。
>
> 要批量重命名，请在内容浏览器中选择多个资产，右键单击，然后选择批量重命名。
> 要编辑蓝图属性工具提示，请在内容浏览器中选择一个或多个蓝图，右键单击，然后选择工具提示编辑器。
>
> 随着ue4.style的风格指南更新，这个插件也会更新。

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE开发先导`、`UE工程规范`。

