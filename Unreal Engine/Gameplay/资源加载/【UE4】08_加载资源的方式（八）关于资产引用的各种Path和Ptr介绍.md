[TOC]

# 【UE4】加载资源的方式（八）关于资产引用的各种Path和Ptr介绍

# 参考资料&原文链接

这篇文章是完全转载的，原文链接在这里：https://blog.csdn.net/qq_29523119/article/details/84929384。

# 在UE4 4.18之后的使用变动

在`UE4 4.18`版本之前，资源的路径软引用采用的是：

`FStringAssetReference`、`FStringClassReference`、`FAssetPtr`、`TAssetPtr`、`TAssetSubclassOf`。

`UE4. 4.18`版本用了另外一套：

`FSoftObjectPath`、`FSoftClassPath`、`FSoftObjectPtr`、`TSoftObjectPtr`、`TSoftClassPtr `。

现在我的版本是`UE4 4.26.2`，看一眼上面的提到的文件，看一下它们的对应关系：

## FStringAssetReference -> FSoftObjectPath

```c++
// Copyright Epic Games, Inc. All Rights Reserved.

#pragma once

#include "CoreTypes.h"

COMPILE_WARNING("FStringAssetReference has been renamed to FSoftObjectPath, change to #include \"UObject/SoftObjectPath.h\" and rename references")
#include "UObject/SoftObjectPath.h"
```

## FStringClassReference -> FSoftClassPath

```c++
// Copyright Epic Games, Inc. All Rights Reserved.

#pragma once

#include "CoreTypes.h"

COMPILE_WARNING("FStringClassReference has been renamed to FSoftClassPath and merged headers, change to #include \"UObject/SoftObjectPath.h\" and rename references")
#include "UObject/SoftObjectPath.h"
```

## FAssetPtr -> FSoftObjectPtr

```c++
UE_DEPRECATED(4.18, "FAssetPtr was renamed to FSoftObjectPtr as it is not necessarily an asset")
typedef FSoftObjectPtr FAssetPtr;
```

## TAssetPtr -> TSoftObjectPtr

```c++
// Not deprecating these yet as it will lead to too many warnings in games
//UE_DEPRECATED(4.18, "TAssetPtr was renamed to TSoftObjectPtr as it is not necessarily an asset")
template<class T=UObject>
using TAssetPtr = TSoftObjectPtr<T>;
```

## TAssetSubclassOf -> TSoftClassPtr

```c++
//UE_DEPRECATED(4.18, "TAssetSubclassOf was renamed to TSoftClassPtr")
template<class TClass = UObject>
using TAssetSubclassOf = TSoftClassPtr<TClass>;
```

在UE4开发中经常性需要获取一些资源(StaticMesh,Material，Particle,Datatable, Actor蓝图，各种继承UObject的蓝图等等)的路径("/Game/.......")，然后利用这些路径进行资源的加载，参考[(UE4 4.20)UE4加载资源得到UClass和UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486))。也可以看本博客前面的几篇文章，对它们有一个较为详细的介绍。

蓝图类资源，也就是BlueprintClass，继承于UObject并且蓝图化的资源，如下所示：

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212115470.png)

非蓝图类资源：UTexture,UStaticMesh,UParticleSystem,UMaterialInterface这些资源：如纹理，粒子，静态网格,材质等等,下图所示:

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212115231.png)

而资源的加载我们经常性的用到 `FSoftObjectPath`、`FSoftClassPath`、`FSoftObjectPtr`、`TSubclassOf `这四个玩意。

# FSoftObjectPath

## 基本使用

翻译成“软对象路径”，也就是在一个(UObject,UStruct,Actor,ActorCompoennt)对象中配置一个具体资源(非蓝图资源和蓝图资源都可以)的路径，当这个对象被加载的时候，FSoftObjectPath指向的资源未被加载，仅仅是提供了一个路径：

```c++
UPROPERTY(EditAnywhere)
FSoftObjectPath  SoftObjectPath;
```

在编辑器显示的结果：

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212048894.png)

获取路径：在`Content Browser`中右键该资产，再选择`Copye Reference`即可复制路径，形如：

