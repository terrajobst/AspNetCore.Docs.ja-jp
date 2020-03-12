`RedirectToLogin` コンポーネント (*Shared/RedirectToLogin. razor*):

* 承認されていないユーザーのログインページへのリダイレクトを管理します。
* 認証が成功した場合にそのページに戻ることができるように、ユーザーがアクセスしようとしている現在の URL を保持します。

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl={Navigation.Uri}");
    }
}
```
