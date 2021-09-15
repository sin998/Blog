[TOC]

# 参考文章&&原文链接

官方文档：[点击前往](https://docs.unrealengine.com/4.26/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/IntroductionToCPP/UObject)。

原文链接：点击前往。

# UObject

## 创建

### 创建运行时动态

创建UE4中所有的类都继承于UObject，对UObject不使用new来直接创建，而使用UE4提供的工厂方法NewObject()来创建：

```c++
 UMyObject* MyObject = NewObject<UMyObject>(this);
```

NewObject()有多种重载方法，其中一个关键的参数为`Outer*`，表示这个对象的外部对象，通常可传this指针进去。

### 构造函数中创建

使用CreateDefaultSubobject()来创建，内部将this指针当作`Outer*`*传进去。*

```c++
MyObejct = CreateDefaultSubobject<UMyObject>(TEXT("MyObject"));
```

## 销毁

### 自动销毁

UObject及其派生 具有被 UE4 垃圾回收机制管理，因而当指向对象的指针为 `nullptr `后，将会被 UE4 自动回收掉：

```c++
Obj = NewObject<UItemObject>(this, TEXT("Obj")); 
Obj = nullptr;
```

### 主动销毁

**方式一：UObject::ConditionalBeginDestroy()**

```c++
Obj->ConditionalBeginDestroy(); 
Obj = nullptr;
```

异常步执行且对象在当前帧内持续有效，等待下次GC。

**方式二：MarkPendingKill()**标记为PendingKill，等待回收。指向此实例的指针将设置为NULL，并在下一次GC时删除。

```c++
Obj->MarkPendingKill(); 
Obj = nullptr;
```

IsPendingKill 判断是否处于 PendingKill 状态。

ClearPendingKill 清除 PendingKill 状态。

`Engine\Config\BaseEngine.ini `更改下面参数，设置销毁时间间隔：

```c++
gc.TimeBetweenPurgingPendingKillObjects=60
```

### 强制垃圾回收

```c++
GEngine->ForceGarbageCollection(true);
```

# Actor

## 运行时动态创建

运行时动态创建Actor使用SpawnActor方法，有多种重载方法。SpawnActor是World的方法，需要提供一个有效的Actor，一般的UObject都有GetWorld()方法，但是一般只有AActor或UActorComponent才会返回有效的World。

```c++
AMyActor* MyActor = GetWorld()->SpawnActor<AMyActor>();
```

## 销毁

```c++
MyActor->Destroy();
```

等待下次GC。

# ActorComponent

## 创建Actor的构造函数中创建

```c++
UActorComponent* MyActorComponent = CreateDefaultSubobject<UActorComponent>(TEXT("MyActorComponentName")); 
```

对于场景组件，可以依附在其它组件上：

```c++
MySceneComponent->AttachToComponent(RootComponent, FAttachmentTransformRules::KeepRelativeTransform);
```

## 动态创建

因为创建ActorComponent需要在Actor中注册，所以使用NewObject()动态创建的时候，需要将外部的Actor对象作为Outer传进去，并调用RegisterComponent()方法。

```c++
UActorComponent* MyActorComponent = New<UActorComponent>(MyActor); 
MyActorComponent->RegisterComponent();  
```

## 销毁组件

同Actor一样，可以手动调用DestroyComponent()方法。

```c++
MyActorComponent->DestoryComponent();
```

等待GC。

# 原生C++对象

对于不是继承自UObject的C++对象，则通过C++的方式创建即可。考虑到使用new容易造成内存泄漏，还是推荐使用智能指针的方式创建和管理对象，并使用UE4自己的智能指针。

```c++
TSharedPtr<FMyObjectType> NewSharedPtr = MakeShared<FMyObjectType>();
```

