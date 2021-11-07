[TOC]

# 【UE4】加载资源的方式（六）使用StreamableManager进行加载

# 参考资料&原文链接

[AssetManager系列之TAssetPtr与FStreamableManager](https://zhuanlan.zhihu.com/p/80846277)

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[(UE4 4.20)UE4同步加载和异步加载UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)

[UE4 异步资源加载](https://blog.csdn.net/mmqqyyqqyyq/article/details/84001778)

[StreamableManager和异步加载](https://blog.csdn.net/ywjun0919/article/details/92798152)

# 介绍

```c++
/** A native class for managing streaming assets in and keeping them in memory. AssetManager is the global singleton version of this with blueprint access */
struct ENGINE_API FStreamableManager : public FGCObject
```

用于管理流资产并将其保存在内存中的本机类。AssetManager是具有蓝图访问权的全局单例版本。

首先，需要创建`FStreamableManager`，我建议将它放在某类全局游戏单件对象中，如使用`GameSingletonClassName`在`DefaultEngine.ini`中指定的对象。

> PS：如果你还有其他的单例要使用，那么我建议你把单例都弄到GameInstance里面，因为引擎里面只能指定一个单例类。除了把它在引擎里面指定为单例以外，还有一种单例的实现方法，就是在GameInstance里面写。
>
> 先继承GameInstance，然后在里面搞一个static的FStreamableManager的指针，在CPP里面引入头文件之后将FStreamableManager初始化为nullptr，然后在重写GameInstance的Init方法，在里面把FStreamableManager给new出来然后再赋值给FStreamableManager指针变量，在需要的时候Get一下GameInstance在直接点GameInstance就能用了。其他要搞成单例的类也都通用，有机会找篇文章聊一下，这里不多赘述。
>
> PS：在4.26版本中发现已经是全局单例了。这种写法比较特殊，因为最新的AssetManager里面就带着有一个StreamableManager，我们可以直接用。也不知道是哪个大佬发现的，还能这么写- -：
>
> `UAssetManager::GetStreamableManager()`
>
> 建议都用这种写法，方便快捷。

拿到StreamableManager对象以后就好办了，可以将`FSoftObjectPath`传递给它并开始加载。`SynchronousLoad`将进行一次简单的块加载并返回对象。

# 同步加载单个资源

```c++
	/** 
	 * Synchronously load the referred asset and return the loaded object, or nullptr if it can't be found. This can be very slow and may stall the game thread for several seconds.
	 * 
	 * @param Target				Specific asset to load off disk
	 * @param bManageActiveHandle	If true, the manager will keep the streamable handle active until explicitly released
	 * @param RequestHandlePointer	If non-null, this will set the handle to the handle used to make this request. This useful for later releasing the handle
	 */
	UObject* LoadSynchronous(const FSoftObjectPath& Target, bool bManageActiveHandle = false, TSharedPtr<FStreamableHandle>* RequestHandlePointer = nullptr);
```

参数解释：

- 同步加载引用的资源并返回加载的对象，如果找不到，则返回nullptr。这可能会非常慢，并可能会暂停游戏线程数秒。
- `Target`要加载磁盘的特定资产。
- `bManageActiveHandle`如果为true，管理器将保持流句柄活动，直到显式释放。
- `RequestHandlePointer`如果非空，这将把句柄设置为发出此请求的句柄。这对以后释放句柄很有用。

句柄介绍：

```c++
/** A handle to a synchronous or async load. As long as the handle is Active, loaded assets will stay in memory*/
/**同步或异步加载的句柄。只要句柄是Active的，加载的资源就会留在内存中。*/
```

你可以控制句柄的显示释放来控制资产的释放。

它有几个包装的加载函数：

```c++
/** Typed wrappers */
template< typename T >
T* LoadSynchronous(const FSoftObjectPath& Target, bool bManageActiveHandle = false, TSharedPtr<FStreamableHandle>* RequestHandlePointer = nullptr);

template< typename T >
T* LoadSynchronous(const TSoftObjectPtr<T>& Target, bool bManageActiveHandle = false, TSharedPtr<FStreamableHandle>* RequestHandlePointer = nullptr)
	
template< typename T >
TSubclassOf<T> LoadSynchronous(const TSoftClassPtr<T>& Target, bool bManageActiveHandle = false, TSharedPtr<FStreamableHandle>* RequestHandlePointer = nullptr)
```

示例代码：

```c++
void UWC_TestUI::OnBtnClickCommonBtn_SmSyncOne()
{
FSoftObjectPath Path = FString(TEXT("Texture2D'/Game/UI/Images/InterestingUE4.InterestingUE4'"));
		UTexture2D* Img = UAssetManager::GetStreamableManager().LoadSynchronous<UTexture2D>(Path,false,nullptr);
		if (Img_SmSync)
		{
			Img_SmSync->SetBrushFromTexture(Img);
		}
}
```

该方法或许适用于较小对象，但可能会导致主线程长时间停滞。在这种情况下，您将需要使用`RequestAsyncLoad`，它将异步加载一组资源并在完成后调用委托。

实际上，FStreamableManager提供了RequestSyncLoad (同步)和 RequestAsyncLoad(异步)的两个接口。它们的区别是同步方法是立即加载，因此没有加载完成的回调函数，但是会返回一个`TSharedPtr<FStreamableHandle>`，异步方法除了返回`TSharedPtr<FStreamableHandle>`还会有一个加载完毕的回调函数，可以使用Lambda表达式在调用异步方法的时候一并传入。

除了`LoadSynchronous`这种方式以外，你还可以用`RequestSyncLoad`：

```c++
void UWC_TestUI::OnBtnClickCommonBtn_SmSyncOne()
{
	FSoftObjectPath Path = FString(TEXT("Texture2D'/Game/UI/Images/InterestingUE4.InterestingUE4'"));
	//注意：在资源未完成加载之前代码会在这一行暂停运行以等待资源加载完成。
	TSharedPtr<FStreamableHandle> SyncStreamableHandle = UAssetManager::GetStreamableManager().RequestSyncLoad(Path);
	if (SyncStreamableHandle)
	{
		UTexture2D * UImg2D = Cast<UTexture2D>(SyncStreamableHandle->GetLoadedAsset());
		if (UImg2D)
		{
			Img_SmSync->SetBrushFromTexture(UImg2D);
		}
	}
}
```

注意，使用这个方法加载单个资源的时候要用GetLoadedAsset来获得FStreamableHandle中返回的资源。

# 同步加载资源组

同步加载一组资源，并返回一个句柄。这可能会非常慢，并可能会暂停游戏线程数秒。

先看声明：

```c++
	/** 
	 * Synchronously load a set of assets, and return a handle. This can be very slow and may stall the game thread for several seconds.
	 * 
	 * @param TargetsToStream		Assets to load off disk
	 * @param bManageActiveHandle	If true, the manager will keep the streamable handle active until explicitly released
	 * @param DebugName				Name of this handle, will be reported in debug tools
	 */
	TSharedPtr<FStreamableHandle> RequestSyncLoad(TArray<FSoftObjectPath> TargetsToStream, bool bManageActiveHandle = false, FString DebugName = TEXT("RequestSyncLoad Array"));
	TSharedPtr<FStreamableHandle> RequestSyncLoad(const FSoftObjectPath& TargetToStream, bool bManageActiveHandle = false, FString DebugName = TEXT("RequestSyncLoad Single"));
```

参数解释：

- `TargetsToStream`要加载的资产磁盘。
- `bManageActiveHandle`如果为true，管理器将保持流句柄活动，直到显式释放。
- `DebugName`此句柄的名称，将在调试工具中报告。

示例代码：

```c++
void UWC_TestUI::OnBtnClickCommonBtn_SmSyncGroup()
{
	TArray<FSoftObjectPath> Paths;
	Paths.AddUnique(FString(TEXT("Texture2D'/Game/UI/Images/InterestingUE4.InterestingUE4'")));
	Paths.AddUnique(FString(TEXT("Texture2D'/Game/UI/Images/VS.VS'")));
	//注意：在资源未完成加载之前代码会在这一行暂停运行以等待资源加载完成。
	TSharedPtr<FStreamableHandle> SyncStreamableHandle = UAssetManager::GetStreamableManager().RequestSyncLoad(Paths);
	if (SyncStreamableHandle)
	{
		TArray<UObject *>LoadedAssets;
		SyncStreamableHandle->GetLoadedAssets(LoadedAssets);
		if (LoadedAssets.Num() > 0)
		{
			UTexture2D * UImg2D;
			for (int32 i = 0 ; i < LoadedAssets.Num() ; i ++)
			{
				UImg2D = Cast<UTexture2D>(LoadedAssets[i]);
				if (UImg2D)
				{
					//Img的名字
					FName ImgName = FName( *FString(TEXT("Img_") + FString::FromInt(i)));
					//动态创建一个Image
					UImage* Image = WidgetTree->ConstructWidget<UImage>(UImage::StaticClass(),ImgName);
					Image->SetBrushFromTexture(UImg2D);
					HB_SmAsyncGroup->AddChild(Image);
				}
			}
		}
	}
}
```

注意，使用这个方法加载多个资源的时候要用GetLoadedAssets来获得FStreamableHandle中返回的资源，里面传入要返回的数组。

# 异步加载单个资源

示例代码：

```c++
void UWC_TestUI::OnBtnClickCommonBtn_SmAsyncOne()
{
	FSoftObjectPath SmSyncGroupTexturePath = TEXT("Texture2D'/Game/UI/Images/VS.VS'");
	TSharedPtr<FStreamableHandle> OneHandle = UAssetManager::GetStreamableManager().RequestAsyncLoad(SmSyncGroupTexturePath);
	if (OneHandle)
	{
		UTexture2D * UImg2D = Cast<UTexture2D>(OneHandle->GetLoadedAsset());
		if (UImg2D)
		{
			Img_SmAsync->SetBrushFromTexture(UImg2D);
		}
	}
}
```

# 异步加载资源组

这是主要的可流操作。一个或多个目标对象的请求流。当完成时，将调用委托函数。返回可流句柄。

老规矩，还是直接上代码：

```c++
/** 
	 * This is the primary streamable operation. Requests streaming of one or more target objects. When complete, a delegate function is called. Returns a Streamable Handle.
	 *
	 * @param TargetsToStream		Assets to load off disk
	 * @param DelegateToCall		Delegate to call when load finishes. Will be called on the next tick if asset is already loaded, or many seconds later
	 * @param Priority				Priority to pass to the streaming system, higher priority will be loaded first
	 * @param bManageActiveHandle	If true, the manager will keep the streamable handle active until explicitly released
	 * @param bStartStalled			If true, the handle will start in a stalled state and will not attempt to actually async load until StartStalledHandle is called on it
	 * @param DebugName				Name of this handle, will be reported in debug tools
	 */
TSharedPtr<FStreamableHandle> RequestAsyncLoad(TArray<FSoftObjectPath> TargetsToStream, FStreamableDelegate DelegateToCall = FStreamableDelegate(), TAsyncLoadPriority Priority = DefaultAsyncLoadPriority, bool bManageActiveHandle = false, bool bStartStalled = false, FString DebugName = TEXT("RequestAsyncLoad ArrayDelegate"));
TSharedPtr<FStreamableHandle> RequestAsyncLoad(const FSoftObjectPath& TargetToStream, FStreamableDelegate DelegateToCall = FStreamableDelegate(), TAsyncLoadPriority Priority = DefaultAsyncLoadPriority, bool bManageActiveHandle = false, bool bStartStalled = false, FString DebugName = TEXT("RequestAsyncLoad SingleDelegate"));
```

参数解释：

- `TargetsToStream`要加载的资产磁盘。
- `DelegateToCall`委托在加载完成时调用。将被调用在下一个Tick，如果资产已加载，或许多秒后。
- `Priority`优先级传递给流系统，优先级高的将首先加载。
- `bManageActiveHandle`如果为true，管理器将保持流句柄活动，直到显式释放。
- `bStartStalled`如果为true，句柄将以停滞状态启动，并且在调用StartStalledHandle之前不会尝试实际异步加载。
- `DebugName`此句柄的名称，将在调试工具中报告。

同样的，他也有包装，不过是使用的Lambda来包装的：

```c++
/** Lambda Wrappers. Be aware that Callback may go off multiple seconds in the future. */
TSharedPtr<FStreamableHandle> RequestAsyncLoad(TArray<FSoftObjectPath> TargetsToStream, TFunction<void()>&& Callback, TAsyncLoadPriority Priority = DefaultAsyncLoadPriority, bool bManageActiveHandle = false, bool bStartStalled = false, FString DebugName = TEXT("RequestAsyncLoad ArrayLambda"));
TSharedPtr<FStreamableHandle> RequestAsyncLoad(const FSoftObjectPath& TargetToStream, TFunction<void()>&& Callback, TAsyncLoadPriority Priority = DefaultAsyncLoadPriority, bool bManageActiveHandle = false, bool bStartStalled = false, FString DebugName = TEXT("RequestAsyncLoad SingleLambda"));
```

注意：资源加载完毕了要用Get取，第二次调用Get则会取消引用：

```c++
	/**
	 * Dereference the soft pointer.
	 *
	 * @return nullptr if this object is gone or the lazy pointer was null, otherwise a valid UObject pointer
	 */
	FORCEINLINE T* Get() const
	{
		return dynamic_cast<T*>(SoftObjectPtr.Get());
	}
```

示例代码：

```c++
//.h
public:
	UPROPERTY(EditAnywhere,BlueprintReadWrite, Category = "Test")
	TArray<TSoftObjectPtr<UTexture2D>> ObjectPtrs;

//.cpp
void UWC_TestUI::OnBtnClickCommonBtn_SmAsyncGroup()
{
	if (ObjectPtrs.Num() <= 0)
	{
		return;
	}
	TArray<FSoftObjectPath> SmSyncGroupTexturePaths;
	for (auto item:ObjectPtrs)
	{
		SmSyncGroupTexturePaths.AddUnique(item.ToSoftObjectPath());
	}
	UAssetManager::GetStreamableManager().RequestAsyncLoad(SmSyncGroupTexturePaths,FStreamableDelegate::CreateUObject(this,&UWC_TestUI::OnStreamableManagerAsyncLoadCompleted));
}

void UWC_TestUI::OnStreamableManagerAsyncLoadCompleted()
{
	UTexture2D * UImg2D;
	for (int32 i = 0 ; i < ObjectPtrs.Num() ; i ++)
	{
		//前面指定了类型，这里就不用强转了
		//UTexture2D * UImg2D = Cast<UTexture2D>(item.Get());
		//注意要用Get来取得资源的引用
		UImg2D = ObjectPtrs[i].Get();
		if (UImg2D)
		{
			//Img的名字
			FName ImgName = FName( *FString(TEXT("Img_") + FString::FromInt(i)));
			//动态创建一个Image
			UImage* Image = WidgetTree->ConstructWidget<UImage>(UImage::StaticClass(),ImgName);
			Image->SetBrushFromTexture(UImg2D);
			HB_SmSyncGroup->AddChild(Image);
		}
	}
}
```

ObjectPtrs是暴露给外部的，由自己手动指定资产路径。

# 特点

这种加载方式的特点是：

- 同时支持同步和异步加载，选择和控制灵活多变。
- 代码量比前面几种方式稍大，不过理解起来也不是很难，结构较为清晰。
- 同步加载是用返回的`TSharedPtr<FStreamableHandle>`句柄并用`GetLoadedAsset`来获得同步加载的资源。
- 异步加载则是通过回调函数加上`Get()`的形式来获取加载好的资源。

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE资源加载`。