```c++
Texture2D'/Game/UI/Images/InterestingUE4.InterestingUE4'
```

## 用AllowedClasses筛选特定资源

我们只需要某种特定资源的路径，可以用UPROPERTY的Metadata Specifiers -- “AllowedClasses”

```c++
UPROPERTY(EditAnywhere, meta = (AllowedClasses ="Material,StaticMesh"))
FSoftObjectPath  softObjectPath;
```

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212050351.png)

注意`AllowedClasses = "Material,StaticMesh" `的 Material和StaticMesh不能有空格，否则会出现编辑器上的bug。

那么可以直接筛选出某种特定的蓝图资源？经我尝试不行。

# FSoftClassPath

对蓝图资源的一种弱引用，类似`FSoftObjectPath`，不过这里是蓝图资源，指向了蓝图资源的路径，通过路径我们可以手动加载。

(同步加载或者异步加载)蓝图资源获得UClass指针。

看源码：

````c++
/**
 *A struct that contains a string reference to a class, can be used to make soft references to classes
 */
struct COREUOBJECT_API FSoftClassPath : public FSoftObjectPath
{
FSoftClassPath()
{ }

FSoftClassPath(FSoftClassPath const& Other)
	: FSoftObjectPath(Other)
{ }
````

`FSoftClassPath`是继承`FSoftObjectPath`，也就是说其实也是指向某种资源的路径。

## 基本使用

和上面那个差不多：

````c++
UPROPERTY(EditAnywhere)
FSoftClassPath SoftClassPath;
````

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212052664.png)


其实`FSoftClassPath`指向的资源的路径为`FSoftObjectPath`的指向资源路径的子集:

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212053540.png)

用`MetaClass`可以筛选一些继承`UObject`的蓝图类。

## 用FSoftClassPath和MetaClass筛选自定义继承于UObject类的资源

这里说一下，FSoftObjectPath用AllowedClasses 只能筛选"Material,StaticMesh,Particle等资源"，像我们自定义继承UObject的类用AllowedClasses 就无法筛选了。

这时候就需要`FSoftClassPath `和 `MetaClass`，FSoftClassPath也是弱引用，得我们手动利用这个资源进行加载成UClass(异步或者同步)。

比如我是们创建一个继承AActor的类AMyActor：

```c++
UCLASS()
class MYPROJECT6_API AMyActor : public AActor
{
	GENERATED_BODY()
}
UCLASS(config=Game)
class AMyProject6Character : public ACharacter
{
	GENERATED_BODY()

	UPROPERTY(EditAnywhere, meta = (MetaClass = "MyActor"))
	FSoftClassPath aaa;

}
```

看下编辑器：

![img](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111212055784.png)

ToString() 依然是返回蓝图资源的路径。

# FSoftObjectPtr

