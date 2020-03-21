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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="3e0b3-102">ASP.NET Core Blazor Webasの追加のセキュリティシナリオ</span><span class="sxs-lookup"><span data-stu-id="3e0b3-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="3e0b3-103">[Javier Calvarro jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="3e0b3-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a><span data-ttu-id="3e0b3-104">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="3e0b3-104">Handle token request errors</span></span>

<span data-ttu-id="3e0b3-105">シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証すると、認証状態は SPA 内および Id プロバイダー (IP) 内でローカルに保持されます。これは、ユーザーが入力したセッションクッキーの形式で、認証.</span><span class="sxs-lookup"><span data-stu-id="3e0b3-105">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="3e0b3-106">通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-106">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="3e0b3-107">そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-107">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="3e0b3-108">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-108">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="3e0b3-109">場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-109">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="3e0b3-110">このシナリオは、ユーザーが `https://login.microsoftonline.com` にアクセスしてログアウトした場合に発生します。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-110">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="3e0b3-111">また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-111">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="3e0b3-112">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-112">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="3e0b3-113">これらは、SPAs の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-113">They are part of the nature of SPAs.</span></span> <span data-ttu-id="3e0b3-114">認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-114">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="3e0b3-115">アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-115">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="3e0b3-116">API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-116">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="3e0b3-117">クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-117">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="3e0b3-118">アプリがトークンを要求すると、次の2つの結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-118">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="3e0b3-119">要求が成功し、アプリに有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-119">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="3e0b3-120">要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-120">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="3e0b3-121">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-121">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="3e0b3-122">次のようないくつかの方法があり、複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-122">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="3e0b3-123">現在のページの状態をセッションストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-123">Store the current page state in session storage.</span></span> <span data-ttu-id="3e0b3-124">`OnInitializeAsync`中に、続行する前に状態を復元できるかどうかを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-124">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="3e0b3-125">クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-125">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="3e0b3-126">セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-126">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="3e0b3-127">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-127">The following example shows how to:</span></span>

* <span data-ttu-id="3e0b3-128">ログインページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-128">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="3e0b3-129">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-129">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="3e0b3-130">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="3e0b3-130">Save app state before an authentication operation</span></span>

<span data-ttu-id="3e0b3-131">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-131">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="3e0b3-132">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-132">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="3e0b3-133">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-133">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="3e0b3-134">`Authentication` コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="3e0b3-134">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="request-additional-access-tokens"></a><span data-ttu-id="3e0b3-135">追加のアクセストークンを要求する</span><span class="sxs-lookup"><span data-stu-id="3e0b3-135">Request additional access tokens</span></span>

<span data-ttu-id="3e0b3-136">ほとんどのアプリでは、使用する保護されたリソースと対話するためのアクセストークンのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-136">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="3e0b3-137">場合によっては、アプリで2つ以上のリソースを操作するために複数のトークンが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-137">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span> <span data-ttu-id="3e0b3-138">`IAccessTokenProvider.RequestToken` メソッドは、次の例に示すように、アプリが特定のスコープセットを使用してトークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-138">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a><span data-ttu-id="3e0b3-139">アプリルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="3e0b3-139">Customize app routes</span></span>

<span data-ttu-id="3e0b3-140">既定では、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-140">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="3e0b3-141">ルート</span><span class="sxs-lookup"><span data-stu-id="3e0b3-141">Route</span></span>                            | <span data-ttu-id="3e0b3-142">目的</span><span class="sxs-lookup"><span data-stu-id="3e0b3-142">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="3e0b3-143">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-143">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="3e0b3-144">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-144">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="3e0b3-145">何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-145">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="3e0b3-146">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-146">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="3e0b3-147">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-147">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="3e0b3-148">何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-148">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="3e0b3-149">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-149">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="3e0b3-150">ユーザープロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-150">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="3e0b3-151">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-151">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="3e0b3-152">上の表に示されているルートは、`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-152">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="3e0b3-153">カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-153">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="3e0b3-154">次の例では、すべてのパスの先頭に `/security`が付きます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-154">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="3e0b3-155">`Authentication` コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="3e0b3-155">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3e0b3-156">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3e0b3-156">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="3e0b3-157">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、明示的なアクションパラメーターを使用して `RemoteAuthenticatorView` を表示します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-157">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="3e0b3-158">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-158">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="3e0b3-159">認証ユーザーインターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="3e0b3-159">Customize the authentication user interface</span></span>

<span data-ttu-id="3e0b3-160">`RemoteAuthenticatorView` には、各認証状態の UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-160">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="3e0b3-161">各状態は、カスタム `RenderFragment`を渡すことによってカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-161">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="3e0b3-162">初期ログインプロセス中に表示されるテキストをカスタマイズするには、次のように `RemoteAuthenticatorView` を変更します。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-162">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="3e0b3-163">`Authentication` コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="3e0b3-163">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="3e0b3-164">`RemoteAuthenticatorView` には、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。</span><span class="sxs-lookup"><span data-stu-id="3e0b3-164">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="3e0b3-165">ルート</span><span class="sxs-lookup"><span data-stu-id="3e0b3-165">Route</span></span>                            | <span data-ttu-id="3e0b3-166">Fragment</span><span class="sxs-lookup"><span data-stu-id="3e0b3-166">Fragment</span></span>                |
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
