[TOC]

# 【UE4】加载资源的方式（四）使用StreamableManager进行加载

# 参考资料&原文链接

[AssetManager系列之TAssetPtr与FStreamableManager](https://zhuanlan.zhihu.com/p/80846277)

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[(UE4 4.20)UE4同步加载和异步加载UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)

[UE4 异步资源加载](https://blog.csdn.net/mmqqyyqqyyq/article/details/84001778)

# FStringAssetReference和TAssetPtr

前面已经用过它们来作为一个简单引用以加载资源，现在再来看一下它的定义：

可以看到`FStringAssetReference`的本质是`FSoftObjectPath`。

```c++
// Not deprecating these yet as it will lead to too many warnings in games
//UE_DEPRECATED(4.18, "FStringAssetReference was renamed to FSoftObjectPath as it is now not always a string and can also refer to a subobject")
typedef FSoftObjectPath FStringAssetReference;

//UE_DEPRECATED(4.18, "FStringClassReference was renamed to FSoftClassPath")
typedef FSoftClassPath FStringClassReference;
```

`TAssetPtr`的本质是`TSoftObjectPtr`。

```c++
// Not deprecating these yet as it will lead to too many warnings in games
//UE_DEPRECATED(4.18, "TAssetPtr was renamed to TSoftObjectPtr as it is not necessarily an asset")
template<class T=UObject>
using TAssetPtr = TSoftObjectPtr<T>;

//UE_DEPRECATED(4.18, "TAssetSubclassOf was renamed to TSoftClassPtr")
template<class TClass = UObject>
using TAssetSubclassOf = TSoftClassPtr<TClass>;
```

以下内容来自[官方文档 - FSoftObjectPath](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/FSoftObjectPath/)：

> FSoftObjectPath
>
> A struct that contains a string reference to an object, either a top level asset or a subobject.
>
> 包含对对象(顶级资产或子对象)的字符串引用的结构。
>
> Remarks
>
> A struct that contains a string reference to an object, either a top level asset or a subobject. This can be used to make soft references to assets that are loaded on demand. This is stored internally as an [FName](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Core/UObject/FName/index.html) pointing to the top level asset (/package/path.assetname) and an option a string subobject path. If the MetaClass metadata is applied to a [FProperty](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/FProperty/index.html) with this the UI will restrict to that type of asset.
>
> 包含对对象(顶级资产或子对象)的字符串引用的结构。这可以用于对按需加载的资产进行软引用。它在内部存储为[FName](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Core/UObject/FName/index.html)，指向顶级资产(/package/path.assetname)和一个选项，一个字符串子对象路径。如果MetaClass元数据被应用到[FProperty](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/FProperty/index.html)， UI将限制在该类型的资产。

以下内容来自[官方文档 - TSoftObjectPtr](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/TSoftObjectPtr/)：

> TSoftObjectPtr
>
> [TSoftObjectPtr](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/TSoftObjectPtr/index.html) is templatized wrapper of the generic [FSoftObjectPtr](https://docs.unrealengine.com/4.27/en-US/API/Runtime/CoreUObject/UObject/FSoftObjectPtr/index.html), it can be used in UProperties
>
> TSoftObjectPtr是通用的模板化包装器，它可以用于UProperties。

以下内容来自[官方文档 - 异步资源加载](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/Assets/AsyncLoading/)

> FSoftObjectPaths和TSoftObjectPtr
>
> 让美术或设计师引用资源的最简单方法是创建硬指针的UProperty并为它指定一个类别。在UE4中，如果有一个硬UObject指针属性引用了一个资源，则加载包含这个属性的对象（放在贴图中，或者从gameinfo等引用）时，就会加载这个资源。如果处理不当，就会在游戏开始时加载全部资源。如果您希望美术/设计师能够使用同一个UI作为硬指针来引用特定资源，而不必总是加载被引用资源，可以使用`FSoftObjectPath`或`TSoftObjectPtr`。
>
> `FSoftObjectPath`是一个简单的结构体，其中有一个字符串包含资源的完整名称。如果您在类中添加这个类型的属性，它就会像`UObject *`属性一样显示在编辑器中。它还会正确处理烘焙和重定向，因此如果您使用SoftObjectPath，就一定能在设备上正确工作。
>
> `TSoftObjectPtr`基本上是包含了`FSoftObjectPath`的`TWeakObjectPtr`，将用于设置特定类的模板，这样就可以限制编辑器UI仅允许选择特定类。如果被引用资源存在于内存中，则`TSoftObjectPtr.Get()`将返回这个资源。如果不存在，可以调用`ToSoftObjectPath()`来找出它引用的资源，使用下述方法加载这个资源，然后再次调用`TSoftObjectPtr.Get()`来取消引用。
>
> 如果美术或设计师要手动设置引用，则`TSoftObjectPtrs`和Soft Object Paths十分有用，但如果想要通过查询等功能来查找符合特定要求的资源，而不加载所有资源，则可以使用资源注册表和对象库。

总结：

FSoftObjectPath（FStringAssetReference ）：包含Asset位置字符串的结构体，可以指向`UObject*`的资源类型。

TSoftObjectPtr（TAssetPtr）：使用FSoftObjectPath构造出来的结构体，用于监视资源状态，指向尚未加载但可以根据请求加载的资产的指针。

TAssetSubclassOf ：指向已定义的基类的子类的指针，该子类尚未加载，但可以根据请求加载。用于指向蓝图而不是基本component。（大概是因为蓝图是Asset）

现在继续前面的内容：

```c++
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Test")
	FStringAssetReference SAR;

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Test")
	TAssetPtr<UTexture2D> AP;

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Test")
	TAssetSubclassOf<AMyCharacter> AMyCharacterSbp;
```

现在我给SAR设置一个UTexture2D的图片（UE的Logo），然后运行代码：

```c++
if (SAR.IsValid())
	{
		UE_LOG(LogTemp, Warning, TEXT("AssetName:%s"), *SAR.GetAssetName());
		UE_LOG(LogTemp, Warning, TEXT("AssetPathName:%s"), *(SAR.GetAssetPathName().ToString()));
		UE_LOG(LogTemp, Warning, TEXT("AssetPathString:%s"), *SAR.GetAssetPathString());
	}
```

打印的结果是：

```c++
[2021.11.03-09.32.07:371][185]LogTemp: Warning: AssetName:T_UE4Logo_Mask
[2021.11.03-09.32.07:371][185]LogTemp: Warning: AssetPathName:/Game/Mannequin/Character/Textures/T_UE4Logo_Mask.T_UE4Logo_Mask
[2021.11.03-09.32.07:371][185]LogTemp: Warning: AssetPathString:/Game/Mannequin/Character/Textures/T_UE4Logo_Mask.T_UE4Logo_Mask
```

现在的得到了引用资源的路径，可以加载，后面再说。







