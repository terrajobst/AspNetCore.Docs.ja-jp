<span data-ttu-id="d0d57-101">`LoginDisplay` コンポーネント (*shared/Logindisplay*) は、`MainLayout` コンポーネント (*shared/mainlayout. razor*) にレンダリングされ、次の動作を管理します。</span><span class="sxs-lookup"><span data-stu-id="d0d57-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="d0d57-102">認証されたユーザーの場合:</span><span class="sxs-lookup"><span data-stu-id="d0d57-102">For authenticated users:</span></span>
  * <span data-ttu-id="d0d57-103">現在のユーザー名を表示します。</span><span class="sxs-lookup"><span data-stu-id="d0d57-103">Displays the current username.</span></span>
  * <span data-ttu-id="d0d57-104">アプリからログアウトするためのボタンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="d0d57-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="d0d57-105">匿名ユーザーの場合、にログインするオプションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="d0d57-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
