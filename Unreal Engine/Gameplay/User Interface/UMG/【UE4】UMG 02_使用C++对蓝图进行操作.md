[TOC]

# 【UE4】UMG 02_使用C++对蓝图进行操作

# 参考资料&原文链接

[虚幻官方文档 - UMG 最佳实践](https://www.unrealengine.com/ja/tech-blog/umg-best-practices?mkt_tok=eyJpIjoiTXpnNE56WXdZVEV5WlRGaSIsInQiOiJtaFBFYWdNRDRxMU05aHVjc2hHcVwvWG1FVk5rQUZrWmdLYlNmUzltbUxrT3lHekJYNlI2SnVqUkZTY0NQS1k5OW12TDdHaHhzam1pVzJ0MFdKTlIyc0pDTTJ2MGpcL0NyXC9vXC9SWlV5Qmw5NlNFSHdHMStWbDFpTXBDOGNUSlJQOEgifQ%3D%3D&lang=ja)

[开发工具的选择](https://www.cnblogs.com/sin998/p/15390468.html)

[C语言中文网-C++如何防止头文件被重复引入（3种方法）？](http://c.biancheng.net/view/vip_7676.html)

[虚幻官方文档-虚幻编译工具IWYU](https://docs.unrealengine.com/4.27/zh-CN/ProductionPipelines/BuildTools/UnrealBuildTool/IWYU/)

[架狙只打脚-[UE4官方直播学习记录]虚幻C++进阶之路|大钊（二）](https://zhuanlan.zhihu.com/p/115750451)

[虚幻官方文档-游戏性类](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/)

[UE项目目录、资源、代码规范及管理](https://www.cnblogs.com/sin998/p/15390854.html)

[腾讯游戏学院-深入理解UE4宏定义—GENERATED_BODY](https://gameinstitute.qq.com/community/detail/114465)

# 类结构

下图来自[虚幻官方文档 - UMG 最佳实践](https://www.unrealengine.com/ja/tech-blog/umg-best-practices?mkt_tok=eyJpIjoiTXpnNE56WXdZVEV5WlRGaSIsInQiOiJtaFBFYWdNRDRxMU05aHVjc2hHcVwvWG1FVk5rQUZrWmdLYlNmUzltbUxrT3lHekJYNlI2SnVqUkZTY0NQS1k5OW12TDdHaHhzam1pVzJ0MFdKTlIyc0pDTTJ2MGpcL0NyXC9vXC9SWlV5Qmw5NlNFSHdHMStWbDFpTXBDOGNUSlJQOEgifQ%3D%3D&lang=ja)。

通常，我们推荐遵循以下模式的架构：

![Unreal+Engine_tech-blog_umg-best-practices_TechBlog_UMG-Best-Practed_Blog-body-524x215-05b9bf4b4413ae4e6d4924da8267d3f87048bf2f](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110301022151.png)

> ### **UMyData**是一个继承自 UObject 的 C++ 类。
>
> 创建一个数据类来封装 UI 试图传达给用户的所有信息。
> 作为一个 UObject，它具有公共/私有访问控制，具有 getter 和 setter，并包含一个方便的 API。
>
> 这种方法将数据的生命周期与 UI 分开。这是非常可取的。所有数据源，例如商店报价、库存项目和玩家统计数据，很少与 UI 对象共享生命周期。此外，多个小部件可以从同一个数据对象中检索数据。这些类可能已经创建并存在于为不同领域的工程师创建游戏或商店后端的过程中。但是，即使在这种情况下，UI 通常也需要自己的数据类来将游戏玩法数据与大量特定于 UI 的数据结合起来。
>
> ### **UMyWidget**是一个继承自 UUserWidget 的 C++ 类。
>
> 这些 C++ 类定义了特定于小部件的 API，用于蓝图和蓝图，用于定义蓝图必须遵循的契约，以便与底层系统正确交互可用事件。
>
> ### **MyBlueprint**是一个衍生自 UMyWidget 的小部件蓝图。
>
> 在该小部件蓝图中，创建和布置您需要的所有可见 UI，应用样式，利用 UMyData 和 UMyWidget 提供的 API，以及所有 UI 基元（文本框、图像等）。输入所需的数据。它还侦听 UMyWidget 事件以了解何时更新相应的 UI。如果是交互式的，它还可以调用 UMyData 或 UMyWidget 提供的 API 以响应按钮单击。

# 新建文件

这里假设您已经会新建项目了，注意新建的时候选择新建C++项目而不是蓝图项目：

![image-20211007205306800](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/NZzCkurjR92tSPE.png)

项目建好以后会自动打开本地的编辑器，这个编辑器可以修改，这在[开发工具的选择](https://www.cnblogs.com/sin998/p/15390468.html)中介绍过，我用的是Rider，所以会自动打开Rider，静待Rider加载完成。

## 新建C++文件

按照如下步骤新建C++文件。

右键新建：

![image-20211007205836431](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072246619.png)

您可能注意到了，我们只能新建C++文件，不能新建文件夹。这个是为什么呢，没错，我也不知道为什么要这么限制- -。盲猜是因为UE不希望用户随便乱动文件夹，毕竟有这么多的互相引用，文件夹一变引用就都找不到了- -？。

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

   - CoreMinimal.h。使用IWYU来加快编译。详情可以[点击这里前往了解细节](https://docs.unrealengine.com/4.27/zh-CN/ProductionPipelines/BuildTools/UnrealBuildTool/IWYU/)。以下文字摘自官方文档：

     > 在旧版本的虚幻引擎4（UE4）中，引擎的大部分功能通过大型、以模块为中心的头文件（如 `Engine.h` 和 `UnrealEd.h`）进行包含。通过预编译头文件（PCH）快速编译这些文件即可达成较快的编译时间。但随着引擎的更新，这成为了一个瓶颈。
     >
     > 通过IWYU，每个文件只包括其需要的内容。我们选择使用的所有PCH文件纯粹只是作为基础源文件之上的优化层。可对它们进行修改，将编译时间缩至最短。其独立于源文件本身的修改，不会影响代码是否成功编译。

   - Blueprint/UserWidget.h。我们继承的父类。UserWidget在Blueprint下面，即：在`UE4\Source\Runtime\UMG\Public\Blueprint\`下面。

   - xx.generated.h。这里的「xx」代表的就是您的文件名，在这里就是「TestMain」，它应用在UE4的反射系统，**并且只能最后一个引入，即这句必须是所有#include的最后一个，否则报错找不到GENERATED_BODY。**以下内容摘自[架狙只打脚-[UE4官方直播学习记录]虚幻C++进阶之路|大钊（二）](https://zhuanlan.zhihu.com/p/115750451)。

     > 当一个头文件中include了“xxx.generated.h“，意味着这个头文件加入了反射系统。那些UPROPERTY,UFUNCTION之类的宏，也标记着这些方法属性等等加入了UE4的反射系统，**加入了反射系统，UE4才能帮您做GC（垃圾回收），您才能实现蓝图C++通信等等很多功能。**

4. 类注释。类注释一般是多行，内容较多，概要描述该类的功能、注意事项、作者、版本号和时间等。给类添加注释是一个好习惯。

   常用的标签有：`@description`、`@author`、`@version`、`@data`、`@time`等。

   除了类注释还有方法注释，与类注释相似，是用来对方法进行注释的，请自行使用搜索引擎了解详情。

   编辑器可以自定义类注释和方法注释，即类或者方法一创建好了就自动添加这些标签。

   类注释和方法注释可以配合Javadoc就可以从程序源代码中抽取类、方法、成员等注释形成一个和源代码配套的API帮助文档。当有新增或者修改的类、方法、成员等就可以很方便查询API和维护项目。有机会单独开篇文章聊一下。

5. 类说明。详情参见[虚幻官方文档-游戏性类](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/)。

   > 类声明定义类的名称、其继承的类，以及其继承的函数和变量。类声明还将定义通过 [虚幻官方文档-类说明符](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/#类说明符) 和元数据要求的其他引擎和编辑器特定行为。
   >
   > 声明包含一个类的标准 C++ 类声明。在标准声明之上，描述符（如类说明符和元数据）将被传递到 `UCLASS` 宏。它们用于创建被声明类的 `UClass`，它可被看作引擎对类的专有表达。此外，`GENERATED_BODY()` 宏必须被放置在类体的最前方。

6. 蓝图的继承个数。这个功能Rider专属，它可以很方便的知道这个类被哪些蓝图继承，我们这里才刚建起来，所以还没有蓝图继承呢。

7. 这个主要用于UE的模块化插件开发。试想一下你写了个插件，你应该如何让别人使用呢？使用这个东西就可以把接口暴露出去给别人使用，我这个项目叫做TestProject，所以这里就是TESTPROJECT_API。举个例子，插件OpenColorIO的API长这样：

   ![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110101628797.png)

   ![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110101628541.png)

8. 我们自己创建的类名。注意我们创建的时候填写的文件名是「TestMainUI」，而类名是「UTestMainUI」。这是虚幻自动帮我们添加的，这是我们必须遵守的[UE项目目录、资源、代码规范及管理](https://www.cnblogs.com/sin998/p/15390854.html)，这表示我们这个类的最终的父类是继承的UObject。

   如果是在Rider中没有遵守UE命名规范的话编译器会报错，通不过编译。例如我这里手动把U去掉并且不理会Rider的提示：

   ![image-20211007220149588](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248742.png)

   强行锤一手：

   ![image-20211007220306049](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248599.png)

   这里的编译报错很清晰：

   ![image-20211007220500379](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248514.png)

9. 我们继承的父类：UUserWidget。所有自定义的UMG蓝图的父类的C++都必须继承自UUserWidget。

   注意：同样的，「UUserWidget」是类名，「UserWidget」是文件名，这里再次体现了UE命名规范的严格。

10. 大致介绍过了。这里不多作介绍，想了解更多点这里：[腾讯游戏学院-深入理解UE4宏定义—GENERATED_BODY](https://gameinstitute.qq.com/community/detail/114465)。

### CPP文件介绍

CPP文件很简单，只是引入了头文件而已。

![image-20211007211514303](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248117.png)

 不过这里要注意的是`#include "UI/TestMainUI.h"`这一行其实是不行的，不能够正确的引入头文件，编译的话反而还会报错：

![image-20211007225112264](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072251605.png)

这个地方应该这样换成：`#include "TestMainUI.h"`之后就不会报错了。

再次看一眼新建的时候的文件目录树：

![image-20211007211016464](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110072248224.png)

所以编辑器的初心是好的，只不过没弄对位置- -。

我们这个头文件和CPP文件在同一个文件夹下面，所以不需要加上「UI」，直接用相对路径就行。

## 新建蓝图文件并选择父类

接下来就该新建蓝图了，一样的，注意分门别类。现在我的目录树是：

Content
 └── UI
     ├── Images
     │   ├── InterestingUE4.uasset
     │   └── InterestingUE4Mapping.uasset
     └── WidgetBlueprint
         └── WB_TestMainUI.uasset

我将这个蓝图命名为「WB_TestMainUI」，这个也在[UE项目目录、资源、代码规范及管理](https://www.cnblogs.com/sin998/p/15390854.html)中讲到过，「WB」是「WidgetBlueprint」的缩写，后面跟上一个下划线，再跟上具体的UI名。

然后重选「TestMainUI」为我们蓝图的父类：

![image-20211008222644744](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110082226120.png)

# 在蓝图中添加控件并重命名

![image-20211008215906194](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110082159566.png)

从简单的开始，这是我的布局，我对我所需要的控件进行了重命名。

我想实现的功能很简单，单击图中的按钮`Btn_ChangeTab`，我希望`WS_TabContent`能在`Image_UE4`和`Image_UE4Mapping`中来回切换。

# 获得蓝图中的控件

接下来我们就可以在蓝图中获取他们并进行绑定了，编辑TestMainUI.h：

````````c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "Blueprint/UserWidget.h"
#include "TestMainUI.generated.h"

/**
 * 主UI。
 */
UCLASS()
class TESTPROJECT_API UTestMainUI : public UUserWidget
{
	GENERATED_BODY()

	/*
	* 重写我们需要的UMG的生命周期方法。注意灵活使用编辑器快捷重写所需要的父类方法。
	*/
protected:
	//UMG构造时调用
	virtual void NativeConstruct() override;
	//UMG销毁时调用
	virtual void NativeDestruct() override;
	//UMG的Tick
	virtual void NativeTick(const FGeometry& MyGeometry, float InDeltaTime) override;

	/*
	 * 注意要用public修饰，用private的话会提示您：Access specifier does not change accessibility level.
	 * 这很简单，UE的反射系统要帮您绑定控件，那么如果设置为private的话UE将访问不到，这时UE将会自动忽略private。
	 */
public:

	/*
	 * 使用UPROPERTY宏，里面的元数据说明符（meta）设置为BindWidget。
	 * UE就会帮助我们拿到目标控件的指针。
	 * 注意这里的控件类型控件名都要和蓝图中的控件类型与控件名严格一一对应。
	 * 如果不严格一一对应的话蓝图将会报错，例如：A required widget binding"Btn ChangeTab" of type OButton was not found. 
	 * 如果没有用BindWidget的话你想要得到蓝图中的控件你得在Initialize()中这么写：
	 * WS_TabContent = Cast<UWidgetSwitcher>(GetWidgetFromName(TEXT("WS_TabContent")));
	 * 其中Button_Start是蓝图中Button的名字。可见BindWidget帮了我们省了好多事，代码好看又轻便起来。
	 */
	UPROPERTY(Meta = (BindWidget))
	class UWidgetSwitcher* WS_TabContent;
	
	UPROPERTY(Meta = (BindWidget))
	class UImage* Image_UE4;
	
	UPROPERTY(Meta = (BindWidget))
	class UImage* Image_UE4Mapping;

	UPROPERTY(Meta = (BindWidget))
	class UButton* Btn_ChangeTab;
	
public:
	/*
	 * Btn_ChangeTab的点击函数。
	 * 必须要有UFUNCTION函数描述符，否则后面绑定函数的时候绑定将会报错：Unable to bind delegate to 'OnBtnClick_ChangeTab' (function might not be marked as a UFUNCTION or object may be pending kill)。
	 * BlueprintCallable描述符表示蓝图可调用。
	 */
	UFUNCTION(BlueprintCallable)
	void OnBtnClick_ChangeTab();

private:
	
	//WS_TabContent当前页面ActiveIndex
	int8 CurrWSIndex = 0;
};

````````

这几行代码很简单，相信您看结合代码中的注释和下面的文章就能轻松看懂：

[虚幻官方文档-元数据说明符](https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/GameplayArchitecture/Metadata/)、[虚幻官方文档-UMWidget::UPROPERTY 宏的有效元数据关键字](https://docs.unrealengine.com/4.27/en-US/API/Runtime/UMG/Components/UMWidget_1/)、[林清的博客-UE4笔记-UMG和Slate记录](https://www.cnblogs.com/linqing/p/9839355.html)。

其中需要注意的是：

- 需要重写需要的父类函数。这个会在后面讲解UMG生命周期和搭建UI框架的时候单独讲解。
- 除了`BindWidget`用来绑定控件外，还有一个常用的是`BindWidgetAnim`，即绑定动画。
- 绑定的类型必须是控件的指针，不能是控件。
- 如果控件需要绑定函数，该函数必须加上`UFUNCTION()`宏。例如Button需要绑定OnClick、CheckBox需要绑定OnCheckStateChanged等时。

# 操作蓝图中的控件

``````c++
// Fill out your copyright notice in the Description page of Project Settings.

#include "TestMainUI.h"
#include "Components/Button.h"
#include "Components/WidgetSwitcher.h"

void UTestMainUI::NativeConstruct()
{
	Super::NativeConstruct();
	/*
	 * 绑定Btn_ChangeTab的点击事件，Btn_ChangeTab的点击都由OnBtnClick_ChangeTab处理。
	 * 这里传递一个蓝图对象和一个函数原型为void FuncName()的方法。
	 * 这个方法的实质就是用的委托实现的。
	 * 可以按住ctrl再点AddDynamic或者点击AddDynamic再按下F12查看方法。
	 */
	Btn_ChangeTab->OnClicked.AddDynamic(this,&UTestMainUI::OnBtnClick_ChangeTab);
}

void UTestMainUI::NativeDestruct()
{
	Super::NativeDestruct();
	//UMG销毁时必须解绑，不然会造成内存泄漏。
	Btn_ChangeTab->OnClicked.RemoveDynamic(this,&UTestMainUI::OnBtnClick_ChangeTab);
}

void UTestMainUI::NativeTick(const FGeometry& MovieSceneBlends, float InDeltaTime)
{
	//Tick我们暂时没用到，一般来说只有更新进度条或者需要高度实时刷新界面的数据才会考虑到Tick中编写对应逻辑。
	Super::NativeTick(MovieSceneBlends, InDeltaTime);
}

void UTestMainUI::OnBtnClick_ChangeTab()
{
	/*
	 * 如果对WidgetSwitcher还有印象的话就会很容易明白下面的代码在干什么。
	 * 这个WS_TabContent的ActiveIndex的就是放置的子控件的顺序。
	 * 这个Index必须严格对应，否则可能得不到期望效果。
	 */
	switch (CurrWSIndex)
	{
		case 0:
			WS_TabContent->SetActiveWidgetIndex(1);
			CurrWSIndex = 1;
			break;
		case 1:
			WS_TabContent->SetActiveWidgetIndex(0);
			CurrWSIndex = 0;
			break;
		default:
			WS_TabContent->SetActiveWidgetIndex(0);
			break;
	}
}

``````

这个注释写的比较详尽，代码也比较简单，就不再啰嗦什么了。

# 最终效果

最后我们在关卡蓝图中把这个UI显示出来：

![image-20211008233122931](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110082331749.png)

保存关卡，点击运行，这就是最终的效果：

![动画adasda](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110082334789.gif)

# 总结

本文介绍了如何创建自定义蓝图界面，并且认识了UE4基本的C++代码框架，最后还实现了一个小功能。

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 用户界面`、`UE4 UMG`、`UMG基础`。

