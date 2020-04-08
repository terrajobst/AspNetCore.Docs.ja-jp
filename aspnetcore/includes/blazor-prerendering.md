---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647528"
---
<span data-ttu-id="82252-101">Blazor サーバー アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="82252-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="82252-102">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="82252-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="82252-103">ブラウザーとの接続が確立されるまで JavaScript の相互運用呼び出しを遅らせるために、[OnAfterRenderAsync コンポーネント ライフサイクル イベント](xref:blazor/lifecycle#after-component-render)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="82252-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="82252-104">このイベントは、アプリが完全にレンダリングされ、クライアント接続が確立された後にのみ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="82252-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="82252-105">前のサンプル コードでは、`setElementText`wwwroot/index.html`<head>` (*WebAssembly) または*Pages/_Host.cshtmlBlazor (*サーバー) の* 要素内で、Blazor JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="82252-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="82252-106">関数は `IJSRuntime.InvokeVoidAsync` を指定して呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="82252-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="82252-107">前の例では、デモンストレーションのみを目的として、ドキュメント オブジェクト モデル (DOM) を直接変更しています。</span><span class="sxs-lookup"><span data-stu-id="82252-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="82252-108">JavaScript での DOM の直接変更は、ほとんどのシナリオでは推奨されません。JavaScript が Blazor の変更追跡に影響する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="82252-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="82252-109">次のコンポーネントは、プリレンダリングと互換性のある方法で、コンポーネントの初期化ロジックの一部として JavaScript の相互運用を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="82252-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="82252-110">コンポーネントには、`OnAfterRenderAsync` 内からレンダリングの更新をトリガーできることが示されています。</span><span class="sxs-lookup"><span data-stu-id="82252-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="82252-111">このシナリオでは、開発者が無限ループを作成しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="82252-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="82252-112">`JSRuntime.InvokeAsync` が呼び出されるとき、`ElementRef` は、以前のライフサイクル メソッドではなく `OnAfterRenderAsync` でのみ使用されます。コンポーネントがレンダリングされるまで JavaScript 要素が存在しないためです。</span><span class="sxs-lookup"><span data-stu-id="82252-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="82252-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) は、JavaScript の相互運用呼び出しから取得された新しい状態でコンポーネントを再度レンダリングするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="82252-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="82252-114">`StateHasChanged` は `infoFromJs`が `null` である場合にのみ呼び出されるため、このコードで無限ループが作成されることはありません。</span><span class="sxs-lookup"><span data-stu-id="82252-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="82252-115">前のサンプル コードでは、`setElementText`wwwroot/index.html`<head>` (*WebAssembly) または*Pages/_Host.cshtmlBlazor (*サーバー) の* 要素内で、Blazor JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="82252-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="82252-116">関数は `IJSRuntime.InvokeAsync` を指定して呼び出され、次の値を返します。</span><span class="sxs-lookup"><span data-stu-id="82252-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="82252-117">前の例では、デモンストレーションのみを目的として、ドキュメント オブジェクト モデル (DOM) を直接変更しています。</span><span class="sxs-lookup"><span data-stu-id="82252-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="82252-118">JavaScript での DOM の直接変更は、ほとんどのシナリオでは推奨されません。JavaScript が Blazor の変更追跡に影響する可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="82252-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