软对象指针，作用我在[(UE4 4.20)UE4加载资源得到UClass和UObject ----------LoadObject,LoadClass,FStreamableManager](https://blog.csdn.net/qq_29523119/article/details/84455486)已经说过。

用于在异步加载并且资源加载完成触发回调函数的时候获取资源对应的对象指针用的，毕竟异步加载是无法直接获取对象指针的。

示例代码：

```c++
void ATestLoadObjectCharacter::BeginPlay()
{
    Super::BeginPlay();

    FStreamableManager streamableManager;
    FString strMeshFileName = "/Game/Geometry/Meshes/1M_Cube.1M_Cube";
    FStreamableDelegate streamableDelegate;
    FSoftObjectPath strMeshObjectFileName = FSoftObjectPath(strMeshFileName);
    streamableDelegate.BindUObject(this, &ThisClass::LoadFinish, strMeshObjectFileName);
    streamableManager.RequestAsyncLoad(strMeshObjectFileName, streamableDelegate);

}

void ATestLoadObjectCharacter::LoadFinish(FSoftObjectPath meshFilePath)
{
	FSoftObjectPtr meshObjectPtr = FSoftObjectPtr(meshFilePath);
	UObject* pObject = meshObjectPtr.Get();
	if (nullptr == pObject)
		return;

	UStaticMesh* pStaticMesh = Cast<UStaticMesh>(pObject);
	if (pStaticMesh)
	{
		UE_LOG(LogTemp, Error, TEXT("UStaicMesh name is %s"), *pStaticMesh->GetName());
	}

}
```

异步加载的时候绑定一个委托FSteamableDelegate，在委托回调的时候进行对象的获取,对象指针的获取是通过 FSoftObjectPtr 对 FSoftObjectPath 的构造。要拿到资源还要使用Get函数：

```c++
FSoftObjectPtr meshObjectPtr = FSoftObjectPtr(meshFilePath);
UObject* pObject = meshObjectPtr.Get();
```

# TSoftObjectPtr

TSoftObjectPtr是封装了FSoftObjectPtr的模板，同样是用于“在给予文件路径下检测一个资源是否已经加载进了内存，获取资源对应的对象指针”。类似上面的一种写法,，直接帮我们省掉了“Cast”转为对象的过程：

```c++
TSoftObjectPtr<UStaticMesh> StaticMeshPtr = TSoftObjectPtr<UStaticMesh>(meshFilePath);
UStaticMesh* pStaticMesh = StaticMeshPtr.Get();
if (pStaticMesh)
{
	UE_LOG(LogTemp, Error, TEXT("UStaicMesh name is %s"), *pStaticMesh->GetName());
}
```

# TSoftClassPtr

很抱歉我查了下并不存在FSoftClassPtr，UE4直接提供了`TSoftClassPtr`来检测蓝图资源加载而成的`UClass*`。

异步加载蓝图变成`UClass*`的demo:

````c++
void AMyProject7Character::BeginPlay()
{
	Super::BeginPlay();
	FStreamableManager streamableManager;
	FString strBPClassPath = "/Game/testActor.testActor_C";
	FStreamableDelegate streamableDelegate;
	FSoftClassPath SoftBPClassPathName = FSoftClassPath(strBPClassPath);
	streamableDelegate.BindUObject(this, &ThisClass::LoadFinish, SoftBPClassPathName);
	streamableManager.RequestAsyncLoad(SoftBPClassPathName, streamableDelegate);
}

void AMyProject7Character::LoadFinish(FSoftClassPath SoftBPClassPathName)
{
	TSoftClassPtr<AActor> ActorClassPtr = TSoftClassPtr<AActor>(SoftBPClassPathName);
	UClass* pClass = ActorClassPtr.Get();
	if (pClass)
	{
		UE_LOG(LogTemp, Error, TEXT("UStaicMesh name is %s"), *pClass->GetName());
	}
}
````

反正就是跟TSoftObjectPtr类似，只不过`TSoftClassPtr`是仅仅用于`UClass*`。

当然你可以直接用TSoftObjectPtr来获取UClass的指针，因为UClass本身就是UObject的子类，就像`TSoftObjectPtr<UClass>`。

# TSubclassOf

TSubclassOf 作用类似于

```c++
UPROPERTY(EditAnywhere)
UClass* actorClass;
```

不过TSubclassOf是安全类型也带筛选功能(用模板筛选UObject或者继承UObject的蓝图类)。

为强引用，就是所在资源加载的时候，这个UClass也跟着加载，就跟一个UStaticMeshComponent 里的 UPROPERTY(EditAnywhere) class UStaticMesh* StaticMesh 是同样的道理。

```c++
UPROPERTY(EditAnywhere)
TSubclassOf<ACharacter> actorClass;
```


获取UClass*指针

```c++
UClass* pClass = actorClass.Get();
```

参考资料
【1】http://api.unrealengine.com/INT/API/Runtime/CoreUObject/UObject/FSoftObjectPath/index.html

【2】http://api.unrealengine.com/INT/API/Runtime/CoreUObject/UObject/FSoftObjectPtr/index.html

【3】https://docs.unrealengine.com/en-us/Programming/Assets/AsyncLoading

【4】https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/Reference/Metadata

【5】https://docs.unrealengine.com/en-US/Programming/UnrealArchitecture/TSubclassOf

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE资源加载`。

