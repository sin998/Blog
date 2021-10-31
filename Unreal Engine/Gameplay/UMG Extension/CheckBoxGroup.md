

.h：

```c++
#pragma once

#include "CoreMinimal.h"
#include "Components/Overlay.h"
#include "Components/CheckBox.h"
#include "CheckBoxGroup.generated.h"


DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnCheckBoxGroupStateChangedEvent, const TArray<UCheckBox*>&, ChildChangedArray);
/*
* 基于overlay容器实现的CheckBox分组管理容器。
* 实现同一组checkbox的check状态互斥。即同组中只有一个checkbox可被置为checked,会自动将别的box置为unchecked。
* 可以嵌套别的容器组件以控制布局。
* 当嵌套容器自身，效果同新分组。
*/
UCLASS()
class UCheckBoxGroup : public UPanelWidget
{
    GENERATED_BODY()
public:
    UCheckBoxGroup();

protected:
	TSharedPtr<class SOverlay> MyCheckGroupBox;

protected:
	// UWidget interface
	virtual TSharedRef<SWidget> RebuildWidget() override;
	virtual void OnWidgetRebuilt() override;
	// End of UWidget interface

	virtual void ReleaseSlateResources(bool bReleaseChildren) override;

	// UPanelWidget
	virtual UClass* GetSlotClass() const override;
	virtual void OnSlotAdded(UPanelSlot* Slot) override;
	virtual void OnSlotRemoved(UPanelSlot* Slot) override;
	// End UPanelWidget

#if WITH_EDITOR
	virtual const FText GetPaletteCategory() override;
#endif
protected:
    bool InitGroup();

    bool InitChild(UCheckBox* child);

	TArray<UCheckBox*> GetAllChildCheckBox(UPanelWidget* Parent);

    UFUNCTION(Category = "CheckBox Group")
    void OnStateChanged(bool bIsChecked);
public:
    UFUNCTION(BlueprintCallable, Category = "CheckBox Group")
    TArray<UCheckBox*> CheckChildsState();

	//在CheckGroup的根容器上添加Child
	UFUNCTION(BlueprintCallable, Category = "CheckBox Group")
	UOverlaySlot* AddChildToGroupPanel(UWidget* Content);

	//UFUNCTION(BlueprintCallable, Category = "CheckBox Group")
	bool SetChildState(const FName& Name, bool status);

	UFUNCTION(BlueprintCallable, Category = "CheckBox Group")
	bool SetChildState(UCheckBox* Child, bool status);

	//动态刷新下group，适用于代码动态插入checkbox的情况
	void FlushGroup();

	//广播组内哪个checkbox状态发生了改变
	UPROPERTY(BlueprintAssignable, Category = "CheckBox Group")
	FOnCheckBoxGroupStateChangedEvent _onStateChangedEvent;

private:
	//从逻辑上添加Child CheckBox(控件不一定在CheckGroupPanel的层级中，只是会被group管理)
	UE_DEPRECATED(4.21, L"理论上有需求，但是容易造成混乱，那就算了，废弃掉，思路留着.")
	UFUNCTION(BlueprintCallable, Category = "CheckBox Group")
	bool AddGroupChild(UCheckBox* child);
	//从逻辑上移除Child CheckBox(控件还在，只是不再被group管理)
	UFUNCTION(BlueprintCallable, Category = "CheckBox Group")
	bool RemoveGroupChild(UCheckBox* child);

protected:
	//是否允许checkbox手动点击切换为unchecked状态
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "CheckBox Group")
	bool bAllowSwitchOff;

	FScriptDelegate _onStateChangedScriptDelegate;

	UPROPERTY()
	UCheckBox* _lastCheckedChild;

	UPROPERTY()
	TArray<UCheckBox*> AllChild;

	UPROPERTY()
	TArray<UCheckBox*> ExternalChildren;
};
```

.cpp：

