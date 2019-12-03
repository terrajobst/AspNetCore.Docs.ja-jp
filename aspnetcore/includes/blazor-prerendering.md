Blazor Server アプリをプリレンダリングするときに、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。 Prerendered の場合、コンポーネントのレンダリングが異なる場合があります。

ブラウザーとの接続が確立されるまで、JavaScript の相互運用呼び出しを遅延するには、 [OnAfterRenderAsync コンポーネントライフサイクルイベント](xref:blazor/lifecycle#after-component-render)を使用できます。 このイベントは、アプリが完全にレンダリングされ、クライアント接続が確立された後にのみ呼び出されます。

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

前のコード例では、 *wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内に JavaScript 関数 `setElementText` を指定します。 関数は `IJSRuntime.InvokeVoidAsync` と共に呼び出され、値を返しません。

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> 前の例では、デモンストレーションのみを目的として、ドキュメントオブジェクトモデル (DOM) を直接変更しています。 Javascript で DOM を直接変更することは、ほとんどのシナリオでは推奨されません。これは、JavaScript が Blazor の変更の追跡に干渉する可能性があるためです。

次のコンポーネントは、プリレンダリングと互換性のある方法で、コンポーネントの初期化ロジックの一部として JavaScript の相互運用機能を使用する方法を示しています。 コンポーネントには、`OnAfterRenderAsync`内からレンダリングの更新をトリガーできることが示されています。 このシナリオでは、開発者が無限ループを作成しないようにする必要があります。

`JSRuntime.InvokeAsync` が呼び出される場合、`ElementRef` は、コンポーネントがレンダリングされるまで JavaScript 要素が存在しないため、`OnAfterRenderAsync` ではなく、以前のライフサイクルメソッドでのみ使用されます。

[Statehaschanged](xref:blazor/lifecycle#state-changes)は、JavaScript の相互運用呼び出しから取得された新しい状態をコンポーネントにレンダリングするために呼び出されます。 このコードでは、`infoFromJs` が `null`場合にのみ `StateHasChanged` が呼び出されるため、無限ループは作成されません。

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

前のコード例では、 *wwwroot/index.html* (Blazor WebAssembly または*Pages/_Host* (Blazor Server) の `<head>` 要素内に JavaScript 関数 `setElementText` を指定します。 関数は `IJSRuntime.InvokeAsync` を指定して呼び出され、値を返します。

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> 前の例では、デモンストレーションのみを目的として、ドキュメントオブジェクトモデル (DOM) を直接変更しています。 Javascript で DOM を直接変更することは、ほとんどのシナリオでは推奨されません。これは、JavaScript が Blazor の変更の追跡に干渉する可能性があるためです。
