[TOC]

# 【UE4】UMG_06_加载资源的方式

# 参考资料&原文链接

[UE4笔记---C++加载BP蓝图及C++BP蓝图文件并创建UUserWidget对象](https://blog.csdn.net/chinahaerbin/article/details/74905439)

[UE4资源加载的方式](https://www.sohu.com/a/203578475_667928)

[UE4资源加载的几种方式](https://zhuanlan.zhihu.com/p/197792243)

[虚幻4批量加载资源，加载指定目录或指定类的资源](https://blog.csdn.net/qq_16756235/article/details/82714491)

[UE4中资源加载资源的方式](https://blog.csdn.net/a359877454/article/details/52765133)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[Unreal Engine 4 —— Asset Manager介绍](https://blog.csdn.net/noahzuo/article/details/78815596)

[虚幻官方文档 - AssetManager](https://docs.unrealengine.com/4.26/zh-CN/ProductionPipelines/AssetManagement/)

[【UE4】资源管理之UAssetManager用法](https://zhuanlan.zhihu.com/p/129712105)

[如何评价unreal4.17的Asset manager?](https://www.zhihu.com/question/66615175?sort=created)

[芭蕉不解的博客](https://bajiaobujie.github.io/)

# 加载UObject和UClass

这里说的**静态加载指的是必须在构造函数中完成的加载方式**，**动态加载值得是可以在Runtime期间加载的方式**。UE4源码里面，前者其实是对后者的一层封装，即FObjectFinder()是对LoadObject()的**封装**。But，FClassFinder()不是对LoadClass()的封装，FClassFinder()**内部调用**的是LoadObject()。

## 静态加载



## 动态加载

























# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 用户界面`、`UE4 UMG`、`UMG基础`。

