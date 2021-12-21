# 【Houdini 地形制作】 09_地形制作实践_基础地形

这是我们最终要做的地形，现在我们做它的基础地形。

![image-20211212154255997](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112121542414.png)

# 基础地形

## 画个山

先画个山。

![image-20211220203555978](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202035648.png)

![image-20211220203612643](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202036814.png)

## 做个坑

再做个坑。

![image-20211220203820446](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202038879.png)

注意这一步combine method选minimum是比较好的。

![image-20211220203911993](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202039923.png)

给这个坑留一层Mask，后面好处理：

![image-20211220204038252](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202040888.png)

## 给地形一些小noise

下面给地形搞一些小凸起，凹凸不平的这种效果。

![image-20211220204615026](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202046077.png)

我们用height layer来做。

参数一：基础地形。就用上一步的那个地形。

参数二：要拷贝的地形。先把基础地形打平，然后再noise一下，最后，再裁剪下半部分，因为我们需要地形noise之后的上半部分。

参数三：要作用到哪个mask上。由于我们希望这个mask会影响到我们的坑，所以要用第一步paint我们自己画的mask来减去这个坑的mask。

### 参数二：要拷贝的地形

![image-20211220205227509](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202052979.png)

![image-20211220205715509](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202057599.png)

![image-20211220205834134](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202058245.png)

### 参数三：要作用到哪个mask上

![image-20211220205004658](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202050727.png)

注意，这一步是一个习惯性做法，把mask限制到0~1以内。如果不是在这个区间以内的话后面会达不到预期的效果，后面运算的值是错误的。

我们可以看一下如果直接连接会是什么情况：

![image-20211220210135065](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202101949.png)

感受一下：

![image-20211220210248181](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202102540.png)

![image-20211220210319985](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202103037.png)

所以我们还是一样的，把mask限制到0~1以内，并且把多的值去掉。

![image-20211220210410089](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202104061.png)

最后用layer节点拷过去就行：

![image-20211220204615026](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202106259.png)

## 保持Mask整洁

对mask进行操作以后尽量用clear mask来清理一下mask，让mask保持「整洁」，不要影响我们接下来的操作，这是一个好习惯。

![image-20211220214056399](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202140407.png)

## 给山润滑

然后就可以用disort节点给山润滑一下，看你心情：

![ssdddddddd](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202109521.gif)

## 给地形加点粗糙度

我觉得地形有点平，可以叠一个 Sparse Convolution罩波，再加一点粗糙度。

![image-20211220211141369](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202111572.png)

![image-20211220211218561](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202112739.png)

## 用erode节点给地形加点细节

![image-20211220211755665](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202117131.png)

![image-20211220212103459](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202121371.png)

然后你会发现有这种缝隙：

![image-20211220212147197](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202121594.png)

这种缝隙说过的，很丑，人也不能在上面跑，会在UE里面表现为一个很深的沟壑，所以我们处理一下。

用两个Blur来处理，第一个Blur是Blur Mask，第二个Blur是Blur地形。

先把水的这一层的副本Mask层（前面我们已经把Water层拷贝为Mask层了，所以这里操作Mask层实际上就是在操作Water的副本）平滑一下，然后再用平滑过后的Mask对地形进行平滑。

![image-20211220212655657](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202126858.png)

![image-20211220212709994](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202127138.png)

 一样的，不要忘记清理一下mask，保持mask整洁。

![image-20211220214205732](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202142899.png)

注意在Blur地形的时候一定要勾上mask aware blur，不然会出现这种小凸起：

![image-20211220213049765](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202130655.png)

但是在处理的时候我们要注意我们只是处理这种沟壑，不要把山影响了，所以我们需要针对Water层来处理。如果不针对Water层来处理的话整个地形又会变得很平滑了，这不是我们想要的：

![image-20211220212930705](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202129668.png)

## 做一些平地

整个地形还是需要一些平地的，不可能全是凹凸和山脉。

我们用clip直接打屏一些地形，就用减去的圆圈的这个mask吧：

![image-20211220205004658](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202050727.png)

