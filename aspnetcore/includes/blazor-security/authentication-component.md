<span data-ttu-id="065df-101">`Authentication` コンポーネント (*Pages/Authentication. razor*) によって生成されるページは、さまざまな認証ステージを処理するために必要なルートを定義します。</span><span class="sxs-lookup"><span data-stu-id="065df-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="065df-102">`RemoteAuthenticatorView` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="065df-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="065df-103">は、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="065df-103">Is provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span>
* <span data-ttu-id="065df-104">認証の各段階で、適切な操作の実行を管理します。</span><span class="sxs-lookup"><span data-stu-id="065df-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
