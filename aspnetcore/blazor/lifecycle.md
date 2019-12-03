---
title: ASP.NET Core Blazor ライフサイクル
author: guardrex
description: ASP.NET Core Blazor アプリで Razor コンポーネントライフサイクルメソッドを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 1482f6b2147c74b11836e8029401bb8bcb3cdb2d
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681375"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="563f9-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="563f9-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="563f9-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="563f9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="563f9-105">Blazor framework には、同期および非同期のライフサイクルメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="563f9-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="563f9-106">コンポーネントの初期化およびレンダリング中にコンポーネントに対して追加の操作を実行するには、ライフサイクルメソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="563f9-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="563f9-107">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="563f9-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="563f9-108">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="563f9-108">Component initialization methods</span></span>

<span data-ttu-id="563f9-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> し、コンポーネントを初期化するコードを実行 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> ます。</span><span class="sxs-lookup"><span data-stu-id="563f9-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="563f9-110">これらのメソッドは、コンポーネントが最初にインスタンス化されるときに1回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="563f9-111">非同期操作を実行するには、操作で `OnInitializedAsync` と `await` キーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="563f9-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="563f9-112">`OnInitializedAsync` のライフサイクルイベント中に、コンポーネントの初期化時に非同期作業を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="563f9-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="563f9-113">同期操作の場合は、`OnInitialized`を使用します。</span><span class="sxs-lookup"><span data-stu-id="563f9-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="563f9-114">パラメーターを設定する前</span><span class="sxs-lookup"><span data-stu-id="563f9-114">Before parameters are set</span></span>

<span data-ttu-id="563f9-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> は、レンダリングツリーのコンポーネントの親によって提供されるパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="563f9-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="563f9-116"><xref:Microsoft.AspNetCore.Components.ParameterView> には、`SetParametersAsync` が呼び出されるたびに、パラメーター値のセット全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="563f9-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="563f9-117">`SetParametersAsync` の既定の実装では、`ParameterView`に対応する値を持つ `[Parameter]` または `[CascadingParameter]` 属性で修飾された各プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-117">The default implementation of `SetParametersAsync` sets the value of each property decorated with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="563f9-118">`ParameterView` に対応する値を持たないパラメーターは、変更されずに残ります。</span><span class="sxs-lookup"><span data-stu-id="563f9-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="563f9-119">`base.SetParametersAync` が呼び出されない場合、カスタムコードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="563f9-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="563f9-120">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="563f9-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="563f9-121">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="563f9-121">After parameters are set</span></span>

<span data-ttu-id="563f9-122">コンポーネントが親からパラメーターを受け取り、値がプロパティに割り当てられると、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="563f9-123">これらのメソッドは、コンポーネントの初期化の後に、新しいパラメーター値が指定されるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="563f9-124">パラメーターとプロパティ値を適用するときの非同期処理は、`OnParametersSetAsync` ライフサイクルイベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="563f9-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="563f9-125">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="563f9-125">After component render</span></span>

<span data-ttu-id="563f9-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="563f9-127">この時点で、要素参照とコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="563f9-128">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="563f9-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="563f9-129">`OnAfterRenderAsync` と `OnAfterRender`の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="563f9-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="563f9-130">は、コンポーネントインスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="563f9-131">を使用すると、初期化作業が1回だけ実行されるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="563f9-131">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="563f9-132">`OnAfterRenderAsync` のライフサイクルイベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="563f9-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="563f9-133">`OnAfterRenderAsync`から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークは、そのタスクが完了すると、コンポーネントに対してさらにレンダリングサイクルをスケジュールしません。</span><span class="sxs-lookup"><span data-stu-id="563f9-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="563f9-134">これは、無限のレンダーループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="563f9-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="563f9-135">これは、返されたタスクが完了すると、さらにレンダリングサイクルをスケジュールする他のライフサイクルメソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="563f9-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="563f9-136">`OnAfterRender` と `OnAfterRenderAsync`*は、サーバーでのプリレンダリング時に呼び出されません。*</span><span class="sxs-lookup"><span data-stu-id="563f9-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="563f9-137">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="563f9-137">Suppress UI refreshing</span></span>

<span data-ttu-id="563f9-138"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="563f9-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="563f9-139">実装によって `true`が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="563f9-140">`ShouldRender` は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="563f9-141">`ShouldRender` がオーバーライドされた場合でも、コンポーネントは常に最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="563f9-142">状態変更</span><span class="sxs-lookup"><span data-stu-id="563f9-142">State changes</span></span>

<span data-ttu-id="563f9-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="563f9-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="563f9-144">必要に応じて、`StateHasChanged` を呼び出すと、コンポーネントが再スローされます。</span><span class="sxs-lookup"><span data-stu-id="563f9-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="563f9-145">レンダー時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="563f9-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="563f9-146">ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="563f9-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="563f9-147">ライフサイクルメソッドの実行中に、オブジェクトが `null` されているか、データが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="563f9-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="563f9-148">オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="563f9-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="563f9-149">オブジェクトの `null`中に、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="563f9-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="563f9-150">Blazor テンプレートの `FetchData` コンポーネントでは、`OnInitializedAsync` がユーザー receive 予測データ (`forecasts`) に上書きされます。</span><span class="sxs-lookup"><span data-stu-id="563f9-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="563f9-151">`forecasts` が `null`と、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="563f9-152">`OnInitializedAsync` によって返された `Task` が完了すると、コンポーネントは更新された状態とピアリングされます。</span><span class="sxs-lookup"><span data-stu-id="563f9-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="563f9-153">Blazor サーバーテンプレートでの*Pages/FetchData. razor* :</span><span class="sxs-lookup"><span data-stu-id="563f9-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="563f9-154">IDisposable によるコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="563f9-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="563f9-155">コンポーネントが <xref:System.IDisposable>を実装している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="563f9-156">次のコンポーネントでは、`@implements IDisposable` と `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="563f9-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="563f9-157">`Dispose` での <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="563f9-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="563f9-158">`StateHasChanged` は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="563f9-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="563f9-159">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="563f9-159">Handle errors</span></span>

<span data-ttu-id="563f9-160">ライフサイクルメソッドの実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563f9-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
