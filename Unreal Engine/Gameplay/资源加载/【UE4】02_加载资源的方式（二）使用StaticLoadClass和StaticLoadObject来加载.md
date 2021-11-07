[TOC]

# 【UE4】加载资源的方式（二）使用StaticLoadClass和StaticLoadObject来加载

# 参考资料&原文链接

[UE4笔记---C++加载BP蓝图及C++BP蓝图文件并创建UUserWidget对象](https://blog.csdn.net/chinahaerbin/article/details/74905439)

[UE4中资源加载资源的方式](https://blog.csdn.net/a359877454/article/details/52765133)

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[[UE4]C++实现动态加载的问题:LoadClass和LoadObject](https://www.iteye.com/blog/aigo-2281558)

[[UE4]C++实现动态加载UObject:StaticLoadObject,以Texture和Material为例](https://www.iteye.com/blog/aigo-2268056)

[Dynamic Asset Loading with C++](https://www.youtube.com/watch?v=pJIAmSGxfmQ)

[Dynamic Load Object](https://wiki.unrealengine.com/Dynamic_Load_Object)

# 静态加载和动态加载

这里说的**静态加载指的是必须在构造函数中完成的加载方式（主要使用ConstructorHelpers，后面的文章会讲述）**，**动态加载值得是可以在Runtime期间加载的方式**。

# 同步加载和异步加载

注意：`LoadObject<T>()`和`LoadClass<T>()` 这两者的加载方式都是同步加载。

为了防止某些情况下引发的巨大延迟，必要的时候我们需要使用异步资源载入系统。

若想用异步加载则可以使用`FStreamableManager`，它同时提供了同步加载和异步加载两种方式。后面的文章会讲述。

# 动态加载的核心函数及参数

核心函数是StaticLoadObject和StaticLoadClass函数，它们分别加载继承于UObject的资源和加载继承于UClass的C++类，当然加载它们的Native Class也是可以的。

它们的参数都是一样的，如下：

```
/**
 * Find or load an object by string name with optional outer and filename specifications.
 * These are optional because the InName can contain all of the necessary information.
 *
 * @param ObjectClass	The class (or a superclass) of the object to be loaded.
 * @param InOuter		An optional object to narrow where to find/load the object from
 * @param Name			String name of the object. If it's not fully qualified, InOuter and/or Filename will be needed
 * @param Filename		An optional file to load from (or find in the file's package object)
 * @param LoadFlags		Flags controlling how to handle loading from disk, from the ELoadFlags enum
 * @param Sandbox		A list of packages to restrict the search for the object
 * @param bAllowObjectReconciliation	Whether to allow the object to be found via FindObject in the case of seek free loading
 * @param InstancingContext				InstancingContext used to remap imports when loading a packager under a new name
 *
 * @return The object that was loaded or found. nullptr for a failure.
 */
```

- `ObjectClass`要加载的对象的类（或超类）。
- `InOuter`一个可选对象，用于缩小查找/加载对象的位置。
- `Name`对象的字符串名称。如果它不是完全限定的，则需要 InOuter 和/或 Filename。
- `Filename`要从中加载（或在文件的包对象中查找）的可选文件。
- `LoadFlags`控制如何处理从磁盘加载的标志，来自 ELoadFlags 枚举。
- `Sandbox`用于限制对象搜索的包列表。
- `bAllowObjectReconciliation`是否允许在seek free加载的情况下通过FindObject找到对象。
- `InstancingContext`在序列化期间调用时的附加上下文。

这个函数乍一看很吓人，这么多参数。其实如果只是动态加载资源的话我们只需要关注几个中要的参数就可以了，下文会一一讲到。

# LoadClass与StaticLoadClass、LoadObject与StaticLoadObject

动态加载**UObject**和动态加载**UClass**分别用`LoadObject<T>()`和`LoadClass<T>()` ，两者均在在`UObjectGlobals.h`中。

## 关系

其中`LoadClass`是将`StaticLoadClass`封装成了模板函数，省略了一些参数，只需要传您关心的参数即可，使用更为方便。

同样`LoadObject`是将`StaticLoadObject`封装成了模板函数。

## 区别

`LoadObject<T>()`用来加载非蓝图资源，比如动画、贴图、音效等资源；

`LoadClass<T>()`用来加载蓝图并获取蓝图Class，比如角色蓝图。如果要用蓝图创建对象，必须先通过LoadClass获取class，然后再通过SpawnActor生成对象。

# 加载UObject和UClass

## 加载UObject

```c++
COREUOBJECT_API UObject* StaticLoadObject( UClass* Class, UObject* InOuter, const TCHAR* Name, const TCHAR* Filename = nullptr, uint32 LoadFlags = LOAD_None, UPackageMap* Sandbox = nullptr, bool bAllowObjectReconciliation = true, const FLinkerInstancingContext* InstancingContext = nullptr);
```

例子：

```c++
void UWC_TestUI::OnBtnClickCommonBtn_StaticLoadObject()
{
	UE_LOG(LogTemp,Warning,TEXT("UWC_TestUI:OnBtnClickCommonBtn_StaticLoadObject!"));
	if (!InterestingUE4Path.IsEmpty())
	{
		if (InterestingUE4 == nullptr)
		{
			InterestingUE4 = Cast<UTexture2D>(StaticLoadObject(UTexture2D::StaticClass(),nullptr,*InterestingUE4Path,nullptr,LOAD_None,nullptr));
			if (InterestingUE4 != nullptr)
			{
				Img_StaticLoadObject->SetBrushFromTexture(InterestingUE4);
			}
		}
	}
}
```

这里只用了两个参数：

`ObjectClass`要加载的对象的类（或超类），告诉UE我要加载的资源的是什么类型的。

`Name`对象的字符串名称，可以简单理解为它在UE的Content中的路径，可以直接右键然后复制下来：

![image-20211102150734502](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111021507910.png)

其中：

```c++
public:
	UPROPERTY(Meta = (BindWidget))
	class UImage * Img_StaticLoadObject;
private:
	UTexture2D * InterestingUE4 = nullptr;
	FString InterestingUE4Path = FString(TEXT("Texture2D'/Game/UI/Images/InterestingUE4.InterestingUE4'"));
```

还有一个函数是LoadObject，这个函数只是将StaticLoadObject封装成模板而已，除了Outer和Name以外其余都是空：

```c++
/** 
 * Load an object. 
 * @see StaticLoadObject()
 */
template< class T > 
inline T* LoadObject( UObject* Outer, const TCHAR* Name, const TCHAR* Filename=nullptr, uint32 LoadFlags=LOAD_None, UPackageMap* Sandbox=nullptr )
{
	return (T*)StaticLoadObject( T::StaticClass(), Outer, Name, Filename, LoadFlags, Sandbox );
}
```

你如果也不关心这些参数的话就可以直接用LoadObject，写起来还快、简洁，这是例子：

```c++
FString InterestingUE4MappingPath = FString(TEXT("Texture2D'/Game/UI/Images/InterestingUE4Mapping.InterestingUE4Mapping'"));
UTexture2D * InterestingUE4Mapping = Cast<UTexture2D>(LoadObject<UTexture2D>(nullptr,*InterestingUE4MappingPath));
```

### 支持加载的资源类型

Texture、Material、SoundWave、SoundCue、ParticlesSystem、AnimMontage、BlendSpace(1D，2D，3D)、AnimSequence、AnimBlueprint、SkeletalMesh等等。这些文件的父类都是**UObject**，所以也可以先加载为`UObject*`然后再强转为具体的类型，只要父类是UObject都可以。

## 加载UClass

```c++
/** Version of StaticLoadObject() that will load classes */
COREUOBJECT_API UClass* StaticLoadClass(UClass* BaseClass, UObject* InOuter, const TCHAR* Name, const TCHAR* Filename = nullptr, uint32 LoadFlags = LOAD_None, UPackageMap* Sandbox = nullptr);
```

例子：

```c++
//这个地方有个坑，下面会讲，注意这个手动添加的「_C」
FString MyCharacterPath = FString(TEXT("'/Game/Core/BP_MyCharacter.BP_MyCharacter_C'"));
//这里可以填AMyCharacter或AActor，因为AMyCharacter继承的就是AActor，推荐填AMyCharacter。
UClass *MyCharacterClass = StaticLoadClass(AMyCharacter::StaticClass(),nullptr,*MyCharacterPath,nullptr,LOAD_None,nullptr);
//如果还要从Class生成Actor的话还需要Spawn，生成的时候可以指定位置和旋转和和你需要的其他参数，具体看源码。
FVector Location(230.0,0,120);
FRotator Rotation(0.0,0,0);
MyCharacter = Cast<AMyCharacter>(GetWorld()->SpawnActor(MyCharacterClass,&Location,&Rotation));
```

`MyCharacterPath`这个地方很坑，如果直接在蓝图上右键复制引用的话得到的结果是：`'Blueprint /Game/Core/BP_MyCharacter.BP_MyCharacter'`。

但是启动游戏，代码执行到这一行就会报错，并不能加载成功。

```c++
LogUObjectGlobals: Warning: Failed to find object 'Class /Game/Core/BP_MyCharacter.BP_MyCharacter'
LogSpawn: Warning: SpawnActor failed because no class was specified
```

解决方式：

在路径的后面加上`_C`。即：`FString MyCharacterPath = FString(TEXT("'Blueprint /Game/Core/BP_MyCharacter.BP_MyCharacter_C'"));`。

同样的，加载UClass同样还有一个函数LoadClass，也只是将StaticLoadClass封装成模板而已：

```c++
/**
 * Load a class object
 * @see StaticLoadClass
 */
template< class T > 
inline UClass* LoadClass( UObject* Outer, const TCHAR* Name, const TCHAR* Filename=nullptr, uint32 LoadFlags=LOAD_None, UPackageMap* Sandbox=nullptr )
{
	return StaticLoadClass( T::StaticClass(), Outer, Name, Filename, LoadFlags, Sandbox );
}
```

优点也是一样的，如果你不关心某些参数，写起来快，这是例子：

```c++
UClass *MyCharacterClass = LoadClass<AMyCharacter>(nullptr,*MyCharacterPath);
FVector Location(230.0,0,120);
FRotator Rotation(0.0,0,0);
MyCharacter = Cast<AMyCharacter>(GetWorld()->SpawnActor(MyCharacterClass,&Location,&Rotation));
```

# 特点

此加载方式的特点是：

- 同步加载，注意大资源可能会引起的卡顿和掉帧。
- 需要路径不能出错（后面会用优化），蓝图则需要加上`_C`。
- 不论加载的是UObject还是UClass，其本质都是使用路径加载，即必须知道完整路径。
- 支持加载的UObject的类型很多。例如Texture、Material、SoundWave、SoundCue、ParticlesSystem、AnimMontage、BlendSpace(1D，2D，3D)、AnimSequence、AnimBlueprint、SkeletalMesh等等。

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE资源加载`。

