[TOC]

# 【UE4】07_加载资源的方式（七）使用AssetManager进行加载

# 参考资料&原文链接

[官方文档 - AssetManager](https://docs.unrealengine.com/4.26/zh-CN/ProductionPipelines/AssetManagement/)

[Asset Registry](https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/Assets/Registry/)

[Unreal Engine 4 —— Asset Manager介绍](https://blog.csdn.net/noahzuo/article/details/78815596)

[Unreal Engine 4 —— Fortnite中的Asset Manager与资源控制](https://blog.csdn.net/noahzuo/article/details/78892664)

[【UE4】资源管理之UAssetManager用法](https://zhuanlan.zhihu.com/p/129712105)

# 有关概念介绍

以下内容来自[Unreal Engine 4 —— Asset Manager介绍](https://blog.csdn.net/noahzuo/article/details/78815596)：

## 涉及到的类

`Assest`
Asset指的是在Content Browser中看到的那些物件。贴图，BP，音频和地图等都属于Asset.
`Asset Registry`
Asset Registry是资源注册表，其中存储了每个特定的asset的有用的信息。这些信息会在asset被储存的时候进行更新。
`Streamable Managers`
Streamable Managers负责进行读取物件并将其放在内存中。
`Primary Assets`
Primary Assets指的是在游戏中可以进行手动载入/释放的东西。包括地图文件以及一些游戏相关的物件，例如character classs或者inventory items.
`Secondary Assets`
Secondary Assets指的是其他的那些Assets了，例如贴图和声音等。这一类型的assets是根据Primary Assets来自动进行载入的。
`Asset Bundle`
Asset Bundle是一个Asset的列表，用于将一堆Asset在runtime的时候载入。
`Asset Manager`
Asset Manager是一个可选的全局单例类，用于管理primary assets和asset bundles，这些东西在runtime的时候很有用。
`Primary Assets`
Primary Asset指的是可以针对于UObject::GetPrimaryAssetId()返回一个有效的值的UObject。

对于默认的工程，所有在/Game/Maps路径下的Maps会被设为Primary Assets，而所有其他的assets如果需要设定为Primary Assets，都需要手动指定。

所有的Primary Assets是通过FPrimaryAssetId来进行引用的，FPrimaryAssetId拥有如下的属性：

`PrimaryAssetType`：这指的是一个用于描述物件的逻辑类型的名字，通常是基类的名字。例如，我们有两个继承自同一个本地类AWeapon的BPAWeaponRangedGrenade_C和AWeaponMeleeHammer_C，那么它们会有同样的PrimaryAssetType——"Weapon"
PrimaryAssetName：这指的是用于描述这个asset的名字。通常来说，这就是这个object的名字（short name），但是对于例如说maps来说，这个值就是对应的asset path。
PrimaryAssetType:PrimaryAssetName可以组成整个游戏实例中的asset的唯一的描述。当客户端在和服务端通信的时候，就可以通过这个字符串来确认某个物件。例如，"Weapon:BattleAxe_Tier2"本质上和"/Game/Items/Weapons/Axes/BattleAxe_Tier2.BattleAxe_Tier2_C"是一样的。
在FPrimaryAssetId中分别有两个FName的Tag，分别是PrimaryAssetTypeTag和PrimaryAssetNameTag。因此，当一个Primary Asset被保存了之后，就可以直接在Asset Registry中通过这两个Tag来找到这个Asset。

## 主资源、次资源和主资源标签

以下内容来自[官方文档 - AssetManager](https://docs.unrealengine.com/4.26/zh-CN/ProductionPipelines/AssetManagement/)：

从概念上来说，虚幻引擎 4 的资源管理系统将所有资源分为两类：**主资源** 和 **次资源**。资源管理器通过主资源的 [**主资源 ID**](https://docs.unrealengine.com/en-US/API/Runtime/CoreUObject/UObject/FPrimaryAssetId) 即可直接对其进行操作，调用 `GetPrimaryAssetId` 即可获得此 ID。为将特定 `UObject` 类构成的资源指定为主资源，覆盖 `GetPrimaryAssetId` 即可返回一个有效的 `FPrimaryAssetId` 结构。次资源不由资源管理器直接处理，但其被主资源引用或使用后引擎便会自动进行加载。默认只有 `UWorld` 资源（关卡）为主资源；所有其他资源均为次资源。为将次资源设为主资源，必须覆盖其类的 `GetPrimaryAssetId` 函数，返回一个有效的 `FPrimaryAssetId` 结构。

# 将次资源设置为主资源

下面部分内容来自[【UE4】资源管理之UAssetManager用法](https://zhuanlan.zhihu.com/p/129712105)

示例：

```c++
//.h
//定义资源类型
static const FPrimaryAssetType CharacterType = TEXT("Character");
//重写方法以获取主资产ID，GetPrimaryAssetId是在UObject就声明的抽象函数，所以不用担心父类没有这个抽象方法。
virtual FPrimaryAssetId GetPrimaryAssetId() const override;

//.cpp
FPrimaryAssetId AMyCharacter::GetPrimaryAssetId() const
{
	//AssetType是FPrimaryAssetType类型的变量
	return  FPrimaryAssetId(CharacterType,GetFName());
}
```

如果要将资源提升为PrimaryAsset，需要实现GetPrimaryAssetId方法。方法的返回值就是AssetManager对这个资源进行管理的标识符了。 返回FPrimaryAssetId对象时，需要传入AssetType和GetFName两个参数。 其中AssetType是FPrimaryAssetType的对象，而GetFName()方法返回的是资源对象名。这两个参数一起确定了这个资源的Id。 也就是说，我们的资源对象可以属于不同或相同的资源类型，按照资源类型对可以对PriamryAsset进行分类。

建议将CharacterType的定义放在一个全局的静态文件中，方便统一管理。

例如： 

```c++
#pragma once

#include "CoreMinimal.h"

namespace ResString
{
	//定义资源类型
	static const FPrimaryAssetType MyCharacterType = TEXT("Character");
	//资源名
	static const FName MyCharacterName = TEXT("MyCharacter"); 
}
```

# 注册资源

注册资源有两种方式，一种是在引擎里面注册，一种是在代码里面注册。

## 引擎里面注册

我们通过在C++中通过实现类的GetPrimaryAssetId方法对类进行了Id和Type的设置。如果我们想要在蓝图中对资源进行引用的话，还需要在项目设置里面对AssetType进行注册。换句话说，我们在C++中只是对AssetType进行声明和定义（因为这个AssetType的定义位置比较随意，虽然我们推荐定义在AssetManager，但实际任何位置，甚至直接返回TEXT都可以。），如果要让UE4去识别我们的AssetType，就需要对其进行注册。 首先打开ProjectSettings，然后找到AssetManager，在设置面板里面可以看到PrimaryAssetTypestoScan，这个变量就是对AssetType进行注册的地方了。参数说明请参考[官方文档](https://link.zhihu.com/?target=https%3A//docs.unrealengine.com/zh-CN/Engine/Basics/AssetsAndPackages/AssetManagement/index.html)。

![image-20211114101907554](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111141019018.png)

果然是Map才是主资源- -。

接下来添加我们自己的资源类型：

![image-20211114103211873](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111141032867.png)

可选的配置类型如下：

![image-20211114102220998](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111141022140.png)

这里需要注意的是数组元素里有一项是PrimaryAssetType，这项的值必须与注册的资源返回的Id参数AssetType的TEXT值一致。可能有点绕口，就是说PrimaryAssetType的值与此项注册的资源类在C++中赋值的AssetType一致。

## 代码里面注册

还是先看函数：

```c++
/** 
	 * Scans a list of paths and reads asset data for all primary assets of a specific type.
	 * If done in the editor it will load the data off disk, in cooked games it will load out of the asset registry cache
	 *
	 * @param PrimaryAssetType	Type of asset to look for. If the scanned asset matches GetPrimaryAssetType with this it will be added to directory
	 * @param Paths				List of file system paths to scan
	 * @param BaseClass			Base class of all loaded assets, if the scanned asset isn't a child of this class it will be skipped
	 * @param bHasBlueprintClasses	If true, the assets are blueprints that subclass BaseClass. If false they are base UObject assets
	 * @param bIsEditorOnly		If true, assets will only be scanned in editor builds, and will not be stored into the asset registry
	 * @param bForceSynchronousScan If true will scan the disk synchronously, otherwise will wait for asset registry scan to complete
	 * @return					Number of primary assets found
	 */
	virtual int32 ScanPathsForPrimaryAssets(FPrimaryAssetType PrimaryAssetType, const TArray<FString>& Paths, UClass* BaseClass, bool bHasBlueprintClasses, bool bIsEditorOnly = false, bool bForceSynchronousScan = true);

	/** Single path wrapper */
	virtual int32 ScanPathForPrimaryAssets(FPrimaryAssetType PrimaryAssetType, const FString& Path, UClass* BaseClass, bool bHasBlueprintClasses, bool bIsEditorOnly = false, bool bForceSynchronousScan = true);

```

参数：

扫描路径列表，读取指定类型的所有主资产的资产数据。

如果在编辑器中完成，它将从磁盘上加载数据，在打包的游戏中，它将从资产注册表缓存中加载数据。如果扫描资产匹配GetPrimaryAssetType这将被添加到目录。

`PrimaryAssetType`要查找的资产类型。如果扫描的资产与这个GetPrimaryAssetType匹配，它将被添加到目录。

`Paths`要扫描的文件系统路径列表。

`BaseClass`所有加载的资源的基类，如果扫描的资源不是这个类的子类，它将被跳过。

`bHasBlueprintClasses`如果为真，则资产是子类BaseClass的蓝图。如果为false，它们是基本UObject资产。

`bIsEditorOnly`如果为true，资产将只在编辑器构建中扫描，而不会存储到资产注册表中。

`bForceSynchronousScan`如果为true将同步扫描磁盘，否则将等待资产注册表扫描完成。

`return`发现的主要资产数量。

如希望直接在代码中注册主资源，则覆盖资源管理器类中的 StartInitialLoading 函数并从该处调用 ScanPathsForPrimaryAssets。因此，推荐您将所有同类型的主资源放入相同的子文件夹中。这将使资源查找和注册更为迅速。

观察代码可以发现是和手动在引擎里面注册时一样的。那么代码就好写了：

```c++
//扫描
UAssetManager::Get().ScanPathsForPrimaryAssets(ResString::MyCharacterType,TArray<FString>{"/Game/Core"},AMyCharacter::StaticClass(),true);
```

类似于UObjectLibrary：

```c++
UObjectLibrary* TextureOL;
//如果UObjectLibrary未被加载
if (!TextureOL)
{
	//加载一手，类型写自己想要的
	TextureOL = UObjectLibrary::CreateLibrary(UTexture2D::StaticClass(), false, false);
	//手动指定不要被GC回收
	TextureOL->AddToRoot();
}
//扫描路径
TextureOL->LoadAssetDataFromPath("/Game/UI/Images");
```

# 加载资源

资源管理器函数 `LoadPrimaryAssets`、`LoadPrimaryAsset` 和 `LoadPrimaryAssetsWithType` 可用于在适当的时间开始加载主资源。之后资源可通过 `UnloadPrimaryAssets`、`UnloadPrimaryAsset` 和 `UnloadPrimaryAssetsWithType` 进行卸载。使用这些加载函数时，可指定一个资源束列表。以此法进行加载将使资源管理器按以上描述的方式加载这些资源束应用的次资源。

```c++
	/** 
	 * Loads a list of Primary Assets. This will start an async load of those assets, calling callback on completion.
	 * These assets will stay in memory until explicitly unloaded.
	 * You can wait on the returned streamable request or poll as needed.
	 * If there is no work to do, returned handle will be null and delegate will get called before function returns.
	 *
	 * @param AssetsToLoad		List of primary assets to load
	 * @param LoadBundles		List of bundles to load for those assets
	 * @param DelegateToCall	Delegate that will be called on completion, may be called before function returns if assets are already loaded
	 * @param Priority			Async loading priority for this request
	 * @return					Streamable Handle that can be used to poll or wait. You do not need to keep this handle to stop the assets from being unloaded
	 */
	virtual TSharedPtr<FStreamableHandle> LoadPrimaryAssets(const TArray<FPrimaryAssetId>& AssetsToLoad, const TArray<FName>& LoadBundles = TArray<FName>(), FStreamableDelegate DelegateToCall = FStreamableDelegate(), TAsyncLoadPriority Priority = FStreamableManager::DefaultAsyncLoadPriority);

	/** Single asset wrapper */
	virtual TSharedPtr<FStreamableHandle> LoadPrimaryAsset(const FPrimaryAssetId& AssetToLoad, const TArray<FName>& LoadBundles = TArray<FName>(), FStreamableDelegate DelegateToCall = FStreamableDelegate(), TAsyncLoadPriority Priority = FStreamableManager::DefaultAsyncLoadPriority);

	/** Loads all assets of a given type, useful for cooking */
	virtual TSharedPtr<FStreamableHandle> LoadPrimaryAssetsWithType(FPrimaryAssetType PrimaryAssetType, const TArray<FName>& LoadBundles = TArray<FName>(), FStreamableDelegate DelegateToCall = FStreamableDelegate(), TAsyncLoadPriority Priority = FStreamableManager::DefaultAsyncLoadPriority);
```

示例代码：

```c++
void UWC_TestUI::OnBtnClickCommonBtn_AssetManager()
{
UAssetManager::Get().LoadPrimaryAsset(FPrimaryAssetId(ResString::MyCharacterType,ResString::MyCharacterName), TArray<FName>{ResString::MyCharacterName},FStreamableDelegate::CreateUObject(this,&UWC_TestUI::OnAssetManagerAsyncLoadCompleted));
}
```

同样的，资源加载完成了回有一个回调函数，写法和SteamableManager是一样的。

```c++
void UWC_TestUI::OnAssetManagerAsyncLoadCompleted()
{
	FPrimaryAssetId AssetId = FPrimaryAssetId(ResString::MyCharacterType, ResString::MyCharacterName);
	UObject * MyCharacterObj = UAssetManager::Get().GetPrimaryAssetObject(AssetId);
}
```

可以看到我们的资源是被成功加载了的：

![image-20211114195532418](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111141955391.png)

注意我这里加载的是角色蓝图，如果要生成Actor的话还是得拿到它的UClass再生成：

```c++
void UWC_TestUI::OnAssetManagerAsyncLoadCompleted()
{
	FPrimaryAssetId AssetId = FPrimaryAssetId(ResString::MyCharacterType, ResString::MyCharacterName);
	UObject * MyCharacterObj = UAssetManager::Get().GetPrimaryAssetObject(AssetId);
	UClass * MyCharacterClass = UAssetManager::Get().GetPrimaryAssetObjectClass<AMyCharacter>(AssetId);
	FVector Location(230.0,0,120);
	FRotator Rotation(0.0,0,0);
	GetWorld()->SpawnActor(MyCharacterClass,&Location,&Rotation);
}
```

# 卸载资源

一样的传入FPrimaryAssetId即可，这里就不作演示了。

```c++
/** 
	 * Unloads a list of Primary Assets that were previously Loaded.
	 * If the only thing keeping these assets in memory was a prior Load call, they will be freed.
	 *
	 * @param AssetsToUnload	List of primary assets to load
	 * @return					Number of assets unloaded
	 */
	virtual int32 UnloadPrimaryAssets(const TArray<FPrimaryAssetId>& AssetsToUnload);

	/** Single asset wrapper */
	virtual int32 UnloadPrimaryAsset(const FPrimaryAssetId& AssetToUnload);

	/** Loads all assets of a given type, useful for cooking */
	virtual int32 UnloadPrimaryAssetsWithType(FPrimaryAssetType PrimaryAssetType);
```

# AssetsRegistry

[官方文档 - Asset Registry](https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/Assets/Registry/)

编辑器如何发现资产以及如何在加载资产之前使其了解更多有关资产类型的信息。

该**资产的注册表**是异步收集有关卸载的资产信息为编辑负荷编辑子系统。此信息存储在内存中，因此编辑器可以创建资产列表而无需加载它们。该信息具有权威性，并且会随着内存中的资产更改或磁盘上的文件更改而自动保持最新。在**内容浏览器**是该系统的主要消费者，但它可能在任何地方编辑代码中使用。

## 获取资产列表

要按类形成资产列表，只需加载资产注册表模块，然后调用 `Module.Get().GetAssetsByClass()`

```c++
FAssetRegistryModule& AssetRegistryModule = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
TArray<FAssetData> AssetData;
const UClass* Class = UStaticMesh::StaticClass();
AssetRegistryModule.Get().GetAssetsByClass(Class->GetFName(), AssetData);
```

![image-20211114201338726](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111142013036.png)

![](https://sin998-blog-image.oss-cn-beijing.aliyuncs.com/images/202111142013032.png)

## 创建过滤器

`FARFilter`可以在调用时提供A`GetAssets()`以创建按多个条件过滤的资产列表。过滤器由多个组件组成：

+ 包裹名字
+ 包路径
+ 收藏
+ 班级
+ 标签/值对

一个组件可能有多个元素。如果资产满足**所有**组件，则资产通过过滤器。为了满足一个组件，资产必须匹配其中的**任何**元素。

例如，如果存在路径为 /Game/Meshes/BeachBall 的 StaticMesh 资源：

+ 如果过滤器仅包含 PackagePath ，则资产将通过`/Game/Meshes`。只有一个组件具有一个元素。
+ 如果过滤器包含 PackagePath`/Game/Meshes`和 Classes `UParticleSystem` **AND ，** 则资产将通过`UStaticMesh`。有两个组件，第一个有一个元素，第二个有两个。
+ 如果过滤器包含 PackagePath`/Game/Meshes`且**仅**包含 Class ，则资产将失败`UParticleSystem`。有两个组件，每个组件都有一个元素。
+ 如果过滤器包含 PackagePath`/Game/NotMeshes`和 Class ，则资产将失败`UStaticMesh`。此过滤器还使用两个组件，每个组件都有一个元素。

使用具有两个组件 Class 和 PackagePath 的过滤器的示例：

```c++
FAssetRegistryModule& AssetRegistryModule = FModuleManager::LoadModuleChecked<FAssetRegistryModule>("AssetRegistry");
TArray<FAssetData> AssetData;
FARFilter Filter;
Filter.Classes.Add(UStaticMesh::StaticClass());
Filter.PackagePaths.Add("/Game/Meshes");
AssetRegistryModule.Get().GetAssets(Filter, AssetData);
```

## 异步数据收集

资产注册表`UAsset`异步读取文件，并且在您请求它时可能没有所有资产的完整列表。如果您的编辑器代码需要完整列表，则资产注册表会在发现/创建、重命名或删除资产时提供委托回调。当资产注册表完成其初始搜索时，还有一个委托，这对许多系统都很有用。

您可以通过加载资产注册表模块来注册这些委托，然后使用中提供的功能`IAssetRegistry`：

```c++
/** Register/Unregister a callback for when assets are added to the registry */
virtual FAssetAddedEvent& OnAssetAdded() = 0;

/** Register/Unregister a callback for when assets are removed from the registry */
virtual FAssetRemovedEvent& OnAssetRemoved() = 0;

/** Register/Unregister a callback for when assets are renamed in the registry */
virtual FAssetRenamedEvent& OnAssetRenamed() = 0;

/** Register/Unregister a callback for when the asset registry is done loading files */
virtual FFilesLoadedEvent& OnFilesLoaded() = 0;

/** Register/Unregister a callback to update the progress of the background file load */
virtual FFileLoadProgressUpdatedEvent& OnFileLoadProgressUpdated() = 0;

/** Returns true if the asset registry is currently loading files and does not yet know about all assets */
virtual bool IsLoadingAssets() = 0;
```

# 特点

这种加载方式的特点是：

- 被认为是UObjectLibrary的替代品，从上面的实践来说确实很不错。
- 确实可以很精确的控制资源的饿加载和释放的时机。
- 这是被视为UObjectLibrary的替代品，从编码表现来说确实很不错。
- 配置非常给力精确。
- AssetsRegistry的存在也使得登记、获取、过滤、监听资产更为便捷。

# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`UE资源加载`。

