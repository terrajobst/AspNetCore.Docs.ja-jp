[インデックスページ (*wwwroot/index.html*)] ページには、JavaScript で `AuthenticationService` を定義するスクリプトが含まれています。 `AuthenticationService` は、OIDC プロトコルの下位レベルの詳細を処理します。 アプリは、認証操作を実行するために、スクリプトで定義されているメソッドを内部的に呼び出します。

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
