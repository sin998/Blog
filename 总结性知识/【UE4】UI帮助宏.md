```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "GFMacroTool.h"


/*
* 帮助宏，用来绑定widget控件的点击等事件，便于统一处理点击等音效（改源码也能实现，这里不想污染源码，免得升级版本麻烦）
* 用法：默认点击音效 = BIND_CLICK(UWG_OneTeam,btn_team,&UWG_OneTeam::OnClicked) 
*     自定义覆盖音效 = BIND_CLICK(UWG_OneTeam,btn_team,&UWG_OneTeam::OnClicked, &UWG_OneTeam::PlayMySound)
*/
// Button的点击和悬浮等。
#define BIND_CLICK(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_BIND_CLICK_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)
#define REMOVE_CLICK(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_REMOVE_CLICK_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)

#define BIND_HOVER(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_BIND_HOVER_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)
#define REMOVE_HOVER(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_REMOVE_HOVER_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)

#define BIND_PRESS(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_BIND_PRESS_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)
#define REMOVE_PRESS(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_REMOVE_PRESS_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)

//button release 和 unhover一般不会播音效，先做普通处理，后续需要在加。
#define BIND_RELEASE(CLASS,WIDGET,FUNCTION) WIDGET->OnReleased.AddDynamic(this,FUNCTION);
#define REMOVE_RELEASE(CLASS,WIDGET,FUNCTION) WIDGET->OnReleased.RemoveDynamic(this,FUNCTION);

#define BIND_UNHOVER(CLASS,WIDGET,FUNCTION) WIDGET->OnUnhovered.AddDynamic(this,FUNCTION);
#define REMOVE_UNHOVER(CLASS,WIDGET,FUNCTION) WIDGET->OnUnhovered.RemoveDynamic(this,FUNCTION);

//CheckBox 只有状态变更，可当做点击使用，没有悬浮接口，想要悬浮效果可盖一个button在上。
#define BIND_CHECK(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_BIND_CHECK_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)
#define REMOVE_CHECK(CLASS,WIDGET,...) VORTEX_CONCAT(INNER_REMOVE_CHECK_, GET_ARG_COUNT(__VA_ARGS__))(CLASS,WIDGET,__VA_ARGS__)

//==================================================================================



/*
* Inner macros:内部中转宏，默认的音效播放方法在wg_base基类里实现
*/
//button click
#define INNER_BIND_CLICK_1(CLASS,WIDGET,FUNC1) INNER_BIND_CLICK_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayClickSound)
#define INNER_BIND_CLICK_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnClicked.AddDynamic(this,FUNC1);\
	WIDGET->OnClicked.AddDynamic(this,FUNC2);
	
#define INNER_REMOVE_CLICK_1(CLASS,WIDGET,FUNC1) INNER_REMOVE_CLICK_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayClickSound)
#define INNER_REMOVE_CLICK_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnClicked.RemoveDynamic(this,FUNC1);\
	WIDGET->OnClicked.RemoveDynamic(this,FUNC2);
//button hover
#define INNER_BIND_HOVER_1(CLASS,WIDGET,FUNC1) INNER_BIND_HOVER_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayHoverSound)
#define INNER_BIND_HOVER_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnHovered.AddDynamic(this,FUNC1);\
	WIDGET->OnHovered.AddDynamic(this,FUNC2);

#define INNER_REMOVE_HOVER_1(CLASS,WIDGET,FUNC1) INNER_REMOVE_HOVER_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayHoverSound)
#define INNER_REMOVE_HOVER_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnHovered.RemoveDynamic(this,FUNC1);\
	WIDGET->OnHovered.RemoveDynamic(this,FUNC2);
//button press
#define INNER_BIND_PRESS_1(CLASS,WIDGET,FUNC1) INNER_BIND_PRESS_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayPressSound)
#define INNER_BIND_PRESS_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnPressed.AddDynamic(this,FUNC1);\
	WIDGET->OnPressed.AddDynamic(this,FUNC2);

#define INNER_REMOVE_PRESS_1(CLASS,WIDGET,FUNC1) INNER_REMOVE_PRESS_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayPressSound)
#define INNER_REMOVE_PRESS_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnPressed.RemoveDynamic(this,FUNC1);\
	WIDGET->OnPressed.RemoveDynamic(this,FUNC2);

//checkbox state change
#define INNER_BIND_CHECK_1(CLASS,WIDGET,FUNC1) INNER_BIND_CHECK_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayCheckSound)
#define INNER_BIND_CHECK_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnCheckStateChanged.AddDynamic(this,FUNC1);\
	WIDGET->OnCheckStateChanged.AddDynamic(this,FUNC2);

#define INNER_REMOVE_CHECK_1(CLASS,WIDGET,FUNC1) INNER_REMOVE_CHECK_2(CLASS,WIDGET,FUNC1,&CLASS::OnPlayCheckSound)
#define INNER_REMOVE_CHECK_2(CLASS,WIDGET,FUNC1,FUNC2) \
	WIDGET->OnCheckStateChanged.RemoveDynamic(this,FUNC1);\
	WIDGET->OnCheckStateChanged.RemoveDynamic(this,FUNC2);







///////////////////////////////////  为所以UWidget控件加鼠标的hover等事件  ///////////////////////////////////////////////////////
//UE4原生只有少数几种控件可以简单直接的监听鼠标hover等事件，如 button。其他控件要想获得这种事件需要从SWidgt里面去拿。 参考SWidget.h//
//用法 ： SET_ONMOUSE_ENTER(UCheckBoxObj, OnBoxMouseEnter);  传入 UWidget控件对象和回调方法名即可。

#define SET_ONMOUSE_ENTER(WIDGET,FUNCTION_NAME) \
	{ \
		TSharedPtr<SWidget> SafeWidget = WIDGET->GetCachedWidget();\
		if ( SafeWidget.IsValid() ) \
		{\
			SafeWidget->SetOnMouseEnter( FNoReplyPointerEventHandler::CreateLambda([this](const FGeometry& Geometry,const FPointerEvent& PointEvent)->void \
				{\
					this->FUNCTION_NAME();\
				})\
			); \
		}\
	}


#define SET_ONMOUSE_LEAVE(WIDGET,FUNCTION_NAME) \
	{ \
		TSharedPtr<SWidget> SafeWidget = WIDGET->GetCachedWidget();\
		if ( SafeWidget.IsValid() ) \
		{\
			SafeWidget->SetOnMouseLeave( FSimpleNoReplyPointerEventHandler::CreateLambda([this](const FPointerEvent& PointEvent)->void \
				{\
					this->FUNCTION_NAME();\
				})\
			); \
		}\
	}


```

