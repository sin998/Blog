[TOC]

# 【UE4】加载资源的方式（五）使用ObjectLibrary进行加载

# 参考资料&原文链接

[AssetManager系列之TAssetPtr与FStreamableManager](https://zhuanlan.zhihu.com/p/80846277)

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[(UE4 4.20)UE4同步加载和异步加载UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)

[UE4 异步资源加载](https://blog.csdn.net/mmqqyyqqyyq/article/details/84001778)

[StreamableManager和异步加载](https://blog.csdn.net/ywjun0919/article/details/92798152)

[UE4学习记录：资源加载（一） ——DataAsset使用](https://blog.csdn.net/hyf2713/article/details/104972017)

[UObjectLibrary](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Engine/Engine/UObjectLibrary/)

# UObjectLibrary

ObjectLibrary：是一个对象，包含了一系列继承共享基类的未加载对象或者未加载对象的FAssetData 。您可以通过提供一个搜索路径来加载一个对象库，它将加载那个路径中的所有资源。

发现了把，里面放的就是一个`FAssetData`：

```c++
	/** Asset data of objects that will belong in library, possibly not loaded yet */
	TArray<FAssetData>	AssetDataList;
```

只不过与`FAssetData`不同的是`FAssetData`需要我们手动添加路径，而这个我们只需要给它设定一个路径，然后让它来扫描，那么就不用我们手动来一个一添加了，会为我们省很多事。

`CreateLibrary`函数介绍：

```c++
/** 
	 * Static function to create a new ObjectLibrary at runtime, with various options set
	 * There is now a better version of this functionality in AssetManager, if you are creating many game-specific libraries you should switch to using AssetManager instead
	 * @param	InBaseClass				Only objects of this class can exist in the library
	 * @param	bInHasBlueprintClasses	If true, this library contains blueprint classes derived from BaseClass, will convert them correctly
	 * @param	InUseWeak				If true, references to objects are weak, so they can be garbage collected. Useful in the editor to allow deletion
	 */
	ENGINE_API static class UObjectLibrary* CreateLibrary(UClass* InBaseClass, bool bInHasBlueprintClasses, bool bInUseWeak);
```

静态函数在运行时创建新的ObjectLibrary，并设置各种选项。参数介绍：

> 现在在AssetManager中有一个更好的版本，如果你正在创建许多游戏特定的库，你应该转而使用AssetManager。
>
> `InBaseClass`只有该类的对象才能存在于库中。
>
> `bInHasBlueprintClasses`如果为true，则该库包含从BaseClass派生的蓝图类，将正确地转换它们
>
> `InUseWeak`如果为true，则对对象的引用为弱引用，因此可以对它们进行垃圾回收。在编辑器中用于允许删除。

注意：最好不要让ObjectLibrary给GC回收掉，所以可以添加到根节点，因为你不想还在扫描或者加载的途中出什么岔子。

扫描好之后可以用`GetAssetDataList`来获取扫描的结果，传入一个`TArray<FAssetData>`的引用来获得所有的路径，这个路径就是传入扫描的路径，然后可以按需加载，可以说是相当方便了。

```c++
	/** Returns the list of asset data */
	virtual void GetAssetDataList(TArray<FAssetData>& OutAssetData);
```

最后再加载即可。附上完整代码：

```c++
//.h
private:
	int32 CurrentCount = 0;
	UObjectLibrary* TextureOL;
	TArray<FAssetData> TextureData;
	TArray<FSoftObjectPath> TexturePath;
	
//.cpp
void UWC_TestUI::NativeConstruct()
{
	Super::NativeConstruct();
	//如果UObjectLibrary未被加载
	if (!TextureOL)
	{
		//加兹一手，类型写自己想要的
		TextureOL = UObjectLibrary::CreateLibrary(UTexture2D::StaticClass(), false, false);
		//手动指定不要被GC回收
		TextureOL->AddToRoot();
	}
	//扫描路径
	TextureOL->LoadAssetDataFromPath("/Game/UI/Images");
}

void UWC_TestUI::OnBtnClickCommonBtn_OL()
{
	//没有数据就先拿数据
	if(TexturePath.Num() <= 0)
	{
		//拿到资产列表，注意这里的TextureData传递的是一个引用
		TextureOL->GetAssetDataList(TextureData);
		for (int32 i = 0; i < TextureData.Num(); ++i)
		{
			//保存了TextureData的所有ToSoftObjectPath
			TexturePath.AddUnique(TextureData[i].ToSoftObjectPath());
		}
	}
	//有数据就填充列表
	if (TexturePath.Num() > 0)
	{
		FString ImgSAR = TexturePath[CurrentCount].ToString();
		Img_OL->SetBrushFromTexture(LoadObject<UTexture2D>(nullptr,*ImgSAR));
		if (CurrentCount >= TexturePath.Num() - 1)
		{
			CurrentCount = 0;
		}
		else {
			CurrentCount++;
		}
	}
}
```

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 资源加载`。
