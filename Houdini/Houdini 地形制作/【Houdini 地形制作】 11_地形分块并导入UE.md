# 【Houdini 地形制作】 11_地形分块并导入UE

# 打包并制作分块

先打包。

![image-20211222220723813](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222207471.png)

再制作分块：

![image-20211222220810742](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222208453.png)

# 官方指引

接下来打开官方文档：https://www.sidefx.com/docs/unreal/

找到houdini和UE关联需要的参数：https://www.sidefx.com/docs/unreal/_attributes

我们可以在这个页面找到如何将Houdini的一些属性传递到UE中，将他们对应起来。

我们做分块需要注意两个属性：

unreal_level_path和unreal_landscape_tile_actor_type。

## unreal_level_path

地形分块是把一个大地形分为很多个小地形。

在UE里面就是一个持久关卡里面有很多子关卡。

我们需要在UE里面去指定子关卡的地址。

![image-20211222221255871](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222212910.png)

决定当烘烤到演员/叶子时输出烘焙的水平。网格，实例，叶子和风景被烤到指定的水平。这对于世界组成、烘焙景观瓷砖及其相关的实例或每个瓷砖的独立层次的叶子特别有用。此属性也可用于输入(即设置为输入产生的关卡路径)。默认值是"."，它将在当前持久世界中生成几何图形，以"/"开头的路径被认为是绝对的，否则路径是相对于当前持久世界的地图。(支持字符串标记)。

翻译：我们需要传入每个子关卡的路径，这个是从UE里面复制的引用，然后加上每个子关卡的后缀名而成的，下文会演示。

## unreal_landscape_tile_actor_type

让UE识别这个地形是一个分块。

![image-20211222221805819](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222218796.png)

指示应为平铺式景观创建的景观参与者的类型，可以是单个景观参与者(0)，也可以是具有共享景观参与者(1)的景观流代理。

翻译：如果是整块地形，就将此值置为0；如果是整块中的某一小块，就将此值置为1。

## 创建属性

接下来用attributecreate节点创建这两个属性。

创建unreal_landscape_tile_actor_type：

![image-20211222222845456](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222228397.png)

创建unreal_level_path：





把它们打个包：

![image-20211222223134877](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222231181.png)







