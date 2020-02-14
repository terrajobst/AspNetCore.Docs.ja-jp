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
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213390"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="2347c-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="2347c-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="2347c-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2347c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2347c-105">Blazor framework には、同期および非同期のライフサイクルメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2347c-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="2347c-106">コンポーネントの初期化およびレンダリング中にコンポーネントに対して追加の操作を実行するには、ライフサイクルメソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="2347c-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="2347c-107">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="2347c-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="2347c-108">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="2347c-108">Component initialization methods</span></span>

<span data-ttu-id="2347c-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> は、その親コンポーネントから初期パラメーターを受け取った後に、コンポーネントが初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="2347c-110">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、`OnInitializedAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="2347c-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="2347c-111">同期操作の場合は、`OnInitialized`をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="2347c-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="2347c-112">非同期操作を実行するには、`OnInitializedAsync` をオーバーライドし、操作で `await` キーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="2347c-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="2347c-113">コンテンツの呼び出しを[プリレンダリング](xref:blazor/hosting-model-configuration#render-mode)する Blazor サーバーアプリは、 **_2 回_** `OnInitializedAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="2347c-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="2347c-114">コンポーネントが最初にページの一部として静的にレンダリングされたとき。</span><span class="sxs-lookup"><span data-stu-id="2347c-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="2347c-115">ブラウザーがサーバーへの接続を確立する2回目。</span><span class="sxs-lookup"><span data-stu-id="2347c-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="2347c-116">`OnInitializedAsync` の開発者コードが2回実行されないようにするには、「[プリコーディング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2347c-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="2347c-117">Blazor Server アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="2347c-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="2347c-118">Prerendered の場合、コンポーネントのレンダリングが異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2347c-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="2347c-119">詳細については、「[アプリのプリレンダリングを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2347c-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="2347c-120">パラメーターを設定する前</span><span class="sxs-lookup"><span data-stu-id="2347c-120">Before parameters are set</span></span>

<span data-ttu-id="2347c-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> は、レンダリングツリーのコンポーネントの親によって提供されるパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="2347c-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="2347c-122"><xref:Microsoft.AspNetCore.Components.ParameterView> には、`SetParametersAsync` が呼び出されるたびに、パラメーター値のセット全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2347c-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="2347c-123">`SetParametersAsync` の既定の実装では、`ParameterView`に対応する値を持つ `[Parameter]` または `[CascadingParameter]` 属性を使用して、各プロパティの値を設定します。</span><span class="sxs-lookup"><span data-stu-id="2347c-123">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="2347c-124">`ParameterView` に対応する値を持たないパラメーターは、変更されずに残ります。</span><span class="sxs-lookup"><span data-stu-id="2347c-124">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="2347c-125">`base.SetParametersAync` が呼び出されない場合、カスタムコードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="2347c-125">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="2347c-126">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2347c-126">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="2347c-127">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="2347c-127">After parameters are set</span></span>

<span data-ttu-id="2347c-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> は次のように呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="2347c-129">コンポーネントが初期化され、その親コンポーネントからパラメーターの最初のセットを受け取ったとき。</span><span class="sxs-lookup"><span data-stu-id="2347c-129">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="2347c-130">親コンポーネントが再レンダリングし、次のものを提供する場合:</span><span class="sxs-lookup"><span data-stu-id="2347c-130">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="2347c-131">少なくとも1つのパラメーターが変更された既知のプリミティブ不変型のみです。</span><span class="sxs-lookup"><span data-stu-id="2347c-131">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="2347c-132">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="2347c-132">Any complex-typed parameters.</span></span> <span data-ttu-id="2347c-133">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーターセットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="2347c-133">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="2347c-134">パラメーターとプロパティ値を適用するときの非同期処理は、`OnParametersSetAsync` ライフサイクルイベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2347c-134">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="2347c-135">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="2347c-135">After component render</span></span>

<span data-ttu-id="2347c-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="2347c-137">この時点で、要素参照とコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-137">Element and component references are populated at this point.</span></span> <span data-ttu-id="2347c-138">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="2347c-138">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="2347c-139">`OnAfterRenderAsync` と `OnAfterRender`の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2347c-139">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="2347c-140">は、コンポーネントインスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-140">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="2347c-141">を使用すると、初期化作業が1回だけ実行されるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="2347c-141">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="2347c-142">`OnAfterRenderAsync` のライフサイクルイベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2347c-142">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="2347c-143">`OnAfterRenderAsync`から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークは、そのタスクが完了すると、コンポーネントに対してさらにレンダリングサイクルをスケジュールしません。</span><span class="sxs-lookup"><span data-stu-id="2347c-143">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="2347c-144">これは、無限のレンダーループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="2347c-144">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="2347c-145">これは、返されたタスクが完了すると、さらにレンダリングサイクルをスケジュールする他のライフサイクルメソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="2347c-145">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="2347c-146">`OnAfterRender` と `OnAfterRenderAsync`*は、サーバーでのプリレンダリング時に呼び出されません。*</span><span class="sxs-lookup"><span data-stu-id="2347c-146">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="2347c-147">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="2347c-147">Suppress UI refreshing</span></span>

<span data-ttu-id="2347c-148"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="2347c-148">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="2347c-149">実装によって `true`が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-149">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="2347c-150">`ShouldRender` は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-150">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="2347c-151">`ShouldRender` がオーバーライドされた場合でも、コンポーネントは常に最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-151">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="2347c-152">状態の変化</span><span class="sxs-lookup"><span data-stu-id="2347c-152">State changes</span></span>

<span data-ttu-id="2347c-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="2347c-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="2347c-154">必要に応じて、`StateHasChanged` を呼び出すと、コンポーネントが再スローされます。</span><span class="sxs-lookup"><span data-stu-id="2347c-154">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="2347c-155">レンダー時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="2347c-155">Handle incomplete async actions at render</span></span>

<span data-ttu-id="2347c-156">ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2347c-156">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="2347c-157">ライフサイクルメソッドの実行中に、オブジェクトが `null` されているか、データが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2347c-157">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="2347c-158">オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="2347c-158">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="2347c-159">オブジェクトの `null`中に、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="2347c-159">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="2347c-160">Blazor テンプレートの `FetchData` コンポーネントでは、`OnInitializedAsync` がユーザー receive 予測データ (`forecasts`) に上書きされます。</span><span class="sxs-lookup"><span data-stu-id="2347c-160">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="2347c-161">`forecasts` が `null`と、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-161">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="2347c-162">`OnInitializedAsync` によって返された `Task` が完了すると、コンポーネントは更新された状態とピアリングされます。</span><span class="sxs-lookup"><span data-stu-id="2347c-162">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="2347c-163">Blazor サーバーテンプレートでの*Pages/FetchData. razor* :</span><span class="sxs-lookup"><span data-stu-id="2347c-163">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="2347c-164">IDisposable によるコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="2347c-164">Component disposal with IDisposable</span></span>

<span data-ttu-id="2347c-165">コンポーネントが <xref:System.IDisposable>を実装している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-165">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="2347c-166">次のコンポーネントでは、`@implements IDisposable` と `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-166">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="2347c-167">`Dispose` での <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2347c-167">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="2347c-168">`StateHasChanged` は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2347c-168">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="2347c-169">エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="2347c-169">Handle errors</span></span>

<span data-ttu-id="2347c-170">ライフサイクルメソッドの実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2347c-170">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="2347c-171">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="2347c-171">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="2347c-172">Blazor サーバーアプリで `RenderMode` が `ServerPrerendered`場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="2347c-172">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="2347c-173">ブラウザーがサーバーへの接続を確立すると、コンポーネントが*再び*表示され、コンポーネントが対話型になります。</span><span class="sxs-lookup"><span data-stu-id="2347c-173">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="2347c-174">コンポーネントを初期化するための[Oninitialized 化された {Async}](xref:blazor/lifecycle#component-initialization-methods)ライフサイクルメソッドが存在する場合、メソッドは*2 回*実行されます。</span><span class="sxs-lookup"><span data-stu-id="2347c-174">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="2347c-175">コンポーネントが静的に prerendered された場合。</span><span class="sxs-lookup"><span data-stu-id="2347c-175">When the component is prerendered statically.</span></span>
* <span data-ttu-id="2347c-176">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="2347c-176">After the server connection has been established.</span></span>

<span data-ttu-id="2347c-177">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変化する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2347c-177">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="2347c-178">Blazor サーバーアプリでダブルレンダリングのシナリオを回避するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="2347c-178">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="2347c-179">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="2347c-179">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="2347c-180">コンポーネントの状態を保存するには、プリレンダリング時に識別子を使用します。</span><span class="sxs-lookup"><span data-stu-id="2347c-180">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="2347c-181">プリレンダリング後に識別子を使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="2347c-181">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="2347c-182">次のコードは、テンプレートベースの Blazor サーバーアプリで、ダブルレンダリングを回避する更新された `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="2347c-182">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="2347c-183">`RenderMode`の詳細については、「<xref:blazor/hosting-model-configuration#render-mode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2347c-183">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="2347c-184">アプリがプリレンダリングされるタイミングを検出する</span><span class="sxs-lookup"><span data-stu-id="2347c-184">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
