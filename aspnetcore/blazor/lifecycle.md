---
title: ASP.NET Core Blazor ライフサイクル
author: guardrex
description: ASP.NET Core Blazor アプリで Razor コンポーネントライフサイクルメソッドを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146369"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="135cf-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="135cf-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="135cf-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="135cf-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="135cf-105">Blazor framework には、同期および非同期のライフサイクルメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="135cf-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="135cf-106">コンポーネントの初期化およびレンダリング中にコンポーネントに対して追加の操作を実行するには、ライフサイクルメソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="135cf-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="135cf-107">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="135cf-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="135cf-108">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="135cf-108">Component initialization methods</span></span>

<span data-ttu-id="135cf-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> は、その親コンポーネントから初期パラメーターを受け取った後に、コンポーネントが初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="135cf-110">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、`OnInitializedAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="135cf-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span> <span data-ttu-id="135cf-111">これらのメソッドは、コンポーネントが最初にインスタンス化されるときに1回だけ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-111">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="135cf-112">同期操作の場合は、`OnInitialized`をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="135cf-112">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="135cf-113">非同期操作を実行するには、`OnInitializedAsync` をオーバーライドし、操作で `await` キーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="135cf-113">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="135cf-114">パラメーターを設定する前</span><span class="sxs-lookup"><span data-stu-id="135cf-114">Before parameters are set</span></span>

<span data-ttu-id="135cf-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> は、レンダリングツリーのコンポーネントの親によって提供されるパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="135cf-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="135cf-116"><xref:Microsoft.AspNetCore.Components.ParameterView> には、`SetParametersAsync` が呼び出されるたびに、パラメーター値のセット全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="135cf-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="135cf-117">`SetParametersAsync` の既定の実装では、`ParameterView`に対応する値を持つ `[Parameter]` または `[CascadingParameter]` 属性を使用して、各プロパティの値を設定します。</span><span class="sxs-lookup"><span data-stu-id="135cf-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="135cf-118">`ParameterView` に対応する値を持たないパラメーターは、変更されずに残ります。</span><span class="sxs-lookup"><span data-stu-id="135cf-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="135cf-119">`base.SetParametersAync` が呼び出されない場合、カスタムコードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="135cf-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="135cf-120">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="135cf-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="135cf-121">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="135cf-121">After parameters are set</span></span>

<span data-ttu-id="135cf-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> は次のように呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="135cf-123">コンポーネントが初期化され、その親コンポーネントからパラメーターの最初のセットを受け取ったとき。</span><span class="sxs-lookup"><span data-stu-id="135cf-123">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="135cf-124">親コンポーネントが再レンダリングし、次のものを提供する場合:</span><span class="sxs-lookup"><span data-stu-id="135cf-124">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="135cf-125">少なくとも1つのパラメーターが変更された既知のプリミティブ不変型のみです。</span><span class="sxs-lookup"><span data-stu-id="135cf-125">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="135cf-126">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="135cf-126">Any complex-typed parameters.</span></span> <span data-ttu-id="135cf-127">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーターセットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="135cf-127">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="135cf-128">パラメーターとプロパティ値を適用するときの非同期処理は、`OnParametersSetAsync` ライフサイクルイベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="135cf-128">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="135cf-129">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="135cf-129">After component render</span></span>

<span data-ttu-id="135cf-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="135cf-131">この時点で、要素参照とコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-131">Element and component references are populated at this point.</span></span> <span data-ttu-id="135cf-132">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="135cf-132">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="135cf-133">`OnAfterRenderAsync` と `OnAfterRender`の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="135cf-133">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="135cf-134">は、コンポーネントインスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-134">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="135cf-135">を使用すると、初期化作業が1回だけ実行されるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="135cf-135">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="135cf-136">`OnAfterRenderAsync` のライフサイクルイベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="135cf-136">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="135cf-137">`OnAfterRenderAsync`から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークは、そのタスクが完了すると、コンポーネントに対してさらにレンダリングサイクルをスケジュールしません。</span><span class="sxs-lookup"><span data-stu-id="135cf-137">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="135cf-138">これは、無限のレンダーループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="135cf-138">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="135cf-139">これは、返されたタスクが完了すると、さらにレンダリングサイクルをスケジュールする他のライフサイクルメソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="135cf-139">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="135cf-140">`OnAfterRender` と `OnAfterRenderAsync`*は、サーバーでのプリレンダリング時に呼び出されません。*</span><span class="sxs-lookup"><span data-stu-id="135cf-140">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="135cf-141">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="135cf-141">Suppress UI refreshing</span></span>

<span data-ttu-id="135cf-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="135cf-142">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="135cf-143">実装によって `true`が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-143">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="135cf-144">`ShouldRender` は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-144">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="135cf-145">`ShouldRender` がオーバーライドされた場合でも、コンポーネントは常に最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-145">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="135cf-146">状態変更</span><span class="sxs-lookup"><span data-stu-id="135cf-146">State changes</span></span>

<span data-ttu-id="135cf-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="135cf-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="135cf-148">必要に応じて、`StateHasChanged` を呼び出すと、コンポーネントが再スローされます。</span><span class="sxs-lookup"><span data-stu-id="135cf-148">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="135cf-149">レンダー時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="135cf-149">Handle incomplete async actions at render</span></span>

<span data-ttu-id="135cf-150">ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="135cf-150">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="135cf-151">ライフサイクルメソッドの実行中に、オブジェクトが `null` されているか、データが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="135cf-151">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="135cf-152">オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="135cf-152">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="135cf-153">オブジェクトの `null`中に、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="135cf-153">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="135cf-154">Blazor テンプレートの `FetchData` コンポーネントでは、`OnInitializedAsync` がユーザー receive 予測データ (`forecasts`) に上書きされます。</span><span class="sxs-lookup"><span data-stu-id="135cf-154">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="135cf-155">`forecasts` が `null`と、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-155">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="135cf-156">`OnInitializedAsync` によって返された `Task` が完了すると、コンポーネントは更新された状態とピアリングされます。</span><span class="sxs-lookup"><span data-stu-id="135cf-156">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="135cf-157">Blazor サーバーテンプレートでの*Pages/FetchData. razor* :</span><span class="sxs-lookup"><span data-stu-id="135cf-157">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="135cf-158">IDisposable によるコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="135cf-158">Component disposal with IDisposable</span></span>

<span data-ttu-id="135cf-159">コンポーネントが <xref:System.IDisposable>を実装している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-159">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="135cf-160">次のコンポーネントでは、`@implements IDisposable` と `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="135cf-160">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
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
> <span data-ttu-id="135cf-161">`Dispose` での <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="135cf-161">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="135cf-162">`StateHasChanged` は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="135cf-162">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="135cf-163">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="135cf-163">Handle errors</span></span>

<span data-ttu-id="135cf-164">ライフサイクルメソッドの実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="135cf-164">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
