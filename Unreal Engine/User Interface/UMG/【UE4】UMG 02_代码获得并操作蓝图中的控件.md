[TOC]

# 【UE4】UMG 02_代码获得并操作蓝图中的控件

# 参考资料&原文链接

TODO[开发工具的选择]()

[C语言中文网-C++如何防止头文件被重复引入（3种方法）？](http://c.biancheng.net/view/vip_7676.html)

[虚幻官方文档-虚幻编译工具IWYU](https://docs.unrealengine.com/4.27/zh-CN/ProductionPipelines/BuildTools/UnrealBuildTool/IWYU/)

[架狙只打脚-[UE4官方直播学习记录]虚幻C++进阶之路|大钊（二）](https://zhuanlan.zhihu.com/p/115750451)

[虚幻官方文档-游戏性类](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/)

TODO[UE命名规范]()

[腾讯游戏学院-深入理解UE4宏定义—GENERATED_BODY](https://gameinstitute.qq.com/community/detail/114465)

# 新建文件

这里假设您已经会新建项目了，注意新建的时候选择新建C++项目而不是蓝图项目：

![image-20211007205306800](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/NZzCkurjR92tSPE.png)

项目建好以后会自动打开本地的编辑器，这个编辑器可以修改，这在TODO[开发工具的选择]()中介绍过，我用的是Rider，所以会自动打开Rider，静待Rider加载完成。

## 新建C++文件

按照如下步骤新建C++文件。

右键新建：

![image-20211007205836431](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072246619.png)

选择UserWidget为父类：

![image-20211007205949555](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072247531.png)

填写名称和路径：

![image-20211007210306618](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072247345.png)

到这一步C++就算新建完成了。等待引擎编译一下，可能热重载失败，报如下的错误：

![image-20211007210436169](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248181.png)

不要担心，这不是您的问题，这只是UE的热重载老毛病了。我们可以单击No，然后关闭引擎。

回到编辑器中，如果是VS，它就会提示您工程有变化，这时候只需要单击「Reload All」重新加载项目即可。如果是Rider，它就会自动重新加载，不用点任何提示。

这是我们新建的C++文件：

![image-20211007211016464](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248224.png)

## C++文件介绍

先瞅一眼UE4帮我们干了什么。

### 头文件介绍

先看看.h头文件：

![image-20211007211440483](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248551.png)

解释：

1. 版权声明。

2. 防止重复引入头文件。具体可以在[C语言中文网-C++如何防止头文件被重复引入（3种方法）？](http://c.biancheng.net/view/vip_7676.html)中得到答案。

3. 自动引入的头文件。

   - CoreMinimal.h。使用IWYU来加快编译。详情可以[点击这里前往了解细节](https://docs.unrealengine.com/4.27/zh-CN/ProductionPipelines/BuildTools/UnrealBuildTool/IWYU/)。

     > 在旧版本的虚幻引擎4（UE4）中，引擎的大部分功能通过大型、以模块为中心的头文件（如 `Engine.h` 和 `UnrealEd.h`）进行包含。通过预编译头文件（PCH）快速编译这些文件即可达成较快的编译时间。但随着引擎的更新，这成为了一个瓶颈。
     >
     > 通过IWYU，每个文件只包括其需要的内容。我们选择使用的所有PCH文件纯粹只是作为基础源文件之上的优化层。可对它们进行修改，将编译时间缩至最短。其独立于源文件本身的修改，不会影响代码是否成功编译。

   - Blueprint/UserWidget.h。我们继承的父类。UserWidget在Blueprint下面，即：在`UE4\Source\Runtime\UMG\Public\Blueprint\`下面。

   - xx.generated.h。这里的「xx」代表的就是你的文件名，在这里就是「TestMain」，它应用在UE4的反射系统，**并且只能最后一个引入，即这句必须是所有#include的最后一个，否则报错找不到GENERATED_BODY。**以下内容摘自[架狙只打脚-[UE4官方直播学习记录]虚幻C++进阶之路|大钊（二）](https://zhuanlan.zhihu.com/p/115750451)。

     > 当一个头文件中include了“xxx.generated.h“，意味着这个头文件加入了反射系统。那些UPROPERTY,UFUNCTION之类的宏，也标记着这些方法属性等等加入了UE4的反射系统，**加入了反射系统，UE4才能帮你做GC（垃圾回收），你才能实现蓝图C++通信等等很多功能。**

4. 类注释。类注释一般是多行，内容较多，概要描述该类的功能和注意事项。给类添加注释是一个好习惯。

   常用的标签是：`@description`、`@author`、`@time`等。

   除了类注释还有方法注释，与类注释相似，是用来对方法进行注释的，请自行使用搜索引擎了解详情。

   编辑器可以自定义类注释和方法注释，即类或者方法一创建好了就自动添加这些标签。

   类注释和方法注释可以配合Javadoc就可以从程序源代码中抽取类、方法、成员等注释形成一个和源代码配套的API帮助文档。当有新增或者修改的类、方法、成员等就可以很方便查询API和维护项目。有机会单独开篇文章聊一下。

5. 类说明。详情参见[虚幻官方文档-游戏性类](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/)。

   > 类声明定义类的名称、其继承的类，以及其继承的函数和变量。类声明还将定义通过 [类说明符](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/#类说明符) 和元数据要求的其他引擎和编辑器特定行为。
   >
   > 声明包含一个类的标准 C++ 类声明。在标准声明之上，描述符（如类说明符和元数据）将被传递到 `UCLASS` 宏。它们用于创建被声明类的 `UClass`，它可被看作引擎对类的专有表达。此外，`GENERATED_BODY()` 宏必须被放置在类体的最前方。

6. 蓝图的继承个数。这个功能Rider专属，它可以很方便的知道这个类被哪些蓝图继承，我们这里才刚建起来，所以还没有蓝图继承呢。

7. TODO

8. 我们自己创建的类名。注意我们创建的时候填写的文件名是「TestMainUI」，而类名是「UTestMainUI」。这是虚幻自动帮我们添加的，这是我们必须遵守的TODO[UE命名规范]()，这表示我们这个类的最终的父类是继承的UObject。

   如果是在Rider中没有遵守UE命名规范的话编译器会报错，通不过编译。例如我这里手动把U去掉并且不理会Rider的提示：

   ![image-20211007220149588](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248742.png)

   强行锤一手：

   ![image-20211007220306049](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248599.png)

   这里的编译报错很清晰：

   ![image-20211007220500379](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248514.png)

9. 我们继承的父类：UUserWidget。所有自定义的UMG蓝图的父类的C++都必须继承自UUserWidget。

   注意，「UUserWidget」是类名，「UserWidget」是文件名，这里再次体现了UE命名规范的严格。

10. 大致介绍过了。这里不多作介绍，想了解更多点这里：[腾讯游戏学院-深入理解UE4宏定义—GENERATED_BODY](https://gameinstitute.qq.com/community/detail/114465)。

### CPP文件介绍

CPP文件很简单，只是引入了头文件而已。

![image-20211007211514303](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248117.png)

 不过这里要注意的是`#include "UI/TestMainUI.h"`这一行其实是不行的，不能够正确的引入头文件，编译的话反而还会报错：

![image-20211007225112264](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072251605.png)

这个地方应该这样换成：`#include "TestMainUI.h"`。

再次看一眼新建的时候的文件目录树：

![image-20211007211016464](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248224.png)

所以编辑器的初心是好的，只不过没弄对位置- -。

我们这个头文件和CPP文件在同一个文件夹下面，所以不需要加上「UI」，直接用相对路径就行。

## 新建蓝图文件并选择父类



# 获得蓝图中的控件



# 操作蓝图中的控件



# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 用户界面`、`UE4 UMG`、`UMG基础`。

