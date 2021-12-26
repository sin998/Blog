# 【Houdini 地形制作】 11_地形分块并导入UE

# 打包并制作分块

先打包。

![image-20211222220723813](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222207471.png)

然后merge的对象就选这个「OUT_final_terrain」。

再制作分块：

![image-20211222220810742](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222208453.png)

我们需要把这个Tile Number传递到UE里面。

# 官方指引

接下来打开官方文档：https://www.sidefx.com/docs/unreal/

找到houdini和UE关联需要的参数：https://www.sidefx.com/docs/unreal/_attributes

我们可以在这个页面找到如何将Houdini的一些属性传递到UE中，将他们对应起来。

# 需要的两个属性

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

# 创建属性

接下来用attributecreate节点创建这两个属性。

创建unreal_landscape_tile_actor_type：

![image-20211222222845456](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222228397.png)

创建unreal_level_path：

因为需要关卡路径，所以先打开UE。

先创建一个关卡，复制路径：

![image-20211226152036700](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261520527.png)

比如说我复制的路径是：

`World'/Game/Map/Map_Main.Map_Main'`

那么要填入的是：

`/Game/Map/Map_Main/PDG_{tile}`

![image-20211226152344543](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261523229.png)

`PDG_{tile}`是每个地形块的名称，后面会讲到。用`{}`扩起来的是可以被UE识别的属性，这会传递到UE里面，UE就能识别了。

# 创建数字资产

把它们打个包：

![image-20211222223134877](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112222231181.png)

添加一个Output：

![image-20211226151312896](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261513318.png)

把需要的参数提上来，PDG需要：

![image-20211226151503809](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261515736.png)

一样的创建数字资产。第一个是最后输出的完整山脉。

![image-20211226165142387](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261651647.png)

第二个是我们的分块地形。

![image-20211226163620823](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261636881.png)

![image-20211226165204070](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261652533.png)

保存好以后都save一下。

# 创建topnetwork节点

注意我们这个节点是在obj层级创建的：

![image-20211226152806971](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261528782.png)

前面那些都是在geo里面做的：

![image-20211226152722539](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261527551.png)

进入节点，可以看到一个名为localscheduler的节点，这是用来控制工作流的，可以不用管它。

我们直接创建一个HDA（hdaprocessor）：

![image-20211226165336653](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261653902.png)

再创建一个wedge：

![image-20211226165709975](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261657727.png)

我们可以看一下（双击小点就能出来）：

这是第0块：

![image-20211226171543227](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261715557.png)

这是第1块：

![image-20211226171624248](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112261716793.png)

然后再搞一个hdaprocessor，这个是分块的：

![image-20211226180211961](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262131486.png)

注意参数不要忘了，这个就是上一步wedge中创建的：

![image-20211226213138299](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262131704.png)

然后退一级界面，把这个打个包：

![image-20211226213316177](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262133941.png)

直接创建一个数字资产，我把它命名为「PDG_split_terrain」。

创建的时候可能会报一个警告，这是因为我们引用了外部资产，直接点OK。

每个分块的HDA是很小的，但是如果是保存的地形那么就会很大：

![image-20211226213609474](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262136977.png)

# 导入UE

先把地形的分块打开。

![image-20211226213914864](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262139957.png)

直接把刚刚导出的那个文件甩进来，然后创建到中心，打开它的详情面板就会发现两个属性：

![image-20211226214113701](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262141653.png)

回到HE，然后在最后一个输出节点中把输出的前缀「HE_OUT_」加上，以供UE识别。

保存好之后不要忘记save。

然后回到UE。

![image-20211226214416871](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262144829.png)

在节点里面也要重新弄一下：

![image-20211226214436882](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262144805.png)

然后直接Cook：

![image-20211226214843933](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262148133.png)

这个时候就会发现地形一块块的被导入进来了，这是最后的效果。

![image-20211226215035178](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262150573.png)

# 把地形Bake到子关卡

这样的话地形已经到持久关卡了，一下子就在持久关卡也不是很理想，我们是需要按需加载的，所以要把分块地形弄到子关卡中。

最后再Bake一下，注意Bake有两种：

![image-20211226215204700](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262152589.png)

![image-20211226215236631](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262152607.png)

我们用第二种。Bake完毕之后是这样的：

![image-20211226215816401](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262158090.png)

看一眼：

![image-20211226220738431](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262207836.png)

![image-20211226220709024](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112262207189.png)

最后愉快的Save All就行了，地形分块部分到此结束。

















