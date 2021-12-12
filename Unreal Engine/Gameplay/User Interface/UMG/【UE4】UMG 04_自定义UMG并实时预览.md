[TOC]

# 【UE4】UMG 04_自定义UMG并实时预览

# 参考资料&原文链接

[UE4中UMG与C++交互 页面文本修改](https://www.cnblogs.com/lixiao24/p/9545090.html)

[【UE4】UMG_UMG生命周期](https://www.cnblogs.com/sin998/p/15490311.html)

# 简介

上节我们已经研究过UMG的生命周期了：[【UE4】UMG_UMG生命周期](https://www.cnblogs.com/sin998/p/15490311.html)，这节来试一试自己搞一个UMG出来，然后看是否能方便我们。

在项目里面风格要统一，比如按钮的大小、图片、里面的字体样式等等这些风格都要统一，而如果每次都在UMG里面拖出来然后又改的话实在是太浪费时间了，做了大量的重复劳动。

我希望有一个通用的按钮，它能够统一我们的风格，并且在替换的时候只需要修改与它相关的C++和蓝图即可，其他的都可以跟着变。

# 修改Build.cs文件

以下内容来自[UE4中UMG与C++交互 页面文本修改](https://www.cnblogs.com/lixiao24/p/9545090.html)：

在.Build.cs文件中将以下行：

```c++
PublicDependencyModuleNames.AddRange(``new` `string``[] { ``"Core"``, ``"CoreUObject"``, ``"Engine"``, ``"InputCore"` `});
```

**修改**为：

```c++
PublicDependencyModuleNames.AddRange(``new` `string``[] { ``"Core"``, ``"CoreUObject"``, ``"Engine"``, ``"InputCore"``, ``"UMG"``, ``"Slate"``, ``"SlateCore"` `});
```

修改完成后，打开和你项目同名的头文件然后添加以下包含：

```c++
#include "Runtime/UMG/Public/UMG.h"
#include "Runtime/UMG/Public/UMGStyle.h"
#include "Runtime/UMG/Public/Blueprint/UserWidget.h"
#include "Runtime/UMG/Public/Slate/SObjectWidget.h"
#include "Runtime/UMG/Public/IUMGModule.h"
```

比如我的项目名是`TestProject`，那么这个文件名就是`TestProject.Build.cs`，它一般就在源代码的根目录下：

![image-20211031183457680](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110311834935.png)

# 新建C++文件

其实新建C++文件没有这么麻烦，不用每次都开引擎然后在打开新建，我们只需要在Rider或者VS里面按照新建C++文件的步骤就行：

![image-20211031182908061](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110311829791.png)

填好文件名点OK，然后把以前的代码复制过来改个名字就完事了。比如我就直接复制另一个C++文件的头文件和CPP文件的，然后改了个名（注意遵守命名规范）：

```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "WC_CommonBtn.generated.h"

/**
* 通用按钮。
*/
UCLASS()
class TESTPROJECT_API UWC_CommonBtn : public UUserWidget
{
	GENERATED_BODY()
};
```

我给它取名为「UWC_CommonBtn」。话不多说，先上代码。

# UWC_CommonBtn.h

```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "Blueprint/UserWidget.h"
#include "Engine/Font.h"
#include "Components/Button.h"
#include "Components/TextBlock.h"
#include "Styling/SlateTypes.h"
#include "WC_CommonBtn.generated.h"

class UButton;
class UTextBlock;

/**
* 通用按钮。
*/
UCLASS()
class TESTPROJECT_API UWC_CommonBtn : public UUserWidget
{
	GENERATED_BODY()
public:
	//学一下UserWidget，把构造函数从UserWidget直接复制过来再改个名儿，拿来初始化属性。
	UWC_CommonBtn(const FObjectInitializer& ObjectInitializer);
	
public:
	UPROPERTY(Meta = (BindWidget))
	UTextBlock * Txt_Main;

	UPROPERTY(Meta = (BindWidget))
	UButton * Btn_Main;

	/*
	 *属性，这些属性都是到对应的控件的头文件里面去复制过来的，可以直接用。
	 */
public:
	
	/** The text to display */
	UPROPERTY(EditAnywhere,BlueprintReadWrite,Category = "Content", meta = (DisplayName = "TextContent", MultiLine = "true"))
	FText Text = FText::FromString(L"");//宽字符
	
	/** The color of the text */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Appearance" ,meta = (DisplayName = "TextColorAndOpacity"))
	FSlateColor TextColorAndOpacity;
	
	/** The font to render the text with */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Appearance", meta = (DisplayName = "TextFont"))
	FSlateFontInfo TextFont;

	/** The button style used at runtime */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Appearance", meta = (DisplayName = "ButtonStyle"))
	FButtonStyle ButtonStyle;

	/** The default button style */
	FButtonStyle* DefaultButtonStyle = nullptr;
	
protected:
	virtual bool Initialize() override;
	virtual void NativePreConstruct() override;
};
```

注意：像`TextColorAndOpacity`、`TextFont`、`ButtonStyle`这些属性都是可以直接从UE原来的控件的源代码中去拿的，并且还有注释，何乐而不为呢。

上节说过了如果是需要在PIE里面实时预览的话就把预览相关的代码写到`NativePreConstruct()`即可。详见下文。

# UWC_CommonBtn.cpp

```c++
#include "WC_CommonBtn.h"

UWC_CommonBtn::UWC_CommonBtn(const FObjectInitializer& ObjectInitializer): Super(ObjectInitializer)
{
	Text = FText::FromString(L"请重新填写文字！");

	//这几行代码可以直接从Button里面拿来用。
	if (DefaultButtonStyle == nullptr)
	{
		// HACK: THIS SHOULD NOT COME FROM CORESTYLE AND SHOULD INSTEAD BE DEFINED BY ENGINE TEXTURES/PROJECT SETTINGS
		DefaultButtonStyle = new FButtonStyle(FCoreStyle::Get().GetWidgetStyle<FButtonStyle>("Button"));

		// Unlink UMG default colors from the editor settings colors.
		DefaultButtonStyle->UnlinkColors();
	}
	ButtonStyle = *DefaultButtonStyle;
	
	//这几行代码可以直接从TextBlock里面拿来用。
	if (!IsRunningDedicatedServer())
	{
		static ConstructorHelpers::FObjectFinder<UFont> RobotoFontObj(*UWidget::GetDefaultFontName());
		TextFont = FSlateFontInfo(RobotoFontObj.Object, 24, FName("Bold"));
	}
	TextColorAndOpacity = FLinearColor::White;
}

bool UWC_CommonBtn::Initialize()
{
	//安全判断不要忘了，避免空指针异常，让程序崩溃。
	if (!Super::Initialize())
	{
		return false;
	}
	return true;
}

void UWC_CommonBtn::NativePreConstruct()
{
	Super::NativePreConstruct();
	//控件其实已经在Initialize()的时候用BindWidget拿到了，不过为了避免崩溃还是判断一下。
	if (Txt_Main)
	{
		Txt_Main->SetText(Text);
		Txt_Main->SetFont(TextFont);
		Txt_Main->SetColorAndOpacity(TextColorAndOpacity);
	}
	if (Btn_Main)
	{
		Btn_Main->SetStyle(ButtonStyle);
	}
}
```

这几行代码都比较简单，并且为什么这么做已经在前面说过了，所以就不再赘述啦。

# 新建蓝图并调整好样式

新建UMG文件，然后把它的父类设置为我们的父类：

![image-20211031202948452](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312029446.png)

然后把按钮和文本框建好，注意名字和层级，并且要让按钮铺满屏幕，因为这是作为一个单独的Widget，它的大小是在外面设置的，然后把我们想要的样式在编辑器里面调整好，调整好之后再点保存：

![image-20211031204353458](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312043806.png)

# 在外面使用它

然后就可以在其他的UMG中使用它啦：

![image-20211031204630230](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312046150.png)

如果发现这个大小不好调的话可以先调好一个，然后直接Ctrl CV就行。

调好大小，改好名字，然后就可以像使用按钮一样使用它了：

![image-20211031205347201](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312053276.png)

这是定义：

```c++
	
public:
	UPROPERTY(Meta = (BindWidget))
	UTextBlock * Txt_Main = nullptr;

	UPROPERTY(Meta = (BindWidget))
	UWC_CommonBtn * WB_CommonBtn_1;
	
	UPROPERTY(Meta = (BindWidget))
	UWC_CommonBtn * WB_CommonBtn_2;
	
public:
	UFUNCTION()
	void OnBtnClickCommonBtn_1();
	
	UFUNCTION()
	void OnBtnClickCommonBtn_2();
	
```

注意的是在绑定按钮事件或者访问其他属性的时候需要指定到Widget里面的Button：

```c++
void UWC_TestUI::NativeConstruct()
{
	Super::NativeConstruct();
	if (WB_CommonBtn_1)
	{
		WB_CommonBtn_1->Btn_Main->OnClicked.AddDynamic(this,&UWC_TestUI::OnBtnClickCommonBtn_1);
	}
	if (WB_CommonBtn_2)
	{
		WB_CommonBtn_2->Btn_Main->OnClicked.AddDynamic(this,&UWC_TestUI::OnBtnClickCommonBtn_2);
	}
}

//销毁的时候要取消绑定
void UWC_TestUI::NativeDestruct()
{
	//UE_LOG(LogTemp,Warning,TEXT("UWC_TestUI:NativeDestruct"));
	Super::NativeDestruct();
	if (WB_CommonBtn_1)
	{
		WB_CommonBtn_1->Btn_Main->OnClicked.RemoveDynamic(this,&UWC_TestUI::OnBtnClickCommonBtn_1);
	}
	if (WB_CommonBtn_2)
	{
		WB_CommonBtn_2->Btn_Main->OnClicked.RemoveDynamic(this,&UWC_TestUI::OnBtnClickCommonBtn_2);
	}
}

//按钮的两个响应事件
void UWC_TestUI::OnBtnClickCommonBtn_1()
{
	UE_LOG(LogTemp,Warning,TEXT("UWC_TestUI:OnBtnClickCommonBtn_1!"));
}

void UWC_TestUI::OnBtnClickCommonBtn_2()
{
	UE_LOG(LogTemp,Warning,TEXT("UWC_TestUI:OnBtnClickCommonBtn_2!"));
}

```

# 效果

稍微改一下关卡蓝图：

![image-20211031214034053](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312140512.png)

运行一手：

![通用按钮 00_00_00-00_00_30](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312135146.gif)

# 找属性的技巧

这里说一个技巧，就是我怎么知道我需要哪些属性呢？这些属性的表现是什么呢？我该去哪个控件的文件里面去拿呢？头文件又该引入啥呢？

先随便点开一个蓝图，然后拖出你想要的控件，比如我想要一个Button：

![image-20211031200939772](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312009973.png)

并且我想要知道这个Appearance下面的这个Style，但是我不知道应该在C++里面该怎么写。

那么可以点开Button的头文件，唤起代码编辑器：

![image-20211031201137470](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312011270.png)

然后在这里面查找`Appearance`，记得勾上区分大小写和全字匹配，多找几下就能在`DisplayName`这个属性下面找到`Style`。

![image-20211031201321495](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312013856.png)

然后我们就知道了这个Style其实是`FButtonStyle`类型的，你找到了这个就可以直接复制到你的代码里啦。

最后一步是找到它的头文件，虽然一般情况下会自动引入，但是还是说一下找头文件的方法，以备不时之需。

有两种方法，第一种是直接在这个界面往上翻，就能发现：

```c++
// Copyright Epic Games, Inc. All Rights Reserved.

#pragma once

#include "CoreMinimal.h"
#include "UObject/ObjectMacros.h"
#include "Input/Reply.h"
#include "Styling/SlateTypes.h"
#include "Widgets/SWidget.h"
#include "Components/ContentWidget.h"

#include "Button.generated.h"

class SButton;
class USlateWidgetStyleAsset;

```

猜一下是哪个呢？当然是`#include "Styling/SlateTypes.h"`这一行啦。

要是猜不准的话，还有第二种方法。

先点开UE的官方文档，然后在搜索框你需要的类名，比如说我想搜索`FButtonStyle`，然后进第一个页面就能看到：

![image-20211031202044551](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202110312020946.png)

直接把这行复制走就行了。

# 总结

这次我们可以自定义UMG了，并且也可以正确的复用了，还提了一下找属性的技巧，是提高UMG开发的一小步~

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 用户界面`、`UE4 UMG`、`UMG基础`。

