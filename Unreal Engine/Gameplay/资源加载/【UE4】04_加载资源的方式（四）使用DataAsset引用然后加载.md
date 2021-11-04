[TOC]

# 【UE4】加载资源的方式（四）使用DataAsset引用然后加载

# 参考资料&原文链接

[AssetManager系列之TAssetPtr与FStreamableManager](https://zhuanlan.zhihu.com/p/80846277)

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[(UE4 4.20)UE4同步加载和异步加载UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)

[UE4 异步资源加载](https://blog.csdn.net/mmqqyyqqyyq/article/details/84001778)

[StreamableManager和异步加载](https://blog.csdn.net/ywjun0919/article/details/92798152)

[UE4学习记录：资源加载（一） ——DataAsset使用](https://blog.csdn.net/hyf2713/article/details/104972017)

[UObjectLibrary](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Engine/Engine/UObjectLibrary/)

# DataAsset

包含一个简单资源数据的基类。如果您继承了这个类，编辑器将在content browser中列出它。这个相当于Unity Asset文件，可以用来存储数据。

它里面只有一个TSubclassOf：

```c++
private:
	UPROPERTY(AssetRegistrySearchable)
	TSubclassOf<UDataAsset> NativeClass;
```

TSubclassOf模板允许TClassType以类型安全的方式传递。就是在传递的时候保证类型安全。

使用：

先继承，再往里面写我们感兴趣的数据：

```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "Engine/DataAsset.h"
#include "ImageDataAsset.generated.h"

/*
 *图片结构体
 */
USTRUCT(BlueprintType)
struct FMyImage
{
	GENERATED_BODY()

public:
	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	FName ImageName;

	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	FStringAssetReference ImagePath;

};

/**
 * 自定义的DataAsset，在Unreal的Content里面右键「Miscellaneous」 - 「DataAsset」，再选择这个类即可创建。
 * 创建完成之后可以打开再向里面添加想要的数据。
 */
UCLASS()
class TESTPROJECT_API UImageDataAsset : public UDataAsset
{
	GENERATED_BODY()

public:
	UPROPERTY(EditAnywhere, BlueprintReadWrite)
	TArray<FMyImage> Images;
};

```

你可以写`FStringAssetReference`，也可以写`TAssetPtr`，也可以直接写`TSoftObjectPtr`。都是一样的，存的都是一个弱引用，后面可以用`LoadObject`或者`StreamManager`来加载。

然后在UE里面创建，并选择我们自己的类：

![image-20211104214836999](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111042148972.png)

![image-20211104214909635](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111042149772.png)

可以在里面添加引用：

![image-20211104215031211](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111042150507.png)

C++使用：

```c++
//.h
public:
	UPROPERTY(EditAnywhere,BlueprintReadWrite, Category = "Test")
	UImageDataAsset* ImageAssets;
private:
	int32 CurCount = 0;

//.cpp
void UWC_TestUI::OnBtnClickCommonBtn_DataAsset()
{
	if (ImageAssets)
	{
		if (ImageAssets->Images.Num() > 0)
		{
			FString ImgSAR = ImageAssets->Images[CurCount].ImagePath.ToString();
			Img_DataAsset->SetBrushFromTexture(LoadObject<UTexture2D>(nullptr,*ImgSAR));
			if (CurCount >= ImageAssets->Images.Num() - 1)
			{
				CurCount = 0;
			}
			else {
				CurCount++;
			}
		}
	}
}
```

不要忘了选好：

![image-20211104215747658](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111042157758.png)

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 资源加载`。