```c++
#include "CheckBoxGroup.h"
#include "Components/Overlay.h"
#include "Components/OverlaySlot.h"

UCheckBoxGroup::UCheckBoxGroup()
{
    bAllowSwitchOff = false;
    _lastCheckedChild = nullptr;
    AllChild.Reset();
    _onStateChangedScriptDelegate.BindUFunction(this, TEXT("OnStateChanged"));
    bCanHaveMultipleChildren = true;
}

void UCheckBoxGroup::OnWidgetRebuilt()
{
    if (GetChildrenCount() > 0)
    {
        InitGroup();
    }
}

bool UCheckBoxGroup::InitGroup()
{
    AllChild.Reset();
    AllChild = GetAllChildCheckBox(this);
    for (UCheckBox* Box : AllChild)
    {
        if (Box)
        {
            InitChild(Box);

            //！！！保存被选中的第一个checkbox，并把其他多余的被设为选中的checkbox置为未选中，防止蓝图中的错误配置！！！
            if (nullptr == _lastCheckedChild && Box->IsChecked())
            {
                _lastCheckedChild = Box;
            } else if (_lastCheckedChild != nullptr && Box->IsChecked())
            {
                Box->SetCheckedState(ECheckBoxState::Unchecked);
            }
        }
    }
	return true;
}

bool UCheckBoxGroup::InitChild(UCheckBox * child)
{
    if (nullptr == child)
        return false;

    child->OnCheckStateChanged.AddUnique(_onStateChangedScriptDelegate);
    return true;
}

void UCheckBoxGroup::OnStateChanged(bool bIsChecked)
{
    TArray<UCheckBox*> ChildChangedArray = CheckChildsState();
    if (0 != ChildChangedArray.Num())
    {
        _onStateChangedEvent.Broadcast(ChildChangedArray);
    }
}


void UCheckBoxGroup::ReleaseSlateResources(bool bReleaseChildren)
{
	Super::ReleaseSlateResources(bReleaseChildren);
    MyCheckGroupBox.Reset();
}

UClass* UCheckBoxGroup::GetSlotClass() const
{
	return UOverlaySlot::StaticClass();
}

void UCheckBoxGroup::OnSlotAdded(UPanelSlot* InSlot)
{
	// Add the child to the live canvas if it already exists
	if (MyCheckGroupBox.IsValid())
	{
		CastChecked<UOverlaySlot>(InSlot)->BuildSlot(MyCheckGroupBox.ToSharedRef());
	}
}

void UCheckBoxGroup::OnSlotRemoved(UPanelSlot* InSlot)
{
	// Remove the widget from the live slot if it exists.
	if (MyCheckGroupBox.IsValid() && InSlot->Content)
	{
		TSharedPtr<SWidget> Widget = InSlot->Content->GetCachedWidget();
		if (Widget.IsValid())
		{
            MyCheckGroupBox->RemoveSlot(Widget.ToSharedRef());
		}
	}
}

#if WITH_EDITOR
const FText UCheckBoxGroup::GetPaletteCategory()
{
	return NSLOCTEXT("UMGExtend_CheckGroup", "Panel", "Panel");
}
#endif

TSharedRef<SWidget> UCheckBoxGroup::RebuildWidget()
{
    MyCheckGroupBox = SNew(SOverlay);
	for (UPanelSlot* PanelSlot : Slots)
	{
		if (UOverlaySlot* TypedSlot = Cast<UOverlaySlot>(PanelSlot))
		{
			TypedSlot->Parent = this;
			TypedSlot->BuildSlot(MyCheckGroupBox.ToSharedRef());
		}
	}

#if WITH_EDITOR
	FText ErrorText;
	if (Slots.Num() == 0)
	{
		ErrorText = NSLOCTEXT("UMGExtend_CheckGroup", "CheckBoXGroupTip", 
            "可直接在group下挂checkbox，也可以嵌套别的\n容器用来布局（Group本身是一个Overlay容器）");
        //You can put CheckBox into the GroupPanel which is as Overlay,\n or you can set a WidgetPanel to  the GroupPanel for layout
        MyCheckGroupBox.Reset();
		
        return SNew(STextBlock)
			.Text(ErrorText);
    }
#endif
	return MyCheckGroupBox.ToSharedRef();
}

TArray<UCheckBox*> UCheckBoxGroup::GetAllChildCheckBox(UPanelWidget* Parent)
{
    TArray<UCheckBox*> ChildList;
	int childCnt = Parent->GetChildrenCount();
	for (int i = 0; i < childCnt; ++i)
	{
		UWidget* Child = Parent->GetChildAt(i);
		if (Cast<UCheckBoxGroup>(Child))
		{
			continue;
		}
		if (Cast<UCheckBox>(Child))
		{
            ChildList.Add(Cast<UCheckBox>(Child));
		}
		else if (Cast<UPanelWidget>(Child))
		{
            TArray<UCheckBox*> ChildChildList = GetAllChildCheckBox(Cast<UPanelWidget>(Child));//递归检查子节点
            if (ChildChildList.Num()>0)
                ChildList.Append(ChildChildList);
		}
		else if (Cast<UUserWidget>(Child)) // 10.18添加子控件对userwidget的支持。
		{
			UWidget* Temp = Cast<UUserWidget>(Child)->GetRootWidget();
			if (Cast<UPanelWidget>(Temp))
			{
				TArray<UCheckBox*> ChildChildList = GetAllChildCheckBox(Cast<UPanelWidget>(Temp));
				if (ChildChildList.Num() > 0)
					ChildList.Append(ChildChildList);
			}
		}
	}
	return ChildList;
}

TArray<UCheckBox*> UCheckBoxGroup::CheckChildsState()
{
    TArray<UCheckBox*> ChildChangedArray;
	int childCnt = AllChild.Num();
	UCheckBox* newLastCheckedChild = nullptr;
	UCheckBox* firstCheckedChild = nullptr;
	bool hasLastChecked = false;
	bool hasOtherChecked = false;

	for (UCheckBox* childWidget : AllChild)
	{
        if(nullptr == childWidget) break;
		
		if (nullptr == firstCheckedChild)//保存第一个
            firstCheckedChild = childWidget;

        if (childWidget->IsChecked())
        {
            if (childWidget != _lastCheckedChild)
			{
				if (!hasOtherChecked)
				{
					newLastCheckedChild = childWidget;
					hasOtherChecked = true;
				}
                else
                {   //uncheck childWidget,只保留一个选择项
                    childWidget->SetCheckedState(ECheckBoxState::Unchecked);
                }
				ChildChangedArray.Add(childWidget);
			}
            else
            {
                hasLastChecked = true;
            }
        }
    }



    if (hasOtherChecked)
    {   //uncheck _lastChild
        if (hasLastChecked && nullptr != _lastCheckedChild)
        {
			_lastCheckedChild->SetCheckedState(ECheckBoxState::Unchecked);
			ChildChangedArray.Add(_lastCheckedChild);//原有选择项被改变
        }
		_lastCheckedChild = newLastCheckedChild;
	}
    else
    {
        if (!bAllowSwitchOff)
        {
            if (nullptr != _lastCheckedChild)
            {
                if (!hasLastChecked)
                {
                    _lastCheckedChild->SetCheckedState(ECheckBoxState::Checked);//还原，保留选择项
                }
			}
			else
            {
                //选择第一个作为默认的选择项
                if (nullptr != firstCheckedChild)//选择第一个
                {
                    _lastCheckedChild = firstCheckedChild;
                    _lastCheckedChild->SetCheckedState(ECheckBoxState::Checked);
                    ChildChangedArray.Add(_lastCheckedChild);
                }
            }
		}
		else
        {
            if (nullptr != _lastCheckedChild && 0 == ChildChangedArray.Num())
            {   //没有一个被选择且改变的情况，肯定是最后一个被uncheck了
                ChildChangedArray.Add(_lastCheckedChild);
            }
        }
    }
    return ChildChangedArray;
}

UOverlaySlot* UCheckBoxGroup::AddChildToGroupPanel(UWidget* Content)
{
    return Cast<UOverlaySlot>(AddChild(Content));
}


bool UCheckBoxGroup::AddGroupChild(UCheckBox* child)
{
	if (nullptr == child)
	{
		return false;
	}
    ExternalChildren.AddUnique(child);
	bool bInit = InitChild(child);
	if (!bInit)
	{
        RemoveGroupChild(child);
		return false;
	}
	return true;
}

bool UCheckBoxGroup::RemoveGroupChild(UCheckBox* child)
{
	if (nullptr == child)
	{
		return false;
	}
	ExternalChildren.Remove(child);
    return true;
}

bool UCheckBoxGroup::SetChildState(const FName& Name, bool status)
{
    TArray<UCheckBox*> ChildChangedArray;
    UCheckBox* findCheckedChild = nullptr;
    
    for (UCheckBox* childWidget : AllChild)
    {
        if (nullptr != childWidget && childWidget->GetName() == Name.ToString())
        {
            findCheckedChild = childWidget;
            break;
        }
    }
    if (nullptr == findCheckedChild)
    {
        return false;
    }

    return SetChildState(findCheckedChild, status);
}

bool UCheckBoxGroup::SetChildState(UCheckBox* Child, bool status)
{
	TArray<UCheckBox*> ChildChangedArray;
	UCheckBox* findCheckedChild = Child;

	if (nullptr == findCheckedChild)
	{
		return false;
	}
	if (findCheckedChild->IsChecked() != status)
	{
		findCheckedChild->SetCheckedState(status ? ECheckBoxState::Checked : ECheckBoxState::Unchecked);
		ChildChangedArray.Add(findCheckedChild);
	}

	if (status)
	{
		_lastCheckedChild = findCheckedChild;
		for (UCheckBox* childWidget : AllChild)
		{
			if (nullptr != childWidget)
			{
				if (childWidget != findCheckedChild && childWidget->IsChecked())
				{
					childWidget->SetCheckedState(ECheckBoxState::Unchecked);
					ChildChangedArray.Add(childWidget);
				}
			}
		}
	}
	else
	{
		TArray<UCheckBox*> changedArray = CheckChildsState();
		//移除合并时可能重复的项
		int findIndex = ChildChangedArray.Find(findCheckedChild);
		if (INDEX_NONE != findIndex)
		{
			findIndex = changedArray.Find(findCheckedChild);
			if (INDEX_NONE != findIndex)
			{
				changedArray.RemoveAt(findIndex);
			}
		}
		//合并
		ChildChangedArray.Append(changedArray);
	}

	if (0 != ChildChangedArray.Num())
	{//广播改变
		_onStateChangedEvent.Broadcast(ChildChangedArray);
	}
	return true;
}


void UCheckBoxGroup::FlushGroup()
{
	_lastCheckedChild = nullptr;
	if (GetChildrenCount() > 0)
	{
		InitGroup();
	}
}
```

usage：

在蓝图中拖出这个控件，然后在下面放置若干CheckBox即可。

可以在蓝图中直接设置其中一个CheckBox为true，其他的为false。若有多个CheckBox被设置为true，则只有最后一个是true。

若是要在C++中控制，则需要此代码：`CBG->SetChildState(CB_TabAll,true);`。其中，`CBG`是CheckBoxGroup，`CB_TabAll`是一个CheckBox，`true`表示该CheckBox被选中，其他的不选中。

注意：您可以在C++中使用`CBG->SetChildState(CB_TabAll,true);`初始化CheckBox，使他们某一个为true。值得注意的是这行代码只走表现，不走逻辑。即只是改变了CheckBox的表现为Checked，但是绑定的事件不会执行。您还需要手动调用一下代码，例如更新一下界面什么的：

`UpdateMenuState(MenuState);`。

例如您的`CB_TallAll`的代码是这样的：

```c++
void UWG_Test::OnStateChange_TabAll(bool bIsChecked)
{
	if (bIsChecked && MenuState != EMenuFilter::EAll)
	{
		UpdateMenuState(EMenuFilter::EAll);
	}
}
```







