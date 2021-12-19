# 【Houdini 地形制作】 04_安装HoudiniEngine

先打开文件所在位置。

![image-20211219151440231](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191514956.png)

默认是到这个文件夹下面的：

![image-20211219151505631](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191515762.png)

进入到文件夹：

![image-20211219151634918](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191516963.png)

一样的打开UE的文件夹位置，进入到插件文件夹中，然后拖进去。

![image-20211219152043519](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191520753.png)

一般来说直接放到这里面重启UE就行，但是官方建议我们放到Runtime里面，不知道为什么- -，既然官方都这样说了那还是放一下：

![image-20211219152211222](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191522026.png)

在UE里面应该就能看到：

![image-20211219152630903](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191526660.png)

在Houdini里面搞一个地图测试一下，看能不能导入到UE。

![image-20211213224632795](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112132246555.png)

看一眼，没有什么问题。

![image-20211219155906024](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191559636.png)

提示：如果你导入的并不是你的地形的话，像这样：

![image-20211219160015773](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191600682.png)

那么我建议你用object merge节点，像这样：

![image-20211219160056744](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112191600575.png)

最后再stash，再save node，再到UE里面重新important和rebuild应该就可以了。
