Blazor サーバー側アプリをプリレンダリングするときに、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。 Prerendered の場合、コンポーネントのレンダリングが異なる場合があります。

ブラウザーとの接続が確立されるまで、JavaScript の相互運用呼び出しを遅延するに`OnAfterRenderAsync`は、コンポーネントライフサイクルイベントを使用できます。 このイベントは、アプリが完全にレンダリングされ、クライアント接続が確立された後にのみ呼び出されます。

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

次のコンポーネントは、プリレンダリングと互換性のある方法で、コンポーネントの初期化ロジックの一部として JavaScript の相互運用機能を使用する方法を示しています。 コンポーネントには、内部`OnAfterRenderAsync`から表示の更新をトリガーできることが示されています。 このシナリオでは、開発者が無限ループを作成しないようにする必要があります。

が呼び出された`ElementRef`場合、は、 `OnAfterRenderAsync`コンポーネントがレンダリングされるまで JavaScript 要素が存在しないため、は以前のライフサイクルメソッドでのみ使用されます。 `JSRuntime.InvokeAsync`

`StateHasChanged`は、JavaScript の相互運用呼び出しから取得された新しい状態を使用してコンポーネントをレンダリングするために呼び出されます。 がの`StateHasChanged` `infoFromJs`場合にのみ呼び出されるため、コードは無限ループを作成しません。 `null`

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
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

アプリが現在プリレンダリングされているかどうかに基づいて異なるコンテンツ`IsConnected`を表示する`IComponentContext`には、サービスでプロパティを使用します。 サーバー側で実行され`IsConnected`ている`true`場合、は、クライアントへのアクティブな接続がある場合にのみを返します。 クライアント側を`true`実行すると、常にが返されます。

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```
