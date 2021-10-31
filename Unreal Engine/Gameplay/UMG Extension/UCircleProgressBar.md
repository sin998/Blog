

CircleProgressBar.h

```C++
#pragma once

#include "CoreMinimal.h"
#include "UObject/ObjectMacros.h"
#include "Styling/SlateTypes.h"
#include "Widgets/SWidget.h"
#include "CircleProgressBarWidgetStyle.h"
#include "SCircleProgressBar.h"
#include "Components/Widget.h"
#include "CircleProgressBar.generated.h"

class USlateBrushAsset;

/**
 *  ProgressBarEx
 */
UCLASS()
class UCircleProgressBar : public UWidget
{
	GENERATED_BODY()

public:

	/** The progress bar ex default setting */
	UCircleProgressBar(const FObjectInitializer& ObjectInitializer = FObjectInitializer::Get());

	/** The progress bar ex style */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Style", meta = (DisplayName = "Style"))
	FCircleProgressBarStyle WidgetStyle;

	/** Style used for the progress bar */
	UPROPERTY()
	USlateWidgetStyleAsset* Style_DEPRECATED;

	/** The brush to use as the background of the progress bar */
	UPROPERTY()
	USlateBrushAsset* BackgroundImage_DEPRECATED;

	/** The brush to use as the fill image */
	UPROPERTY()
	USlateBrushAsset* FillImage_DEPRECATED;

	/** Used to determine the fill position of the progress bar ranging 0..1 */
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Progress, meta = (UIMin = "0", UIMax = "1"))
	float Percent;

	/** Defines if this progress bar clip ClockWise or Anti-ClockWise */
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Progress)
	TEnumAsByte<ECircleProgressBarFillType::Type> BarFillType;

	/** A bindable delegate to allow logic to drive the text of the widget */
	UPROPERTY()
	FGetFloat PercentDelegate;

	/** Fill Color and Opacity */
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = Appearance)
	FLinearColor FillColorAndOpacity;

	/** */
	UPROPERTY()
	FGetLinearColor FillColorAndOpacityDelegate;

public:
	UFUNCTION(BlueprintCallable, Category = "Progress")
	void SetPercent(float InPercent);

	UFUNCTION(BlueprintCallable, Category = "Progress")
	void SetFillColorAndOpacity(FLinearColor InColor);

	//TODO UMG Add Set BarFillType.

public:

	//~ Begin UWidget Interface
	virtual void SynchronizeProperties() override;
	//~ End UWidget Interface

	//~ Begin UVisual Interface
	virtual void ReleaseSlateResources(bool bReleaseChildren) override;
	//~ End UVisual Interface

	//~ Begin UObject Interface
	virtual void PostLoad() override;
	//~ End UObject Interface

#if WITH_EDITOR

	//~ Begin UWidget Interface
	virtual const FText GetPaletteCategory() override;
	virtual void OnCreationFromPalette() override;
	//~ End UWidget Interface

#endif

protected:

	/** Native Slate Widget */
	TSharedPtr<SCircleProgressBar> CircleProgressBar;

	//~ Begin UWidget Interface
	virtual TSharedRef<SWidget> RebuildWidget() override;
	//~ End UWidget Interface

	PROPERTY_BINDING_IMPLEMENTATION(FSlateColor, FillColorAndOpacity);
};

```



CircleProgressBar.cpp

