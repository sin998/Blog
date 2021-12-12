[TOC]

# 参考资料&原文链接



# 【Houdini 地形制作】 01_制作流程基本介绍

## 近景

近景一般是玩家可游玩区域，地形精度较高，材质也会比较精细，除非游戏设置哪些地方不能去，一般情况下大部分地区都可以探索游玩。

## 远景 

远处的山脉，玩家不可达，一般情况下为了游戏性能，不会将远处地形的精度和材质给到太高。

# 地理知识与制作地形的联系

## 地理正确

做地形时总结了一些经验，比如**地理正确**，这一点非常重要。关于**侵蚀和河流形成**得做点功课，尤其是做河的时候，我们必须要了解真实世界中，地形是如何形成的。

学习一些**地理知识**，需要知道河流的生成往往伴随着河谷的生成，河谷分阶，河岸的泥土会一层一层的向下游冲刷，会生成凹岸和凸岸，同时河水一层一层向下冲刷，会形成山坡，山谷，悬崖，如果是按照这个思路去做地形的话，就可以不用花精力去研究面片穿插的问题了。

![image-20211212151717339](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121517294.png)

![image-20211212151724736](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121517794.png)

![image-20211212151731869](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121517796.png)

## 场景比例（山脉大小、河流宽度）

在游戏中，地形高度一般为200-400米的样子，个别较高的山地会有700米的高山。

当然我们这个是为了对标毕棚沟的山脉，创建的是3000米的高山，但其实游戏中没有这么高的山，如果想增加一些地表的起伏，这个起伏高度最好控制在20米到40米。

这样的高度既不会阻挡游戏视野，也不会让场景看起来很突兀，同时根据山的类型我们还需要把控山的占地面积以及山与山之间的距离，这些都是场景的比例问题。

河流宽度，一般小溪宽度在8-15米左右，深度1米左右，运河宽度百米上下，这个数据真实世界和游戏世界一样，河岸坡度一般是划入河流。

并且游戏地形和真实世界对比来看，场景往往有缩放，我们也是研究了很久比例缩放的问题，需要一些制作经验。

## 侵蚀效果

只要是玩过houdini erode节点，都会觉得houdini中的侵蚀节点很难用，然后开始在网上找教程，然后发现大佬们的教程要么就是看了等于白看，要么适用于影视，要么不好看，这里我会根据侵蚀算法带大家理解erode侵蚀效果。

houdini中的侵蚀算法有两套，水力侵蚀和热力侵蚀：

**水力侵蚀**是获取地形网格上当前cell和周围cell的高度差，去计算出水量和入水量，然后根据流水的速度及坡度计算了当前cell的最大沉积量sediment，来决定是否沉积，当然还会考虑水的蒸发，是一个比较复杂的算法，水力侵蚀跟地形精度有非常大的关系。

**一般情况下推荐在低精度地形上使用水力侵蚀去产生沟壑**。

**热力侵蚀**，一般可以描述为滑坡，当地形角度大于我们所规定角度时，产生滑移slippage，相比于水力侵蚀，热力侵蚀更容易掌控。

**可以在地形精度提升之后单独使用热力侵蚀，往往能产生比较好的效果，配合mask体验更佳。**

![image-20211212152137204](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121521955.png)

# 地貌类型概况

由于地貌的多样性，不同国家，不同研究所，都有不同的规则，比如说我们国家常常说的丹霞地貌，喀斯特地貌，而在别的国家或许又是另一套说辞

 我国初中的地理书一般将地貌分为平原，高原，盆地，山地，谷地，丘陵几类。

而美国的规则又是四大类六小类，十分混乱

因此这里我只是简单的说一下常见的地形，包括沙漠，丘陵，月球表面，峡谷，山脉等等，并且在后续的课程上会讲解一下制作各种地形的思路和方法。

# 游戏中地形系统的原理

## 主要原理

主要记录的是体素在y方向轴的偏移值，然后根据摄像机的角度和距离实时计算出山体的形状，这一点大家可以测试在不同角度看同一山脉时，山脉形状的变化。

ue和houdini都是这种情况，ue是离得远会变化，houdini是离得近变化，houdini中尤其是做河水和做道路，会比较难判别生成的是否正确。

这样做的好处是能大幅度减少地形的数据体量，但是这样的弊端同样明显，只保留了y方向，所以单地形系统并不能做到一些类似山洞的地形，不过我们可以用堆叠模型的方式做出山洞的效果。

在houdini中，所有数据信息保存在prim级别，相较于houdini的其他模块，数值不可见，但我们也有办法让其可视化。

## 体积占用参考

在Houdini中直接输出一个500*500的地形：

![image-20211212152448727](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121524690.png)

给地形加上一些Mask：

![image-20211212152512466](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121525361.png)

但是将地形转化为Polygon之后体积将会大增：

![image-20211212152529329](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121525851.png)

# Houdini HeightField族

在地形制作过程中我们会用到非常多的节点，每个节点只要理解了都如同小工具一般非常简单，并且其实有很多节点只是改了一个参数就变成了另一个节点。

红色的节点是使用频率最高的节点，黄色节点是使用频率次之，节点名字前面打星号的属于比较难的节点，其他大部分节点就当做ps来用即可。

这里还要补一句，右下角的这些噪声同样重要，每一个都有可能用上，不容轻视，之后的课程我会专门讲一下噪声，不管是做地形，还是做mask，还是做材质过渡，还是做河流，做道路，都需要噪波。

