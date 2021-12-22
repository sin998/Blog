# 【Houdini 地形制作】 10_地形制作实践_制作Mask

# 制作Mask

我们现在有很多层，可以看一下：

![image-20211221211638924](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212116152.png)

## river

![image-20211221211719226](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212117052.png)

## mesa

![image-20211221211751561](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212117420.png)

## debris

![image-20211221211839690](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212118772.png)

## sediment

![image-20211221211855381](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212118367.png)

## flow

![image-20211221211908660](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212119777.png)

## water

可以做水坑。

![image-20211221211928962](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212119124.png)

## cliff

这个层使用heightfield_maskbyfeature筛选的，坡度就是默认的20~70°，是一个经典的Cliff层。

![image-20211221211959446](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212120467.png)

## snow

这个层使用heightfield_maskbyfeature筛选的，高度就是自定义的一个高度，这里用的是75~174，可以用来放一些雪的材质。

![image-20211221212117263](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212121597.png)

# 制作mask示例

你可以用这些层来种草种树撒石头修房子之类的，注意这些都是一个一个的instance，所以在种之前一定要保证mask的值是非0即1的，0就不种，1就种。

如果不是在这两个数之中的话那么在这些mask的边缘（例如值是零点几这种很小的值的话），你可能看不到，但是instance已经种到哪里了，到时候你就很疑惑，明明我没有指定这里啊为什么还种了- -。

## 种草

比如说我要种草，一般来说草是长在平地，不会长在河里，也不会长在顶峰，所以就用mesa层减去river层。

![image-20211221212633786](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212126094.png)

注意限定mask为0或者1。

![image-20211221213006244](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212130117.png)

你也可以用remap来限定，在这之前看一下mask这个值，这些值不是我们想要的。

![image-20211221213140379](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212131306.png)

直接限制。

![image-20211221213229097](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212132855.png)

两种方法都可以，只不过第一种方法比较快速点。

当然两种方法也可以一起用，不冲突也保险。

最后你可以把这一层保存下来，注意命名规范，I表示Instance，后面表示Instance的类型。

![image-20211221214717714](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212147760.png)

还有其他的，比如材质前面就加一个「M_」。

## 种树

种树是在种草的基础上种的。

首先树的数目肯定比草少，并且草种的是比较多且密的，数就需要分散的疏密和随机一点。

所以随机的效果我们就用masknoise来做，把mask给noise一下：

![image-20211221213809176](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212138380.png)

注意这个mask现在不是非0即1的，所以我们需要把它搞成非0即1的，但是又要注意树是比草要疏的，所以我们可以这样做：

![image-20211221214110756](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212141425.png)

最后用滑块来控制种树的mask的大小：

![fff](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212143532.gif)

一样的保存一下：

![image-20211221214940436](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212149308.png)

最后一样的clear一下。

## 保存层

注意height_layer保存的时候只有连上第一个input的才会保存层，一层一层的传递下去。

![image-20211221215353177](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212153935.png)

## 河流

比如我再做一个河流的materia层，当然最后不要忘了maskclear。

![image-20211221215724692](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212157106.png)

## 做其他层

上面做河流那个是一个三件套，你做好这一个之后其他的层都可以直接拷贝就完事了，这里一共做了这几个层。

![image-20211221220159075](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212202088.png)

## 删除层

最后用一个blast把我们不需要的层删除掉，以节约存储空间。

![image-20211221220424939](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212204883.png)

删除以后：

![image-20211221220457491](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212204278.png)

## 清理层

把所有的层置为非0即1。

随便拿一个层出来看一下，例如我这里拿Debris层：

![image-20211221222314684](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212223899.png)

会发现这个层并不是非0即1的，所以我们一定要做好工作，这里我们用一个foreach循环来做这件事。

我们先查看一下值，在视口按下alt+]呼出一个面板，然后新建一个Geometry Spreadsheet面板：

![image-20211221222657492](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212226437.png)

他们依次是：

![image-20211221224933834](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212249894.png)

然后显示我们的层级，可以发现所有的layer都是保存在面层级上的。

![image-20211221222818195](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212228888.png)

先看下foreach end：

![image-20211221223231189](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212232025.png)

我们现在知道一共需要循环9次，但是如果是写死9次的话，如果我上面多一层或者少一层那么这个值就不应该是9次了，这个值应该是一个可变的，并且是随着我们这个层数的变化而变化的。

那么这个值是多少呢，前面说过我们的层都是保存在面上面的，所以如果可以取得面的个数问题就解决了。

我们可以用一个表达式「nprims」来取得某个节点的面的个数。

![image-20211221223650024](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212236141.png)

我们用prims函数来返回一个属性，处理每一个面。

![image-20211221224343837](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212243871.png)

用detail来取得面板编号。

![image-20211221225051817](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212250803.png)

所以表达式就是这样的：

```
`prims("../Out_clamp",detail("../foreach_begin2_metadata14","iteration",0),"name")`
```

参数解释：

![image-20211221225925232](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212259745.png)

foreach_begin2_metadata14里面有个iteration属性：

![image-20211221225241985](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212252725.png)

这样的话就能把Out_Clamp的每一层拿到。

然后再修改它：

![image-20211221230200584](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212302892.png)

你也可以这样修改，效果相同：

![image-20211221230219851](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212302762.png)

最后再把改变的值写回去，再clear一下就算完成了一次循环：

![image-20211221230353968](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212303121.png)

可以看一下循环一次的结果是不是非0即1：

![image-20211221230600670](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212306419.png)

挺好的，效果OK。循环完毕后用layer节点把原来的值直接覆盖掉，千万注意要连接第一个input：

![image-20211221230640802](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212306995.png)

我们可以检查一下是不是真的非0即1：

![image-20211221231144356](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212311332.png)

可以发现确实是非0即1。

![image-20211221231210878](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212312691.png)

