`Authentication` コンポーネント (*Pages/Authentication. razor*) によって生成されるページは、さまざまな認証ステージを処理するために必要なルートを定義します。

`RemoteAuthenticatorView` コンポーネント:

* は、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージによって提供されます。
* 認証の各段階で、適切な操作の実行を管理します。

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
