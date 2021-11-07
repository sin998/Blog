[TOC]

# 【UE4】加载资源的方式（三）使用ConstructorHelpers来加载

# 参考资料&原文链接

[UE4资源加载方式](https://www.sohu.com/a/203578475_667928)

[[UE4]C++静态加载问题：ConstructorHelpers::FClassFinder和FObjectFinder](https://www.iteye.com/blog/aigo-2281373)

# 使用ConstructorHelpers来加载

使用特殊类 `ConstructorHelpers `加载，这个类在构造阶段查找某个对象的对象和类。

FObjectFinder()是对LoadObject()的**封装**。但是FClassFinder()不是对LoadClass()的封装，FClassFinder()**内部调用**的是LoadObject()。

在构造函数中，ConstructorHelpers 类将尝试在内存中查找该资产，如果找不到，则进行加载。

请注意，使用资产的完整路径来指定要加载的内容。如果该资产不存在或者由于出错而无法加载，那么该属性（下文的UnitSelector）将设置为 nullptr。

UPROPERTY 的声明与前面的硬性引用示例相同。它们的工作方式相同，只不过是最初的设置方式有所差别。

注意：

- `ConstructorHelpers `只能在构造函数中使用。如果在非构造函数中使用的话，则会引起Crash（原因不明），在其代码内部检查了是否在构造函数中调用：`CheckIfIsInConstructor(ObjectToFind);`。
- `ConstructorHelpers `前面必须加上`static`。
- 同样的，代码里面的`AMyCharacter`都可以换成`AActor`，只是个人更推荐写`AMyCharacter`，因为`AMyCharacter`是`BP_MyCharacter`的最直接的父类。

## FClassFinder

声明：

```c++
private:
	TSubclassOf<AMyCharacter> UnitSelectorClass;
```

在构造的时候加载：

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
	Super::NativeConstruct();
	FVector Location(230.0,0,120);
	FRotator Rotation(0.0,0,0);
	MyCharacter = Cast<AMyCharacter>(GetWorld()->SpawnActor(UnitSelectorClass,&Location,&Rotation));
}
```

`ConstructorHelpers`有`FObjectFinder`和`FClassFinder`两种方法，通过Name（完整路径）分别找到Object或Class，它们的用法相似，不再赘述。

# 特点

- 同样是需要完整路径的一种加载方式。
- 只能在构造函数里面使用，非构造函数中使用会引起Error。这适用于某些情况，例如要使用这个资源的时候必须加载另一种资源，这个时候就可以考虑在构造函数中使用ConstructorHelpers。

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE资源加载`。