```c++
#pragma once

#include "CircleProgressBar.h"
#include "UMG.h"
#include "UMGStyle.h"
#include "Slate/SlateBrushAsset.h"

#define LOCTEXT_NAMESPACE "UMG"

/////////////////////////////////////////////////////
UCircleProgressBar::UCircleProgressBar(const FObjectInitializer& ObjectInitializer /*= FObjectInitializer::Get()*/)
	: Super(ObjectInitializer)
{
	BarFillType = ECircleProgressBarFillType::ClockWise;
	Percent = 0;
	FillColorAndOpacity = FLinearColor::White;
}


void UCircleProgressBar::SetFillColorAndOpacity(FLinearColor Color)
{
	FillColorAndOpacity = Color;
	if (CircleProgressBar.IsValid())
	{
		CircleProgressBar->SetFillColorAndOpacity(FillColorAndOpacity);
	}
}

void UCircleProgressBar::SetPercent(float InPercent)
{
	Percent = InPercent;
	if (CircleProgressBar.IsValid())
	{
		CircleProgressBar->SetPercent(InPercent);
	}
}

//ͬ����������
void UCircleProgressBar::SynchronizeProperties()
{
	Super::SynchronizeProperties();

	TAttribute< TOptional<float> > PercentBinding = OPTIONAL_BINDING_CONVERT(float, Percent, TOptional<float>, ConvertFloatToOptionalFloat);
	TAttribute<FSlateColor> FillColorAndOpacityBinding = PROPERTY_BINDING(FSlateColor, FillColorAndOpacity);

	SCircleProgressBar::FArguments SlateDefaults;
	CircleProgressBar->SetBackgroundImage(SlateDefaults._BackgroundImage);
	CircleProgressBar->SetFillImage(SlateDefaults._FillImage);
	CircleProgressBar->SetStyle(&WidgetStyle);
	CircleProgressBar->SetBarFillType(BarFillType);
	CircleProgressBar->SetPercent(PercentBinding);
	CircleProgressBar->SetFillColorAndOpacity(FillColorAndOpacityBinding);
}

void UCircleProgressBar::PostLoad()
{
	Super::PostLoad();

	if (GetLinkerUE4Version() < VER_UE4_DEPRECATE_UMG_STYLE_ASSETS)
	{
		if (Style_DEPRECATED != nullptr)
		{
			const FCircleProgressBarStyle* StylePtr = Style_DEPRECATED->GetStyle<FCircleProgressBarStyle>();
			if (StylePtr != nullptr)
			{
				WidgetStyle = *StylePtr;
			}

			Style_DEPRECATED = nullptr;
		}

		if (BackgroundImage_DEPRECATED != nullptr)
		{
			WidgetStyle.BackgroundImage = BackgroundImage_DEPRECATED->Brush;
			BackgroundImage_DEPRECATED = nullptr;
		}

		if (FillImage_DEPRECATED != nullptr)
		{
			WidgetStyle.FillImage = FillImage_DEPRECATED->Brush;
			FillImage_DEPRECATED = nullptr;
		}
	}
}


void UCircleProgressBar::ReleaseSlateResources(bool bReleaseChildren)
{
	Super::ReleaseSlateResources(bReleaseChildren);

	CircleProgressBar.Reset();
}


TSharedRef<SWidget> UCircleProgressBar::RebuildWidget()
{
	CircleProgressBar = SNew(SCircleProgressBar)
		.BorderPadding(FVector2D(0.0f, 0.0f));

	return CircleProgressBar.ToSharedRef();
}


#if WITH_EDITOR
//������������
const FText UCircleProgressBar::GetPaletteCategory()
{
	return LOCTEXT("Common", "Common");
}

void UCircleProgressBar::OnCreationFromPalette()
{
	FillColorAndOpacity = FLinearColor(0, 0.5f, 1.0f);
}

#endif
```



UCircleProgressBarWidgetStyle.h

```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "Styling/SlateWidgetStyleContainerBase.h"
#include "FCircleProgressBarStyle.h"
#include "CircleProgressBarWidgetStyle.generated.h"

/**
 *
 */
UCLASS(BlueprintType, hidecategories = Object)
class UCircleProgressBarWidgetStyle : public USlateWidgetStyleContainerBase
{
	GENERATED_BODY()

public:

	/** The actual data describing the progress's appearance. */
	UPROPERTY(Category = "Style", EditAnywhere, BlueprintReadWrite, meta = (ShowOnlyInnerProperties))
	FCircleProgressBarStyle CircleProgressBarStyle;

	virtual const struct FSlateWidgetStyle* const GetStyle() const override
	{
		return static_cast<const struct FSlateWidgetStyle*>(&CircleProgressBarStyle);
	}
};

```

.cpp无内容



FCircleProgressBarStyle.h

