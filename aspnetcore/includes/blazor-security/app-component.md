<span data-ttu-id="1f648-101">`App` コンポーネント (*app.xaml*) は、Blazor Server apps にある `App` コンポーネントに似ています。</span><span class="sxs-lookup"><span data-stu-id="1f648-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="1f648-102">`CascadingAuthenticationState` コンポーネントは、アプリの残りの部分への `AuthenticationState` の公開を管理します。</span><span class="sxs-lookup"><span data-stu-id="1f648-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="1f648-103">`AuthorizeRouteView` コンポーネントは、現在のユーザーが特定のページにアクセスする権限を持っていることを確認するか、または `RedirectToLogin` コンポーネントをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1f648-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="1f648-104">`RedirectToLogin` コンポーネントは、許可されていないユーザーのログインページへのリダイレクトを管理します。</span><span class="sxs-lookup"><span data-stu-id="1f648-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <RedirectToLogin />
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
