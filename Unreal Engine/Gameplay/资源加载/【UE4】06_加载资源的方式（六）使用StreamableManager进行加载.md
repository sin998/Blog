[TOC]

# 【UE4】加载资源的方式（六）使用StreamableManager进行加载

# 参考资料&原文链接

[AssetManager系列之TAssetPtr与FStreamableManager](https://zhuanlan.zhihu.com/p/80846277)

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[(UE4 4.20)UE4同步加载和异步加载UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)

[UE4 异步资源加载](https://blog.csdn.net/mmqqyyqqyyq/article/details/84001778)

[StreamableManager和异步加载](https://blog.csdn.net/ywjun0919/article/details/92798152)

[UE4学习记录：资源加载（一） ——DataAsset使用](https://blog.csdn.net/hyf2713/article/details/104972017)

[UObjectLibrary](https://docs.unrealengine.com/4.27/en-US/API/Runtime/Engine/Engine/UObjectLibrary/)

# StreamableManager同步加载资源（组）

首先，需要创建`FStreamableManager`，我建议将它放在某类全局游戏单件对象中，如使用`GameSingletonClassName`在`DefaultEngine.ini`中指定的对象。

> PS：如果你还有其他的单例要使用，那么我建议你把单例都弄到GameInstance里面，因为引擎里面只能指定一个单例类。除了把它在引擎里面指定为单例以外，还有一种单例的实现方法，就是在GameInstance里面写。
>
> 先继承GameInstance，然后在里面搞一个static的FStreamableManager的指针，在CPP里面引入头文件之后将FStreamableManager初始化为nullptr，然后在重写GameInstance的Init方法，在里面把FStreamableManager给new出来然后再赋值给FStreamableManager指针变量，在需要的时候Get一下GameInstance在直接点GameInstance就能用了。其他要搞成单例的类也都通用，有机会找篇文章聊一下，这里不多赘述。
>
> 除了上面两种方法以外，还可以有另一种写法，因为最新的AssetManager里面就带着有一个StreamableManager，我们可以直接用。也不知道是哪个大佬发现的，还能这么写- -：
>
> `UAssetManager::GetStreamableManager()`

拿到StreamableManager对象以后就好办了，可以将`FSoftObjectPath`传递给它并开始加载。`SynchronousLoad`将进行一次简单的块加载并返回对象。该方法或许适用于较小对象，但可能会导致主线程长时间停滞。在这种情况下，您将需要使用`RequestAsyncLoad`，它将异步加载一组资源并在完成后调用委托。





普通版



带模板



带回调函数



# StreamableManager异步加载资源（组）









资源加载完毕了要用Get取



```
	/** 
	 * Synchronously load the referred asset and return the loaded object, or nullptr if it can't be found. This can be very slow and may stall the game thread for several seconds.
	 * 
	 * @param Target				Specific asset to load off disk
	 * @param bManageActiveHandle	If true, the manager will keep the streamable handle active until explicitly released
	 * @param RequestHandlePointer	If non-null, this will set the handle to the handle used to make this request. This useful for later releasing the handle
	 */
	UObject* LoadSynchronous(const FSoftObjectPath& Target, bool bManageActiveHandle = false, TSharedPtr<FStreamableHandle>* RequestHandlePointer = nullptr);

```



# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 资源加载`。