```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "Components/Widget.h"
#include "StyleManager/StyleManager.h"
#include "Styling/SlateWidgetStyleContainerBase.h"
#include "Styling/SlateWidgetStyle.h"
#include "SlateBasics.h"
#include "FCircleProgressBarStyle.generated.h"


/**
 * Represents the appearance of an SCircleProgressBarStyle
 */
USTRUCT(BlueprintType)
struct FCircleProgressBarStyle : public FSlateWidgetStyle
{
	GENERATED_USTRUCT_BODY()

public:
	FCircleProgressBarStyle();

	virtual ~FCircleProgressBarStyle() {}

	virtual void GetResources(TArray< const FSlateBrush* >& OutBrushes) const override;

	static const FName TypeName;
	virtual const FName GetTypeName() const override { return TypeName; };

	static const FCircleProgressBarStyle& GetDefault();

	static const void Add()
	{
		FStyleManager::Add<FCircleProgressBarStyle>("CircleProgressBar", GetDefault());
	}

	/** Background image to use for the progress bar */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Appearance)
	FSlateBrush BackgroundImage;

	FCircleProgressBarStyle& SetBackgroundImage(const FSlateBrush& InBackgroundImage) { BackgroundImage = InBackgroundImage; return *this; }

	/** Foreground image to use for the progress bar */
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = Appearance)
	FSlateBrush FillImage;

	FCircleProgressBarStyle& SetFillImage(const FSlateBrush& InFillImage) { FillImage = InFillImage; return *this; }

};

```

FCircleProgressBarStyle.cpp

```C++
// Fill out your copyright notice in the Description page of Project Settings.


#include "FCircleProgressBarStyle.h"

FCircleProgressBarStyle::FCircleProgressBarStyle()
{
}

void FCircleProgressBarStyle::GetResources(TArray< const FSlateBrush* >& OutBrushes) const
{
	OutBrushes.Add(&BackgroundImage);
	OutBrushes.Add(&FillImage);
}

const FName FCircleProgressBarStyle::TypeName(TEXT("FProgressBarExStyle"));

const FCircleProgressBarStyle& FCircleProgressBarStyle::GetDefault()
{
	//static bool HasAdd = false;
	//if (!HasAdd)
	//{
	//	HasAdd = true;
	//	Add();
	//}
	static FCircleProgressBarStyle Default;
	return Default;
}
```



SCircleProgressBar.h

