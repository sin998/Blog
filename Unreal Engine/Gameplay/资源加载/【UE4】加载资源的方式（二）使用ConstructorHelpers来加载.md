[TOC]

# 【UE4】加载资源的方式（二）使用ConstructorHelpers来加载

# 参考资料&原文链接

[UE4中资源加载资源的方式](https://blog.csdn.net/a359877454/article/details/52765133)

[虚幻4批量加载资源，加载指定目录或指定类的资源](https://blog.csdn.net/qq_16756235/article/details/82714491)

[UE4资源加载的方式](https://www.sohu.com/a/203578475_667928)

[Unreal4异步加载资源](https://zhuanlan.zhihu.com/p/369304308)

[Unreal Engine 4 —— Asset Manager介绍](https://blog.csdn.net/noahzuo/article/details/78815596)

[虚幻官方文档 - AssetManager](https://docs.unrealengine.com/4.26/zh-CN/ProductionPipelines/AssetManagement/)

[【UE4】资源管理之UAssetManager用法](https://zhuanlan.zhihu.com/p/129712105)

[如何评价unreal4.17的Asset manager?](https://www.zhihu.com/question/66615175?sort=created)

[芭蕉不解的博客](https://bajiaobujie.github.io/)

[(UE4 4.20)UE4同步加载和异步加载UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)

# 使用ConstructorHelpers来加载

使用特殊类 ConstructorHelpers 加载，这个类在构造阶段查找某个对象的对象和类。

在构造函数中，ConstructorHelpers 类将尝试在内存中查找该资产，如果找不到，则进行加载。

请注意，使用资产的完整路径来指定要加载的内容。如果该资产不存在或者由于出错而无法加载，那么该属性将设置为 nullptr。

UPROPERTY 的声明与前面的硬性引用示例相同。它们的工作方式相同，只不过是最初的设置方式有所差别。

```c++
static ConstructorHelpers::FClassFinder<AMyCharacter> UnitSelector(TEXT("Blueprint'/Game/Blueprints/MyBlueprint.MyBlueprint_C'"));  
TSubclassOf<AMyCharacter> UnitSelectorClass = UnitSelector.Class; 
```

注意：

- 只能在构造函数中使用。如果在非构造函数中使用的话，则会引起Crash（原因不明）。
- 前面必须加上`static`。
- 同样的，代码里面的`AMyCharacter`都可以换成`AActor`，只是个人更推荐写`AMyCharacter`，因为`AMyCharacter`是`BP_MyCharacter`的最直接的父类。

声明：

```c++
private:
	FString MyCharacterPath = FString(TEXT("Blueprint'/Game/Core/BP_MyCharacter.BP_MyCharacter_C'"));
	TSubclassOf<AMyCharacter> UnitSelectorClass;
```

在构造的时候拿到：

```c++
UWC_TestUI::UWC_TestUI()
{
	static ConstructorHelpers::FClassFinder<AMyCharacter> UnitSelector(TEXT("Blueprint'/Game/Core/BP_MyCharacter.BP_MyCharacter_C'"));  
	UnitSelectorClass = UnitSelector.Class;
}
```

在想要使用的地方使用：

```c++
void UWC_TestUI::NativeConstruct()
{
	//UE_LOG(LogTemp,Warning,TEXT("UWC_TestUI:NativeConstruct"));
	//UE_LOG(LogTemp,Warning,TEXT("UWC_TestUI:NativeConstruct Txt_Main is nullptr : %d"),Txt_Main == nullptr ? true : false);
	Super::NativeConstruct();
	FVector Location(230.0,0,120);
	FRotator Rotation(0.0,0,0);
	MyCharacter = Cast<AMyCharacter>(GetWorld()->SpawnActor(UnitSelectorClass,&Location,&Rotation));
}
```













```c++




/** 
 * Find an optional object, relies on the name being unqualified 
 * @see StaticFindObjectFast()
 */
template< class T > 
inline T* FindObjectFast( UObject* Outer, FName Name, bool ExactClass=false, bool AnyPackage=false, EObjectFlags ExclusiveFlags=RF_NoFlags )
{
	return (T*)StaticFindObjectFast( T::StaticClass(), Outer, Name, ExactClass, AnyPackage, ExclusiveFlags );
}

/**
 * Find an optional object.
 * @see StaticFindObject()
 */
template< class T > 
inline T* FindObject( UObject* Outer, const TCHAR* Name, bool ExactClass=false )
{
	return (T*)StaticFindObject( T::StaticClass(), Outer, Name, ExactClass );
}

```

























# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE4 资源加载`。
