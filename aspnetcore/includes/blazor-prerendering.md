Blazor Server アプリをプリレンダリングするときに、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。 Prerendered の場合、コンポーネントのレンダリングが異なる場合があります。

ブラウザーとの接続が確立されるまで、JavaScript の相互運用呼び出しを遅延するには、`OnAfterRenderAsync` コンポーネントライフサイクルイベントを使用できます。 このイベントは、アプリが完全にレンダリングされ、クライアント接続が確立された後にのみ呼び出されます。

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
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

次のコンポーネントは、プリレンダリングと互換性のある方法で、コンポーネントの初期化ロジックの一部として JavaScript の相互運用機能を使用する方法を示しています。 コンポーネントには、`OnAfterRenderAsync` 内からレンダリングの更新をトリガーできることが示されています。 このシナリオでは、開発者が無限ループを作成しないようにする必要があります。

@No__t_0 が呼び出される場合、`ElementRef` は、コンポーネントがレンダリングされるまで JavaScript 要素が存在しないため、`OnAfterRenderAsync` ではなく、以前のライフサイクルメソッドでのみ使用されます。

JavaScript の相互運用呼び出しから取得された新しい状態をコンポーネントにレンダリングするために、`StateHasChanged` が呼び出されます。 このコードでは、`infoFromJs` が `null` 場合にのみ `StateHasChanged` が呼び出されるため、無限ループは作成されません。

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