除了这些节点，也有一些额外的节点需要学习。

![image-20211212151118982](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121511358.png)

第一排：

HeightField
HeightField Blur
HeightField Clip
HeightField Copy Layer
HeightField Crop
HeightField Cutout by Object
HeightField Cutout Clear
HeightField Distort by Layer
HeightField Distort by Noise
HeightField Draw Mask
*HeightField Erode
HeightField Erode Hydro
HeightField Erode
Precipitation
HeightField Erode Thermal
HeightField File

第二排：

HeightField Flow Field
HeightField Isolate Layer
HeightField Layer
HeightField Layer Clear
HeightField Layer Property
HeightField Mask Blur
HeightField Mask by Feature
HeightField Mask by Object
HeightField Mask by
Occlusion
HeightField Mask Clear
HeightField
Mask Expand
HeightField Mask Noise
HeightField
Mask Shrink
HeightField Noise
HeightField Output

第三排：

HeightField Paint
HeightField Patch
*HeightField Pattern
HeightField Project
HeightField Quick Shade
HeightField Remap
HeightField Resample
HeightField Scatter
HeightField Slump
HeightField Terrace
HeightField Tile Splice
HeightField Tile Split
HeightField Transform
HeightField Visualize
*HeightField VOP
*HeightField Wrangle

# Houdini制作地形全流程介绍

## 制作流程Overview

![image-20211212152619389](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121526657.png)

先定义地形大小，再讨论决定哪里是山哪里是河流，再制作河流，河流制作好了地形的全貌就有了。

mask和材质同步进行，需要配合。mask就是类似于PS里面的遮罩层，通过这些遮罩层来控制哪些地方长草，哪些地方长花，哪些地方是河岸/悬崖等等。而材质就是为这些mask提供支持的。

再做植被，最后做道路。也可以一起做。

## 制作地形

![image-20211212153352699](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121533796.png)

Massing Model属于纯地形规划，比如哪里山谷，哪里山峰，哪里河道，哪里做风格化效果，哪里盖房子，哪里做梯田啥的，像画画一样，先画个型。

这个阶段的话heightfield的精度极低（例如500*500），主要是塑形。这个流程从massing model到lobing循环往复，是一个需要花费巨量时间细调的过程，主要输出是一个地形的基本版型。

![image-20211212153638971](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121536883.png)

然后再进行精确制作。比如要做成什么地形，什么地貌，再做道路和植被。

![image-20211212153816450](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121538235.png)

![image-20211212154255997](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121542414.png)

## 制作Mask

制作mask也是一块比较大的内容，mask不仅应用在最后的分层上材质，也应用在生成地形的阶段，是贯穿整个地形制作的核心。

比如：定义40-70°的地方是悬崖，那么是悬崖的地方就要标红。

一般来说需要制作很多的各种各样的Mask。

![image-20211212154133196](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121541981.png)

![image-20211212154138094](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121541607.png)



## 制作河流

![image-20211212154332166](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121543299.png)

## 制作植被分布

植被分布是基于mask，同时每种植物的高矮，旋转，种类，相互作用都是可控的，这个系统的话骞老师研发了一套比较完整的hda，可以直接拿来用。

## 制作道路

道路系统还在研发当中，一般是我们直接出工具供大家使用，非常推荐大家直接在houdini中使用，非常好用，图中是一些比较简单的效果。

![image-20211212154441238](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121544371.png)

# Houdini对比传统软件的优势和劣势

![image-20211212154557027](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121545996.png)

使用传统软件WM制作地形需要在WM中BAKE出map后，导入到ue4引擎里会有以下几个问题：

- WM里预览的地形生成效果跟DCC软件或游戏引擎相比并不美观，也不能像其他工具里直接由美术参与修改。
- WM导入到引擎需要通过导出Heightmap再导入引擎，当地形比较大时，map生成和写入到硬盘的时间会超过10分钟，对美术迭代修改的成本非常大。
- WM生成后到引擎后，关卡设计还需要在地形上进行二次开发，从而影响到地形和地表的Map，这时如果需要WM生成部分地形时，整个流程会变的非常混乱。

![image-20211212154701861](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121547750.png)

使用传统软件WM制作地形将所有层的信息保存在layer中，全部递交给游戏引擎，同时layer信息还附带各种其他信息。比如instance和地形，植被，道路，河流，地形分块，都可以在houdini中制作完后一并导入到ue里。

但是houdini不像那些传统软件能很快的生成一个预览，而是需要对地形进行精雕细琢，很适合流程上的修改。

# 练习中的注意事项

虽然说ue和houdini都支持8k的精度，但是当地形带上mask信息之后hda所存的数据会很快超过1G，这个时候保存的hda会打不开，并且ue也会崩溃，所以基本上都是用4k的分辨率。

## 内存警告

一般情况下，64g对应4k-8k地形，32g对应2k-4k地形，16g，对应1k-2k地形，我们做练习的话一般最终效果呈现到2k就够了，至于细节，会在之后的教材中提到

## 运算时间

需要一个强大的cpu，cpu越强解算时间越短，当然我们也有不少办法去减少解算时间，同时保留解算效果，有些节点电脑性能不太好的同学最好降精度去做练习。

## 地形大小要求

地形的大小要求在ue的官方文档是有明确说明的，之后的教程也会给大家讲讲怎么限定大小。

