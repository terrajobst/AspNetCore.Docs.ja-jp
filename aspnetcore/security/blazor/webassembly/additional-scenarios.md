---
title: Blazor WebasASP.NET Core 追加のセキュリティシナリオ
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: ccb512392341e3eea33f4ab45740b7900f7b63f9
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989456"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor Webasの追加のセキュリティシナリオ

[Javier Calvarro jeannine](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a>トークン要求エラーを処理する

シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証すると、認証状態は SPA 内および Id プロバイダー (IP) 内でローカルに保持されます。これは、ユーザーが入力したセッションクッキーの形式で、認証.

通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。 そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。 ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。

場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。 このシナリオは、ユーザーが `https://login.microsoftonline.com` にアクセスしてログアウトした場合に発生します。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。 また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。

これらのシナリオは、トークンベースの認証に固有のものではありません。 これらは、SPAs の性質の一部です。 認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。

アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。

* API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。
* クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。

アプリがトークンを要求すると、次の2つの結果が得られます。

* 要求が成功し、アプリに有効なトークンがあります。
* 要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。

トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。 次のようないくつかの方法があり、複雑さが増します。

* 現在のページの状態をセッションストレージに格納します。 `OnInitializeAsync`中に、続行する前に状態を復元できるかどうかを確認してください。
* クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。
* セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。

以下の例では、次のことを行っています。

* ログインページにリダイレクトする前に状態を保持します。
* クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>認証操作の前にアプリの状態を保存する

認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。 これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。 カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。

`Authentication` コンポーネント (*Pages/Authentication. razor*):

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="request-additional-access-tokens"></a>追加のアクセストークンを要求する

ほとんどのアプリでは、使用する保護されたリソースと対話するためのアクセストークンのみが必要です。 場合によっては、アプリで2つ以上のリソースを操作するために複数のトークンが必要になることがあります。 `IAccessTokenProvider.RequestToken` メソッドは、次の例に示すように、アプリが特定のスコープセットを使用してトークンをプロビジョニングできるようにするオーバーロードを提供します。

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a>アプリルートをカスタマイズする

既定では、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。

| ルート                            | 目的 |
| -------------------------------- | ------- |
| `authentication/login`           | サインイン操作をトリガーします。 |
| `authentication/login-callback`  | サインイン操作の結果を処理します。 |
| `authentication/login-failed`    | 何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。 |
| `authentication/logout`          | サインアウト操作をトリガーします。 |
| `authentication/logout-callback` | サインアウト操作の結果を処理します。 |
| `authentication/logout-failed`   | 何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。 |
| `authentication/logged-out`      | ユーザーが正常にログアウトしたことを示します。 |
| `authentication/profile`         | ユーザープロファイルを編集する操作をトリガーします。 |
| `authentication/register`        | 新しいユーザーを登録する操作をトリガーします。 |

上の表に示されているルートは、`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`を使用して構成できます。 カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。

次の例では、すべてのパスの先頭に `/security`が付きます。

`Authentication` コンポーネント (*Pages/Authentication. razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

完全に異なるパスを必要とする場合は、前述のようにルートを設定し、明示的なアクションパラメーターを使用して `RemoteAuthenticatorView` を表示します。

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

UI を別のページに分割することもできます。

## <a name="customize-the-authentication-user-interface"></a>認証ユーザーインターフェイスをカスタマイズする

`RemoteAuthenticatorView` には、各認証状態の UI 部分の既定のセットが含まれています。 各状態は、カスタム `RenderFragment`を渡すことによってカスタマイズできます。 初期ログインプロセス中に表示されるテキストをカスタマイズするには、次のように `RemoteAuthenticatorView` を変更します。

`Authentication` コンポーネント (*Pages/Authentication. razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`RemoteAuthenticatorView` には、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。

| ルート                            | Fragment                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
