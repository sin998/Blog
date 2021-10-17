[TOC]



#  【UE4】UE4中传递回调函数的方式

# 参考文章&&原文链接

[CSDN - 解析ue4的TFunction模板类](https://blog.csdn.net/qq_21919621/article/details/108553258)

[菜鸟教程 - 函数指针](https://www.runoob.com/cprogramming/c-fun-pointer-callback.html)

[C语言中文网 - C语言函数指针](http://c.biancheng.net/view/2023.html)

[CSDN - 一文搞懂C语言回调函数](https://blog.csdn.net/baidu_38172402/article/details/95119014)

[知乎 - c++回调函数的实现](https://zhuanlan.zhihu.com/p/83943973)

# 一个通用弹窗

假如您的游戏里面需要一些弹窗界面，里面或许包括一个标题、提示内容，再加上一个确定按钮和一个取消按钮。这做起来很简单，在UMG里面稍微思考一下布局，把这些控件拖出来进行合理的排版和控制，然后再到C++中绑定控件和点击按钮的点击事件即可。

这看起来好像就完成了，但是我们忽略了一个事情。我们的弹窗实际上在游戏中可能不仅仅是起到一个提示作用，更是要真真切切的做一些事情的，例如我希望用户在点击确定按钮的时候并不是简单的关闭弹窗，我们是需要能执行一些C++代码，像这样：

```C++
void UTestMainUI::OnBtnClick_Comfirm()
{
	//do something...
}
```

这个`//do something...`的部分就是我希望调用的方法，这个方法肯定是传递进来的。那么问题是我应该如何传递进来？

TFunction 函数指针 表达式 委托 std::function

# 解决办法

我们可以先定义一个函数指针，然后在何时的时候将它传进来保存，最后再想要的地方调用即可。即在初始化的时候把想要执行的函数传进来，然后在点击确认按钮的时候就调用这个函数，这样就实现了效果。

在UE4中有很多东西我们可以使用。

它们分别是：函数指针（C语言）、Lambda表达式、std::function（C++）、TFunction（UE4）、委托（UE4）。

下面将会分别介绍它们的使用方法和具体的应用场景。

# TFunction

这是UE4里面的，用起来是最简单的。使用方法：

> ```
> /**
>  * TFunction<FuncType>
>  *
>  * A class which represents a copy of something callable.  FuncType represents a function type and so
>  * TFunction should be defined as follows:
>  *
>  * // A function taking a string and float and returning int32.  Parameter names are optional.
>  * TFunction<int32 (const FString& Name, float Scale)>
>  *
>  * Unlike TFunctionRef, this object is intended to be used like a UE4 version of std::function.  That is,
>  * it takes a copy of whatever is bound to it, meaning you can return it from functions and store them in
>  * objects without caring about the lifetime of the original object being bound.
>  *
>  * Example:
>  *
>  * // Something.h
>  * TFunction<FString (int32)> GetTransform();
>  *
>  * // Something.cpp
>  * TFunction<FString (int32)> GetTransform(const FString& Prefix)
>  * {
>  *     // Squares number and returns it as a string with the specified prefix
>  *     return [=](int32 Num) {
>  *         return Prefix + TEXT(": ") + TTypeToString<int32>::ToString(Num * Num);
>  *     };
>  * }
>  *
>  * // SomewhereElse.cpp
>  * #include "Something.h"
>  *
>  * void Func()
>  * {
>  *     TFunction<FString (int32)> Transform = GetTransform(TEXT("Hello"));
>  *
>  *     FString Result = Transform(5); // "Hello: 25"
>  * }
>  */
> ```

TFunction声明：

```c++
private:
	TFunction<void()> TFunConfirm;
```

设置函数的方法声明：

```c++
public:
	void BindFunConfirm(TFunction<void()> TargetFun);
```

设置函数的方法定义：

```c++
void UWG_CommonDialog::BindFunConfirm(TFunction<void()> TargetFun)
{
	TFunConfirm = TargetFun;
}
```

点击确定按钮的方法定义：

```c++
void UWG_CommonDialog::OnClickBtn_Comfirm()
{
	if (TFunConfirm != NULL)
	{
		TFunConfirm();
	}
}
```

示例调用：

```c++
UWG_CommonDialog* dialog;
//use lambda expression
dialog->BindFunConfirm([this]() {OnClickTestComfire();});
dialog->Show();
```

这里的`OnClickTestComfire()`的原型是：

```c++
void OnClickTestComfire();
```

# std::function









# 函数指针

在C语言中，我们有函数指针可以使用。它的定义很简单：`函数指针就是一个指向函数的指针`。

以下内容来自于[C语言中文网 - C语言函数指针](http://c.biancheng.net/view/2023.html)：

> 一个函数总是占用一段连续的内存区域，函数名在表达式中有时也会被转换为该函数所在内存区域的首地址，这和数组名非常类似。我们可以把函数的这个首地址（或称入口地址）赋予一个[指针](http://c.biancheng.net/c/80/)变量，使指针变量指向函数所在的内存区域，然后通过指针变量就可以找到并调用该函数。这种指针就是函数指针。
>
> 函数指针的定义形式为：
>
> `returnType (*pointerName)(param list);`
>
> returnType 为函数返回值类型，pointerName 为指针名称，param list 为函数参数列表。参数列表中可以同时给出参数的类型和名称，也可以只给出参数的类型，省略参数的名称，这一点和函数原型非常类似。
>
> 注意`( )`的优先级高于`*`，第一个括号不能省略，如果写作`returnType *pointerName(param list);`就成了函数原型，它表明函数的返回值类型为`returnType *`。
>
> 【实例】用指针来实现对函数的调用。
>
> ```c
> #include <stdio.h>
> 
> //返回两个数中较大的一个
> int max(int a, int b){
>     return a>b ? a : b;
> }
> 
> int main(){
>     int x, y, maxval;
>     //定义函数指针
>     int (*pmax)(int, int) = max;  //也可以写作int (*pmax)(int a, int b)
>     printf("Input two numbers:");
>     scanf("%d %d", &x, &y);
>     maxval = (*pmax)(x, y);
>     printf("Max value: %d\n", maxval);
> 
>     return 0;
> }
> ```
>
> 运行结果：
> Input two numbers:10 50↙
> Max value: 50
>
> 第 14 行代码对函数进行了调用。pmax 是一个函数指针，在前面加 * 就表示对它指向的函数进行调用。注意`( )`的优先级高于`*`，第一个括号不能省略。

最终的代码如下。

`void Test();`声明：

```C++
void (* Test())();
```

`void Test();`定义：

```c++
void (* UTestMainUI::Test())()
{
    //do something...
	return nullptr;
}
```

函数指针声明：

```C++
private:
	void (*ComfirmFunc)() = nullptr;
```

设置函数指针的方法声明：

```C++
public:
	void SetComfirmFunc(void (*InComfirmFunc)());
```

设置函数指针的方法定义：

```C++
void UTestMainUI::SetComfirmFunc(void(* InComfirmFunc)())
{
	if (InComfirmFunc != nullptr)
	{
		ComfirmFunc = InComfirmFunc;
	}
}
```

在点击确定按钮时：

```c++
void UTestMainUI::OnBtnClick_ChangeTab()
{
	if (ComfirmFunc != nullptr)
	{
		ComfirmFunc();
	}
}
```

这个写法有些奇怪，`void Test();`返回了一个指针，但是我们不用管它，只做自己的事情就好。



# 本文标签

`游戏开发`、`游戏开发基础`、`Unreal Engine`、`总结性知识`、`UE总结性知识`。
