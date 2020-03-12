`App` コンポーネント (*app.xaml*) は、Blazor Server apps にある `App` コンポーネントに似ています。

* `CascadingAuthenticationState` コンポーネントは、アプリの残りの部分への `AuthenticationState` の公開を管理します。
* `AuthorizeRouteView` コンポーネントは、現在のユーザーが特定のページにアクセスする権限を持っていることを確認するか、または `RedirectToLogin` コンポーネントをレンダリングします。
* `RedirectToLogin` コンポーネントは、許可されていないユーザーのログインページへのリダイレクトを管理します。

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
