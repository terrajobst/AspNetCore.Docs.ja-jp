---
title: ASP.NETコアBlazorWeb アセンブリ追加のセキュリティ シナリオ
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501124"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET コア Blazor WebAssembly の追加のセキュリティ シナリオ

[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>追加のアクセス トークンを要求する

ほとんどのアプリは、使用する保護されたリソースと対話するためにアクセス トークンのみを必要とします。 シナリオによっては、アプリが複数のリソースとやり取りするために複数のトークンを必要とする場合があります。

次の例では、ユーザー データを読み取ってメールを送信するためにアプリで追加の Azure アクティブ ディレクトリ (AAD) Microsoft Graph API スコープが必要です。 Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、追加のスコープが`Program.Main`クライアント アプリ ( , *Program.cs)* で構成されます。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

この`IAccessTokenProvider.RequestToken`メソッドは、次の例に示すように、アプリが特定のスコープのセットでトークンをプロビジョニングできるようにするオーバーロードを提供します。

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`TryGetToken`返します：

* `true`を使用`token`します。
* `false`トークンが取得されない場合。

## <a name="handle-token-request-errors"></a>トークン要求エラーの処理

シングル ページ アプリケーション (SPA) が Open ID 接続 (OIDC) を使用してユーザーを認証する場合、認証状態は、ユーザーが資格情報を提供した結果として設定されたセッション Cookie の形式で、SPA 内および ID プロバイダー (IP) 内でローカルに維持されます。

通常、ユーザーに対して生成される IP トークンは、通常約 1 時間の短い期間有効であるため、クライアント アプリは定期的に新しいトークンをフェッチする必要があります。 そうしないと、付与されたトークンの有効期限が切れた後に、ユーザーがログアウトされます。 ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持されている"セッション"のおかげでユーザーが再認証を要求することなく、新しいトークンをプロビジョニングできます。

何らかの理由でユーザーが明示的に IP からログアウトする場合など、ユーザーの操作なしにクライアントがトークンを取得できない場合があります。 このシナリオは、ユーザーがアクセス`https://login.microsoftonline.com`してログアウトした場合に発生します。これらのシナリオでは、アプリは、ユーザーがログアウトしたことをすぐにはわかりません。クライアントが保持しているトークンは、もはや有効ではない可能性があります。 また、現在のトークンの有効期限が切れた後、クライアントはユーザーの操作なしで新しいトークンをプロビジョニングできません。

これらのシナリオは、トークンベースの認証に固有のものではありません。 彼らは、SPAの性質の一部です。 また、認証 Cookie が削除された場合、Cookie を使用する SPA はサーバー API の呼び出しに失敗します。

アプリが保護されたリソースに対して API 呼び出しを実行する場合は、次の点に注意する必要があります。

* 新しいアクセス トークンをプロビジョニングして API を呼び出すために、ユーザーが再び認証を受ける必要がある場合があります。
* クライアントに有効と思われるトークンがある場合でも、ユーザーがトークンを取り消したため、サーバーへの呼び出しが失敗する可能性があります。

アプリがトークンを要求すると、次の 2 つの結果が考えられます。

* 要求が成功し、アプリに有効なトークンが含まれています。
* 要求が失敗し、アプリは新しいトークンを取得するためにユーザーを再認証する必要があります。

トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。 複雑さのレベルが高まる中、いくつかのアプローチが存在します。

* 現在のページの状態をセッション ストレージに格納します。 の`OnInitializeAsync`間に、状態が復元可能かどうかを確認してから続行してください。
* クエリ文字列パラメーターを追加し、以前に保存した状態を再処理する必要があることをアプリに通知する方法として使用します。
* 一意の識別子を持つクエリ文字列パラメーターを追加して、他の項目との競合を危険にさらすことなく、データをセッション ストレージに格納します。

以下の例では、次のことを行っています。

* ログイン ページにリダイレクトする前に状態を保持します。
* クエリ文字列パラメーターを使用して、認証後に前の状態を回復します。

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

認証操作中に、ブラウザーが IP にリダイレクトされる前にアプリの状態を保存する必要がある場合があります。 これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。 カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはアプリケーションへの参照を保持し、認証操作が正常に完了した後にその状態を復元できます。

`Authentication`コンポーネント (*ページ/認証.カミソリ*):

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

## <a name="customize-app-routes"></a>アプリのルートをカスタマイズする

既定では、ライブラリ`Microsoft.AspNetCore.Components.WebAssembly.Authentication`は、次の表に示すルートを使用して、さまざまな認証状態を表します。

| ルート                            | 目的 |
| -------------------------------- | ------- |
| `authentication/login`           | サインイン操作をトリガーします。 |
| `authentication/login-callback`  | サインイン操作の結果を処理します。 |
| `authentication/login-failed`    | 何らかの理由でサインイン操作が失敗した場合にエラー メッセージを表示します。 |
| `authentication/logout`          | サインアウト操作をトリガーします。 |
| `authentication/logout-callback` | サインアウト操作の結果を処理します。 |
| `authentication/logout-failed`   | 何らかの理由でサインアウト操作が失敗した場合にエラー メッセージを表示します。 |
| `authentication/logged-out`      | ユーザーが正常にログアウトしたことを示します。 |
| `authentication/profile`         | ユーザー プロファイルを編集する操作をトリガーします。 |
| `authentication/register`        | 新しいユーザーを登録する操作をトリガーします。 |

上記の表に示したルートは、 を使用`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`して構成できます。 カスタム ルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。

次の例では、すべてのパスの先頭に`/security`.

`Authentication`コンポーネント (*ページ/認証.カミソリ*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

要件が完全に異なるパスを要求する場合は、前述のようにルートを設定し`RemoteAuthenticatorView`、明示的な action パラメーターを使用してをレンダリングします。

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

この操作を行う場合は、UI を別のページに分割できます。

## <a name="customize-the-authentication-user-interface"></a>認証ユーザー インターフェイスのカスタマイズ

`RemoteAuthenticatorView`には、各認証状態に対する UI 部分の既定のセットが含まれています。 各状態は、カスタム`RenderFragment`を渡すことによってカスタマイズできます。 初期ログイン処理中に表示されるテキストをカスタマイズするには、次`RemoteAuthenticatorView`のように変更できます。

`Authentication`コンポーネント (*ページ/認証.カミソリ*):

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

には`RemoteAuthenticatorView`、次の表に示す認証ルートごとに使用できるフラグメントが 1 つ含まれます。

| ルート                            | フラグメント                |
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
