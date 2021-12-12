# 【Houdini 地形制作】 03_HeightFiled常用节点（二）

# HeightField Terrace

做梯田用的。

连上以后：

![image-20211212204958076](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122049884.png)

记得要重新计算然后再调参数。

![image-20211212205059297](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122051212.png)

别忘了和Paint作为Mask配合使用。

![image-20211212205228808](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122052518.png)

# HeightField Layer Property

这个节点不常用，用来平滑地形边缘的，还不如Blur好用。

# HeightField Tile Splice

地形分块，通常和Split配合使用。

例如我现在有个地形：

![image-20211212205636480](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122056634.png)

分块以后：

![image-20211212205649999](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122056130.png)

![image-20211212205717729](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122057884.png)

这个地形被分为了row：4，col：4。所以一共被分为了16块。

默认值是第0块，所以就显示的第0块。

那我们显示第一块看看：

![image-20211212205826945](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122058876.png)

# HeightField Tile Split

地形拼接，就是把两个地形拼到一起。

我现在把地形调整为2*2块。

第一个显示第0块，第二个显示第1块，然后用merge把他们拼起来：

![image-20211212210238211](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122102001.png)

![image-20211212210300777](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122103788.png)

但是仔细看会发现的话两块地形是没有合到一起的，还是各自的边界：

![image-20211212210324323](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122103205.png)

为了效果更明显，我再将其中一个平移一点点：

![image-20211212210613756](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122106894.png)

然后再拼起来：

![image-20211212210646281](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122106278.png)

# HeightField Draw Mask

这个节点和Paint差不多，只是画的方式不太一样。

![image-20211212210909642](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122109447.png)

# HeightField Copy Layer

复制/添加层。

比如我的noise现在只有两个层。

![image-20211212211201524](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122112377.png)

然后把这个节点放上去，给层起一个名字，再次查看信息就会发现多了一个层：

![image-20211212211312871](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122113159.png)

# HeightField Mask Clear

直接将Mask这层的值置为0，并不是删除图层。

![image-20211212211813234](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122118954.png)

# Blast

删除层。

![image-20211212211929531](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122119605.png)

# HeightField Layer Clear

和Mask Clear差不多，只不过前者是Clear的Mask，后者是需要自己指定一个层来Clear。

![image-20211212212135469](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112122121188.png)











