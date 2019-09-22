<span data-ttu-id="f4311-101">Blazor Server アプリをプリレンダリングするときに、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="f4311-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="f4311-102">Prerendered の場合、コンポーネントのレンダリングが異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="f4311-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="f4311-103">ブラウザーとの接続が確立されるまで、JavaScript の相互運用呼び出しを遅延するに`OnAfterRenderAsync`は、コンポーネントライフサイクルイベントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f4311-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="f4311-104">このイベントは、アプリが完全にレンダリングされ、クライアント接続が確立された後にのみ呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f4311-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeAsync<object>(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

<span data-ttu-id="f4311-105">次のコンポーネントは、プリレンダリングと互換性のある方法で、コンポーネントの初期化ロジックの一部として JavaScript の相互運用機能を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f4311-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="f4311-106">コンポーネントには、内部`OnAfterRenderAsync`から表示の更新をトリガーできることが示されています。</span><span class="sxs-lookup"><span data-stu-id="f4311-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="f4311-107">このシナリオでは、開発者が無限ループを作成しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4311-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="f4311-108">が呼び出された`ElementRef`場合、は、 `OnAfterRenderAsync`コンポーネントがレンダリングされるまで JavaScript 要素が存在しないため、は以前のライフサイクルメソッドでのみ使用されます。 `JSRuntime.InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="f4311-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="f4311-109">`StateHasChanged`は、JavaScript の相互運用呼び出しから取得された新しい状態を使用してコンポーネントをレンダリングするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f4311-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="f4311-110">がの`StateHasChanged` `infoFromJs`場合にのみ呼び出されるため、コードは無限ループを作成しません。 `null`</span><span class="sxs-lookup"><span data-stu-id="f4311-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```