```c++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "FCircleProgressBarStyle.h"

UENUM(BlueprintType)
namespace ECircleProgressBarFillType
{
	enum Type
	{
		ClockWise,
		AntiClockwise,
	};
}

/** A circle img progress extend bar widget.*/
class SCircleProgressBar : public SLeafWidget
{
public:
	SLATE_BEGIN_ARGS(SCircleProgressBar)
		: _Style(nullptr)
		, _BarFillType(ECircleProgressBarFillType::ClockWise)
		, _Percent(TOptional<float>())
		, _FillColorAndOpacity(FLinearColor::White)
		, _BorderPadding(FVector2D(1, 0))
		, _BackgroundImage(&FStyleManager::Get().GetWidgetStyle<FCircleProgressBarStyle>("CircleProgressBar").BackgroundImage)
		, _FillImage(&FStyleManager::Get().GetWidgetStyle<FCircleProgressBarStyle>("CircleProgressBar").FillImage)
		, _RefreshRate(2.0f)
	{}

	/** Style used for the progress bar */
		SLATE_STYLE_ARGUMENT(FCircleProgressBarStyle, Style)
		SLATE_ARGUMENT(ECircleProgressBarFillType::Type, BarFillType)
		/** Used to determine the fill position of the progress bar ranging 0..1 */
		SLATE_ATTRIBUTE(TOptional<float>, Percent)
		/** Fill Color and Opacity */
		SLATE_ATTRIBUTE(FSlateColor, FillColorAndOpacity)
		/** Border Padding around fill bar */
		SLATE_ATTRIBUTE(FVector2D, BorderPadding)
		/** The brush to use as the background of the progress bar */
		SLATE_ARGUMENT(const FSlateBrush*, BackgroundImage)
		/** The brush to use as the fill image */
		SLATE_ARGUMENT(const FSlateBrush*, FillImage)
		/** Rate at which this widget is ticked when sleeping in seconds */
		SLATE_ARGUMENT(float, RefreshRate)
	SLATE_END_ARGS()

	void Construct(const FArguments& InArgs);

	/** Paint Widget */
	virtual int32 OnPaint(const FPaintArgs& Args, const FGeometry& AllottedGeometry, const FSlateRect& MyClippingRect, FSlateWindowElementList& OutDrawElements, int32 LayerId, const FWidgetStyle& InWidgetStyle, bool bParentEnabled) const override;
	virtual FVector2D ComputeDesiredSize(float) const override;
	virtual bool ComputeVolatility() const override;

	/** See attribute Percent */
	void SetPercent(TAttribute< TOptional<float> > InPercent);

	/** See attribute Style */
	void SetStyle(const FCircleProgressBarStyle* InStyle);

	/** See attribute BarFillType */
	void SetBarFillType(ECircleProgressBarFillType::Type InBarFillType);

	/** See attribute SetFillColorAndOpacity */
	void SetFillColorAndOpacity(TAttribute< FSlateColor > InFillColorAndOpacity);

	/** See attribute BorderPadding */
	void SetBorderPadding(TAttribute< FVector2D > InBorderPadding);

	/** See attribute BackgroundImage */
	void SetBackgroundImage(const FSlateBrush* InBackgroundImage);

	/** See attribute FillImage */
	void SetFillImage(const FSlateBrush* InFillImage);
private:

	/** Controls the speed at which the widget is ticked when in slate sleep mode */
	void SetActiveTimerTickRate(float TickRate);

	/** Widgets active tick */
	EActiveTimerReturnType ActiveTick(double InCurrentTime, float InDeltaTime);

	/** Gets the current background image. */
	const FSlateBrush* GetBackgroundImage() const;

	/** Gets the current fill image */
	const FSlateBrush* GetFillImage() const;

private:

	/** The style of the progress bar */
	const FCircleProgressBarStyle* Style;

	/** The text displayed over the progress bar */
	TAttribute< TOptional<float> > CurrentPercent;

	/** The fill type for the progress bar */
	ECircleProgressBarFillType::Type BarFillType;

	/** Background image to use for the progress bar */
	const FSlateBrush* BackgroundImage;

	/** Foreground image to use for the progress bar */
	const FSlateBrush* FillImage;

	/** Value to drive progress bar animation */
	float SpeedRate;

	/** Fill Color and Opacity */
	TAttribute<FSlateColor> FillColorAndOpacity;

	/** Border Padding */
	TAttribute<FVector2D> BorderPadding;

	/** Value to drive progress bar animation */
	float MarqueeOffset;

	/** Reference to the widgets current active timer */
	TWeakPtr<FActiveTimerHandle> ActiveTimerHandle;

	/** Rate at which the widget is currently ticked when slate sleep mode is active */
	float CurrentTickRate;

	/** The slowest that this widget can tick when in slate sleep mode */
	float MinimumTickRate;
};

```



SCircleProgressBar.cpp