注意我们只想打平底地形，不要影响山，不能把山一起削平了。

![image-20211220213842363](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202138257.png)

## 用disort打乱一下山脉

由于这个算法的原因，我们操作以后这个山会表现的特别「硬」，很有规律，这看起来不是很理想，有点假：

![image-20211220214554944](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202145464.png)

用disort节点稍微打乱一下就要好很多。

![image-20211220214619240](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202146369.png)

注意我们是针对山坡，不要影响其他地方，所以我们用maskbyfeature把山坡筛选出来，筛选的条件是坡度在20~70之间：

![image-20211220214756571](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202147622.png)

## 画个河

下面我们搞个河流，直接用cruve节点画河流，然后用Sweep扫描出来，最后用height project或者height mask by object把河流添加到地形上即可。

### curve+sweep弄出河流

用cruve来控制河的主体形状，用sweep来控制河的深度宽度。

![image-20211220215408052](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202154998.png)

扫描一下，我们这里就不用几何体了，直接把Col设置为2打平河流。

![image-20211220221059848](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202211855.png)

当然你要是非要用几何体来扫描也不是不行。

注意用几何体来扫描的话，河流的宽度和深度就是由几何体的size决定的，不再由Sweep决定了。

![image-20211220221338423](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202213614.png)

### 把河流加到地形上面

有两种方法，一种是直接用project把河流拷到地形上，另一种是用maskbyobject先把河流的遮罩选出来，然后再用remap把地形映射一下，映射的输出值调低一点就行了。

第一种方式：

![image-20211220221813970](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202218436.png)

第二种方式：

![image-20211220222016021](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202220151.png)

### 留下河的材质层

保存下河的材质层，后面可能会要用，比如说在UE里面单独给河流一个材质。

![image-20211220223953693](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202239573.png)

### 平滑一下河岸两边

这个河岸看起来不太友好，我们处理一下。

![image-20211220222354670](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202223748.png)

处理的方式是和前面「用erode节点给地形加点细节」的内容一样的，即先取出河的mask，对此mask进行blur，然后再依据blur后的mask对地形进行blur。

![image-20211220222745611](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202227627.png)

注意这里remap把mask的值打成了0~1：

![image-20211220222823281](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202228482.png)

如果你重新计算的话会发现它的值并不是在0~1之间的：

![image-20211220222901316](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202229417.png)

如果直接使用这个值的话就达不到我们想要的效果：

![image-20211220223009627](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202230451.png)

最后在blur一下河岸，效果就差不多了。

![image-20211220223124242](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202231419.png)

### 弯曲一下河流细节

一样的用disort弯曲一下河流，跟「用disort打乱一下山脉」的用法差不多，注意要先blur一下mask。

![image-20211220223319296](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202233493.png)

### 保留River层

同样的是留下层级以备不时之需，我们就新起一个层river。

![image-20211220224136973](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202241131.png)

![image-20211220224446028](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202244114.png)

随手maskclear一下，保持好习惯：

![image-20211220224212208](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112202242449.png)

到这一步就基本地形就差不多了，下一步提高精度再雕刻其余部分。

# 再加细节

## 稍微雕刻

你要是觉得现在的场景还是比较糊的话可以再加点细节。

![image-20211221100341148](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112211003448.png)

河流再雕刻：

![image-20211221102556302](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112211025061.png)

地形再雕刻：

![image-20211221210323532](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212103049.png)

好习惯不能丢：

![image-20211221210504581](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212105804.png)

就是你想做什么效果都可以自己慢慢做，做到自己满意为止。

比如我想把这个干掉，这玩意儿特别碍事。

![image-20211221210738837](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212107461.png)

![image-20211221210915151](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212109086.png)

# 完成地形

你要是想在Blur一下的话可以用flowfield把flow层选出来在blur，这个节点更丰富，最后再加上一个NULL来标志一下，我们这个地形差不多就基本做完了。

![image-20211221211046110](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202112212110647.png)

当然这只是个简单的地形，你可以对你的地形进行精雕细琢做好看做丰富。