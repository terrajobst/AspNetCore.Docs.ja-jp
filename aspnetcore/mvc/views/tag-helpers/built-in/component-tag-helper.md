---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: ASP.NET Core コンポーネントタグヘルパーを使用して、ページおよびビューで Razor コンポーネントを表示する方法について説明します。
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226382"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="af337-103">ASP.NET Core のコンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="af337-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="af337-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="af337-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="af337-105">ページまたはビューからコンポーネントを表示するには、[コンポーネントタグヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="af337-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

<span data-ttu-id="af337-106">次のコンポーネントタグヘルパーは、ページまたはビューで `Counter` コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="af337-106">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="af337-107">コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="af337-107">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="af337-108">チェックボックスのラベルの色とサイズを設定する次の `ColorfulCheckbox` コンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="af337-108">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="af337-109">コンポーネントタグヘルパーでは、`Size` (`int`) および `Color` (`string`)[コンポーネントのパラメーター](xref:blazor/components#component-parameters)を設定できます。</span><span class="sxs-lookup"><span data-stu-id="af337-109">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="af337-110">ページまたはビューには、次の HTML が表示されます。</span><span class="sxs-lookup"><span data-stu-id="af337-110">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="af337-111">引用符で囲まれた文字列を渡すには、前の例の `param-Color` に示すように、[明示的な Razor 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です。</span><span class="sxs-lookup"><span data-stu-id="af337-111">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="af337-112">`string` 型の値に対する Razor 解析動作は、属性が `object` 型であるため、`param-*` 属性には適用されません。</span><span class="sxs-lookup"><span data-stu-id="af337-112">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="af337-113">パラメーターは、JSON のシリアル化可能な型である必要があります。これは通常、その型に既定のコンストラクターと設定できるプロパティがある必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="af337-113">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="af337-114">たとえば、前の例では `Size` と `Color` の値を指定できます。 `Size` と `Color` の型はプリミティブ型 (`int` および `string`) であり、JSON シリアライザーでサポートされています。</span><span class="sxs-lookup"><span data-stu-id="af337-114">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="af337-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="af337-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="af337-116">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="af337-116">Is prerendered into the page.</span></span>
* <span data-ttu-id="af337-117">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="af337-117">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="af337-118">レンダリングモード</span><span class="sxs-lookup"><span data-stu-id="af337-118">Render Mode</span></span> | <span data-ttu-id="af337-119">説明</span><span class="sxs-lookup"><span data-stu-id="af337-119">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="af337-120">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="af337-120">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="af337-121">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="af337-121">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="af337-122">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="af337-122">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="af337-123">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="af337-123">Output from the component isn't included.</span></span> <span data-ttu-id="af337-124">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="af337-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="af337-125">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="af337-125">Renders the component into static HTML.</span></span> |

<span data-ttu-id="af337-126">ページとビューはコンポーネントを使用できますが、逆のことはできません。</span><span class="sxs-lookup"><span data-stu-id="af337-126">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="af337-127">コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="af337-127">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="af337-128">コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="af337-128">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="af337-129">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="af337-129">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af337-130">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="af337-130">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