```c++
// Fill out your copyright notice in the Description page of Project Settings.


#pragma optimize("",off)

#include "SCircleProgressBar.h"

void SCircleProgressBar::Construct(const FArguments& InArgs)
{
	check(InArgs._BackgroundImage);
	check(InArgs._FillImage);

	MarqueeOffset = 0.0f;
	Style = InArgs._Style;


	TAttribute< TOptional<float> > Percent = InArgs._Percent.Get().IsSet() ? InArgs._Percent : 0.5f;
	SetPercent(Percent);

	SpeedRate = 30.0f;

	BarFillType = InArgs._BarFillType;
	BackgroundImage = InArgs._BackgroundImage;
	FillImage = InArgs._FillImage;

	FillColorAndOpacity = InArgs._FillColorAndOpacity;
	BorderPadding = InArgs._BorderPadding;

	CurrentTickRate = 0.0f;
	MinimumTickRate = InArgs._RefreshRate;

	ActiveTimerHandle = RegisterActiveTimer(CurrentTickRate, FWidgetActiveTimerDelegate::CreateSP(this, &SCircleProgressBar::ActiveTick));
}


void SCircleProgressBar::SetPercent(TAttribute< TOptional<float> > InPercent)
{
	if (!CurrentPercent.IdenticalTo(InPercent))
	{
		CurrentPercent = InPercent;
		Invalidate(EInvalidateWidget::LayoutAndVolatility);
	}
}


void SCircleProgressBar::SetStyle(const FCircleProgressBarStyle* InStyle)
{
	Style = InStyle;

	if (Style == nullptr)
	{
		FArguments Defaults;
		Style = Defaults._Style;
	}

	check(Style);

	Invalidate(EInvalidateWidget::Layout);
}


void SCircleProgressBar::SetBarFillType(ECircleProgressBarFillType::Type InBarFillType)
{
	BarFillType = InBarFillType;
	Invalidate(EInvalidateWidget::Layout);
}


void SCircleProgressBar::SetFillColorAndOpacity(TAttribute< FSlateColor > InFillColorAndOpacity)
{
	FillColorAndOpacity = InFillColorAndOpacity;
	Invalidate(EInvalidateWidget::Layout);
}


void SCircleProgressBar::SetBorderPadding(TAttribute< FVector2D > InBorderPadding)
{
	BorderPadding = InBorderPadding;
	Invalidate(EInvalidateWidget::Layout);
}


void SCircleProgressBar::SetBackgroundImage(const FSlateBrush* InBackgroundImage)
{
	BackgroundImage = InBackgroundImage;
	Invalidate(EInvalidateWidget::Layout);
}


void SCircleProgressBar::SetFillImage(const FSlateBrush* InFillImage)
{
	FillImage = InFillImage;
	Invalidate(EInvalidateWidget::Layout);
}


const FSlateBrush* SCircleProgressBar::GetBackgroundImage() const
{
	return &Style->BackgroundImage ? &Style->BackgroundImage : BackgroundImage;
}


const FSlateBrush* SCircleProgressBar::GetFillImage() const
{
	return &Style->FillImage ? &Style->FillImage : FillImage;
}


int32 SCircleProgressBar::OnPaint(const FPaintArgs& Args, const FGeometry& AllottedGeometry, const FSlateRect& MyClippingRect, FSlateWindowElementList& OutDrawElements, int32 LayerId, const FWidgetStyle& InWidgetStyle, bool bParentEnabled) const
{
	// Used to track the layer ID we will return.
	int32 RetLayerId = LayerId;

	bool bEnabled = ShouldBeEnabled(bParentEnabled);
	const ESlateDrawEffect DrawEffects = bEnabled ? ESlateDrawEffect::None : ESlateDrawEffect::DisabledEffect;

	const FSlateBrush* CurrentFillImage = GetFillImage();
	const FLinearColor FillColorAndOpacitySRGB(InWidgetStyle.GetColorAndOpacityTint() * FillColorAndOpacity.Get().GetColor(InWidgetStyle) * CurrentFillImage->GetTint(InWidgetStyle));
	const FLinearColor ColorAndOpacitySRGB = InWidgetStyle.GetColorAndOpacityTint();

	// Paint background image
	const FSlateBrush* CurrentBackgroundImage = GetBackgroundImage();
	if (CurrentBackgroundImage)
	{
		FSlateDrawElement::MakeBox(
			OutDrawElements,
			RetLayerId++,
			AllottedGeometry.ToPaintGeometry(),
			CurrentBackgroundImage,
			DrawEffects,
			InWidgetStyle.GetColorAndOpacityTint() * CurrentBackgroundImage->GetTint(InWidgetStyle)
		);
	}

	TOptional<float> CurrentPercentFraction = CurrentPercent.Get();


	// Paint fill image
	float GemoScale = AllottedGeometry.Scale;
//#if WITH_EDITOR
//	GemoScale = AllottedGeometry.Scale;
//#endif
	const float Radius = FMath::Min(AllottedGeometry.GetLocalSize().X, AllottedGeometry.GetLocalSize().Y) * 0.5f;
	const FVector2D Center = AllottedGeometry.AbsolutePosition + AllottedGeometry.GetLocalSize() * GemoScale * 0.5f;

	const FVector2D Origin = AllottedGeometry.AbsolutePosition;
	const FVector2D GeometryEdge = AllottedGeometry.GetLocalSize() * GemoScale;

	const FSlateBrush* MyBrush = GetFillImage();
	FSlateResourceHandle Handle = MyBrush->GetRenderingResource();
	const FSlateShaderResourceProxy* ResourceProxy = Handle.GetResourceProxy();

	FVector2D UVCenter = FVector2D::ZeroVector;
	FVector2D UVRadius = FVector2D(1, 1);
	FVector2D UVLength = FVector2D(1, 1);
	if (ResourceProxy != nullptr)
	{
		UVRadius = 0.5f * ResourceProxy->SizeUV;	 
		UVCenter = ResourceProxy->StartUV + UVRadius;

		UVLength = ResourceProxy->SizeUV + ResourceProxy->StartUV;
	}

	/*������������ֳ�8������������,�����ڲ�ͬ���޵���������������Σ��������ۣ�б�Ƕ��ۣ�
	�������귽�� X���ң�Y���£����Ͻ���0��0
	like this:
	0,0    1,0
	---------
	| \ | / |
	|  \|/  |
	|---X---|
    |  /|\  |
	| / | \ |
	---------
	0,1      1,1
	*/

	struct MyWH
	{
		MyWH(float w, float h) : W(w), H(h) {}
		float W;
		float H;
	};

	TArray<MyWH> TempWHArray; //���������ϸ��������UV��߱�����
	TArray<FSlateVertex> Verts;

	TempWHArray.Add(MyWH(0.5, 0.5)); //����Բ��
	// Center Vertex
	Verts.AddZeroed();
	{
		FSlateVertex& NewVert = Verts.Last();
		NewVert.Position[0] = Center.X;
		NewVert.Position[1] = Center.Y;
		NewVert.TexCoords[0] = UVCenter.X;
		NewVert.TexCoords[1] = UVCenter.Y;
		NewVert.TexCoords[2] = 1.0f;
		NewVert.TexCoords[3] = 1.0f;
		NewVert.Color = FColor::White;
	}
	float CurrentPercentValue = FMath::Clamp(CurrentPercentFraction.GetValue(), 0.0f, 1.0f);
	float PercentValue = CurrentPercentValue * 100;


	switch (BarFillType)
	{
	case ECircleProgressBarFillType::ClockWise:
		if (PercentValue == 0)
		{
			//������
		}
		else if (PercentValue <= 100.0f / 8.0f)
		{
			float x = 0.5 + (0.5 * PercentValue / (100.0f / 8.0f));
			float y = 0;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > 100.0f / 8.0f && PercentValue <= 300.0f / 8.0f)
		{
			float x = 1;
			float y = ((PercentValue - 100.0f / 8.0f) / 25.0f);

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(1.0, 0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > (300.0f / 8.0f) && PercentValue <= (500.0f / 8.0f))
		{
			float x = 1.0 - (PercentValue - 300.0f / 8.0f) / 25.0f;
			float y = 1.0;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(1.0, 0));
			TempWHArray.Add(MyWH(1.0, 1.0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > 500.0f / 8.0f && PercentValue <= 700.0f / 8.0f)
		{
			float x = 0;
			float y = 1.0 - (PercentValue - 500.0f / 8.0f) / 25.0f;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(1.0, 0));
			TempWHArray.Add(MyWH(1.0, 1.0));
			TempWHArray.Add(MyWH(0, 1.0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > 700.0f / 8.0f)
		{
			float x = (PercentValue - 700.0f / 8.0f) / 25.0f;
			float y = 0;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(1.0, 0));
			TempWHArray.Add(MyWH(1.0, 1.0));
			TempWHArray.Add(MyWH(0, 1.0));
			TempWHArray.Add(MyWH(0, 0));
			TempWHArray.Add(MyWH(x, y));
		}

		break;

	case ECircleProgressBarFillType::AntiClockwise:
		if (PercentValue == 0)
		{
			//������
		}
		else if (PercentValue <= 100.0f / 8.0f)
		{
			float x = 0.5f - (0.5f * PercentValue / (100.0f / 8.0f));
			float y = 0;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > 100.0f / 8.0f && PercentValue <= 300.0f / 8.0f)
		{
			float x = 0;
			float y = ((PercentValue - 100.0f / 8.0f) / 25.0f);

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(0, 0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > (300.0f / 8.0f) && PercentValue <= (500.0f / 8.0f))
		{
			float x = (PercentValue - 300.0f / 8.0f) / 25.0f;
			float y = 1.0;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(0, 0));
			TempWHArray.Add(MyWH(0, 1.0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > 500.0f / 8.0f && PercentValue <= 700.0f / 8.0f)
		{
			float x = 1;
			float y = 1.0f - (PercentValue - 500.0f / 8.0f) / 25.0f;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(0, 0));
			TempWHArray.Add(MyWH(0, 1.0));
			TempWHArray.Add(MyWH(1.0, 1.0));
			TempWHArray.Add(MyWH(x, y));
		}
		else if (PercentValue > 700.0f / 8.0f)
		{
			float x = 0.5f + (0.5f * (100.0f - PercentValue) / (100.0f / 8.0f));
			float y = 0;

			TempWHArray.Add(MyWH(0.5, 0));
			TempWHArray.Add(MyWH(0, 0));
			TempWHArray.Add(MyWH(0, 1.0));
			TempWHArray.Add(MyWH(1.0, 1.0));
			TempWHArray.Add(MyWH(1.0, 0));
			TempWHArray.Add(MyWH(x, y));
		}

		break;
	}

	if (TempWHArray.Num() > 1)
	{
		for (int i = 0; i < TempWHArray.Num(); ++i) //��һ���Ѿ�������Բ�ĵ㣬��һ�����Ѷ�������ε������㣬���������������
		{
			Verts.AddZeroed();
			{
				FSlateVertex& NewVert = Verts.Last();
				NewVert.Position[0] = Origin.X + GeometryEdge.X * TempWHArray[i].W;
				NewVert.Position[1] = Origin.Y + GeometryEdge.Y * TempWHArray[i].H;
				NewVert.TexCoords[0] = UVLength.X * TempWHArray[i].W;
				NewVert.TexCoords[1] = UVLength.Y * TempWHArray[i].H;
				NewVert.TexCoords[2] = NewVert.TexCoords[3] = 1.0f;
				NewVert.Color = FColor::White;
			}
		}

		TArray<SlateIndex> Indexes;  //ָ��Verts���Ǽ�����Ӧ������������
		for (int i = 1; i < TempWHArray.Num(); ++i)
		{
			Indexes.Add(0);
			Indexes.Add(i);
			Indexes.Add((i + 1));
		}
		FSlateDrawElement::MakeCustomVerts(OutDrawElements, RetLayerId++, Handle, Verts, Indexes, nullptr, 0, 0);
	}

	return RetLayerId;
}


FVector2D SCircleProgressBar::ComputeDesiredSize(float) const
{
	return GetBackgroundImage()->ImageSize;
}


bool SCircleProgressBar::ComputeVolatility() const
{
	return SLeafWidget::ComputeVolatility() || CurrentPercent.IsBound();
}


void SCircleProgressBar::SetActiveTimerTickRate(float TickRate)
{
	if (CurrentTickRate != TickRate || !ActiveTimerHandle.IsValid())
	{
		CurrentTickRate = TickRate;

		TSharedPtr<FActiveTimerHandle> SharedActiveTimerHandle = ActiveTimerHandle.Pin();
		if (SharedActiveTimerHandle.IsValid())
		{
			UnRegisterActiveTimer(SharedActiveTimerHandle.ToSharedRef());
		}

		ActiveTimerHandle = RegisterActiveTimer(TickRate, FWidgetActiveTimerDelegate::CreateSP(this, &SCircleProgressBar::ActiveTick));
	}
}


EActiveTimerReturnType SCircleProgressBar::ActiveTick(double InCurrentTime, float InDeltaTime)
{
	MarqueeOffset = InCurrentTime - FMath::FloorToDouble(InCurrentTime);

	TOptional<float> CurrentPercentFraction = CurrentPercent.Get();
	if (CurrentPercentFraction.IsSet())
	{
		SetActiveTimerTickRate(MinimumTickRate);
	}
	else
	{
		SetActiveTimerTickRate(0.0f);
	}

	return EActiveTimerReturnType::Continue;
}


#pragma optimize("",on)
```

