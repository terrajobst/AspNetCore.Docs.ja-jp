<span data-ttu-id="5121a-101">`RedirectToLogin` コンポーネント (*Shared/RedirectToLogin. razor*):</span><span class="sxs-lookup"><span data-stu-id="5121a-101">The `RedirectToLogin` component (*Shared/RedirectToLogin.razor*):</span></span>

* <span data-ttu-id="5121a-102">承認されていないユーザーのログインページへのリダイレクトを管理します。</span><span class="sxs-lookup"><span data-stu-id="5121a-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="5121a-103">認証が成功した場合にそのページに戻ることができるように、ユーザーがアクセスしようとしている現在の URL を保持します。</span><span class="sxs-lookup"><span data-stu-id="5121a-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
