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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="ccb5b-102">ASP.NET コア Blazor WebAssembly の追加のセキュリティ シナリオ</span><span class="sxs-lookup"><span data-stu-id="ccb5b-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="ccb5b-103">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="ccb5b-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="ccb5b-104">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="ccb5b-104">Request additional access tokens</span></span>

<span data-ttu-id="ccb5b-105">ほとんどのアプリは、使用する保護されたリソースと対話するためにアクセス トークンのみを必要とします。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="ccb5b-106">シナリオによっては、アプリが複数のリソースとやり取りするために複数のトークンを必要とする場合があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="ccb5b-107">次の例では、ユーザー データを読み取ってメールを送信するためにアプリで追加の Azure アクティブ ディレクトリ (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="ccb5b-108">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、追加のスコープが`Program.Main`クライアント アプリ ( , *Program.cs)* で構成されます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="ccb5b-109">この`IAccessTokenProvider.RequestToken`メソッドは、次の例に示すように、アプリが特定のスコープのセットでトークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="ccb5b-110">`TryGetToken`返します：</span><span class="sxs-lookup"><span data-stu-id="ccb5b-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="ccb5b-111">`true`を使用`token`します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="ccb5b-112">`false`トークンが取得されない場合。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="ccb5b-113">トークン要求エラーの処理</span><span class="sxs-lookup"><span data-stu-id="ccb5b-113">Handle token request errors</span></span>

<span data-ttu-id="ccb5b-114">シングル ページ アプリケーション (SPA) が Open ID 接続 (OIDC) を使用してユーザーを認証する場合、認証状態は、ユーザーが資格情報を提供した結果として設定されたセッション Cookie の形式で、SPA 内および ID プロバイダー (IP) 内でローカルに維持されます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="ccb5b-115">通常、ユーザーに対して生成される IP トークンは、通常約 1 時間の短い期間有効であるため、クライアント アプリは定期的に新しいトークンをフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="ccb5b-116">そうしないと、付与されたトークンの有効期限が切れた後に、ユーザーがログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="ccb5b-117">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持されている"セッション"のおかげでユーザーが再認証を要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="ccb5b-118">何らかの理由でユーザーが明示的に IP からログアウトする場合など、ユーザーの操作なしにクライアントがトークンを取得できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="ccb5b-119">このシナリオは、ユーザーがアクセス`https://login.microsoftonline.com`してログアウトした場合に発生します。これらのシナリオでは、アプリは、ユーザーがログアウトしたことをすぐにはわかりません。クライアントが保持しているトークンは、もはや有効ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="ccb5b-120">また、現在のトークンの有効期限が切れた後、クライアントはユーザーの操作なしで新しいトークンをプロビジョニングできません。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="ccb5b-121">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="ccb5b-122">彼らは、SPAの性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="ccb5b-123">また、認証 Cookie が削除された場合、Cookie を使用する SPA はサーバー API の呼び出しに失敗します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="ccb5b-124">アプリが保護されたリソースに対して API 呼び出しを実行する場合は、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="ccb5b-125">新しいアクセス トークンをプロビジョニングして API を呼び出すために、ユーザーが再び認証を受ける必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="ccb5b-126">クライアントに有効と思われるトークンがある場合でも、ユーザーがトークンを取り消したため、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="ccb5b-127">アプリがトークンを要求すると、次の 2 つの結果が考えられます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="ccb5b-128">要求が成功し、アプリに有効なトークンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="ccb5b-129">要求が失敗し、アプリは新しいトークンを取得するためにユーザーを再認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="ccb5b-130">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="ccb5b-131">複雑さのレベルが高まる中、いくつかのアプローチが存在します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="ccb5b-132">現在のページの状態をセッション ストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-132">Store the current page state in session storage.</span></span> <span data-ttu-id="ccb5b-133">の`OnInitializeAsync`間に、状態が復元可能かどうかを確認してから続行してください。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="ccb5b-134">クエリ文字列パラメーターを追加し、以前に保存した状態を再処理する必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="ccb5b-135">一意の識別子を持つクエリ文字列パラメーターを追加して、他の項目との競合を危険にさらすことなく、データをセッション ストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="ccb5b-136">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-136">The following example shows how to:</span></span>

* <span data-ttu-id="ccb5b-137">ログイン ページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="ccb5b-138">クエリ文字列パラメーターを使用して、認証後に前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="ccb5b-139">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="ccb5b-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="ccb5b-140">認証操作中に、ブラウザーが IP にリダイレクトされる前にアプリの状態を保存する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="ccb5b-141">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="ccb5b-142">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはアプリケーションへの参照を保持し、認証操作が正常に完了した後にその状態を復元できます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="ccb5b-143">`Authentication`コンポーネント (*ページ/認証.カミソリ*):</span><span class="sxs-lookup"><span data-stu-id="ccb5b-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="ccb5b-144">アプリのルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ccb5b-144">Customize app routes</span></span>

<span data-ttu-id="ccb5b-145">既定では、ライブラリ`Microsoft.AspNetCore.Components.WebAssembly.Authentication`は、次の表に示すルートを使用して、さまざまな認証状態を表します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="ccb5b-146">ルート</span><span class="sxs-lookup"><span data-stu-id="ccb5b-146">Route</span></span>                            | <span data-ttu-id="ccb5b-147">目的</span><span class="sxs-lookup"><span data-stu-id="ccb5b-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="ccb5b-148">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="ccb5b-149">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="ccb5b-150">何らかの理由でサインイン操作が失敗した場合にエラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="ccb5b-151">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="ccb5b-152">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="ccb5b-153">何らかの理由でサインアウト操作が失敗した場合にエラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="ccb5b-154">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="ccb5b-155">ユーザー プロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="ccb5b-156">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="ccb5b-157">上記の表に示したルートは、 を使用`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`して構成できます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="ccb5b-158">カスタム ルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="ccb5b-159">次の例では、すべてのパスの先頭に`/security`.</span><span class="sxs-lookup"><span data-stu-id="ccb5b-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="ccb5b-160">`Authentication`コンポーネント (*ページ/認証.カミソリ*):</span><span class="sxs-lookup"><span data-stu-id="ccb5b-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="ccb5b-161">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="ccb5b-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="ccb5b-162">要件が完全に異なるパスを要求する場合は、前述のようにルートを設定し`RemoteAuthenticatorView`、明示的な action パラメーターを使用してをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="ccb5b-163">この操作を行う場合は、UI を別のページに分割できます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="ccb5b-164">認証ユーザー インターフェイスのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="ccb5b-164">Customize the authentication user interface</span></span>

<span data-ttu-id="ccb5b-165">`RemoteAuthenticatorView`には、各認証状態に対する UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="ccb5b-166">各状態は、カスタム`RenderFragment`を渡すことによってカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="ccb5b-167">初期ログイン処理中に表示されるテキストをカスタマイズするには、次`RemoteAuthenticatorView`のように変更できます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="ccb5b-168">`Authentication`コンポーネント (*ページ/認証.カミソリ*):</span><span class="sxs-lookup"><span data-stu-id="ccb5b-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="ccb5b-169">には`RemoteAuthenticatorView`、次の表に示す認証ルートごとに使用できるフラグメントが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="ccb5b-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="ccb5b-170">ルート</span><span class="sxs-lookup"><span data-stu-id="ccb5b-170">Route</span></span>                            | <span data-ttu-id="ccb5b-171">フラグメント</span><span class="sxs-lookup"><span data-stu-id="ccb5b-171">Fragment</span></span>                |
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
