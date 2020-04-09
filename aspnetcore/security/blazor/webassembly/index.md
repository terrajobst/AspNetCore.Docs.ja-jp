---
title: ASP.NET Core Blazor WebAssembly をセキュリティで保護する
author: guardrex
description: シングル ページ アプリケーション (SPA) として Blazor WebAssemlby アプリをセキュリティで保護する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538981"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="27e9f-103">ASP.NET Core Blazor WebAssembly をセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="27e9f-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="27e9f-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="27e9f-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="27e9f-105"> WebAssembly アプリは、シングル ページ アプリケーション (SPA) と同じ方法でセキュリティ保護されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="27e9f-106">ユーザーを SPA で認証する方法はいくつかありますが、最も一般的で包括的な方法は、[Open ID Connect (OIDC)](https://openid.net/connect/) などの [OAuth 2.0 プロトコル](https://oauth.net/)に基づく実装を使用することです。</span><span class="sxs-lookup"><span data-stu-id="27e9f-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="27e9f-107">認証ライブラリ</span><span class="sxs-lookup"><span data-stu-id="27e9f-107">Authentication library</span></span>

Blazor<span data-ttu-id="27e9f-108"> WebAssembly は、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリを介して OIDC を使用したアプリの認証と認可をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="27e9f-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="27e9f-109">ライブラリには、ASP.NET Core バックエンドに対してシームレスに認証を行うための一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="27e9f-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="27e9f-110">ライブラリは ASP.NET Core の ID を [Identity Server](https://identityserver.io/) 上に構築された API 認可サポートと統合します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="27e9f-111">このライブラリは、OpenID Provider (OP) と呼ばれる OIDC をサポートするすべてのサードパーティ ID プロバイダー (IP) に対して認証できます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="27e9f-112">Blazor WebAssembly の認証のサポートは、基になる認証プロトコルの詳細を処理するために使用される、*oidc-client.js* ライブラリの上に構築されています。</span><span class="sxs-lookup"><span data-stu-id="27e9f-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="27e9f-113">SameSite Cookie の使用など、SPA を認証するためのその他のオプションも存在します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="27e9f-114">ただし、Blazor WebAssembly のエンジニアリング設計は、Blazor WebAssembly アプリでの認証に最適なオプションとして OAuth および OIDC にも採用されています。</span><span class="sxs-lookup"><span data-stu-id="27e9f-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="27e9f-115">[JSON Web トークン (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) に基づく[トークンベースの認証](xref:security/anti-request-forgery#token-based-authentication)は、機能とセキュリティ上の理由により、[Cookie ベースの認証](xref:security/anti-request-forgery#cookie-based-authentication) に基づいて選択されています。</span><span class="sxs-lookup"><span data-stu-id="27e9f-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="27e9f-116">トークンベースのプロトコルを使用すると、トークンがすべての要求で送信されないため、攻撃対象領域が小さくなります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="27e9f-117">サーバー エンドポイントでは、トークンが明示的に送信されるため、[クロスサイト リクエスト フォージェリ (CSRF)](xref:security/anti-request-forgery) に対する保護が必要ありません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="27e9f-118">これにより、MVC または Razor ページのアプリと共に、Blazor WebAssembly アプリをホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="27e9f-119">トークンの権限は Cookie よりも狭くなります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="27e9f-120">たとえば、該当する機能が明示的に実装されていない限り、トークンを使用してユーザー アカウントを管理したり、ユーザーのパスワードを変更したりできません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="27e9f-121">トークンの有効期間は短く、既定では 1 時間です。これにより、攻撃時間が制限されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="27e9f-122">トークンは、いつでも取り消すことができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="27e9f-123">自己完結型の JWT は、クライアントとサーバーの認証プロセスを保証します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="27e9f-124">たとえば、クライアントには、受信したトークンが正当であり、指定した認証プロセスの一環として出力されたことを検出して検証する手段があります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="27e9f-125">サード パーティが認証プロセスの途中でトークンを切り替えようとすると、クライアントは切り替えられたトークンを検出して使用しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="27e9f-126">OAuth および OIDC を使用したトークンは、アプリが安全であることを確認するために、正しく動作しているユーザー エージェントに依存しません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="27e9f-127">OAuth や OIDC などのトークンベースのプロトコルでは、同じセキュリティ特性のセットを使用して、ホストされたアプリとスタンドアロンのアプリの認証と認可を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="27e9f-128">OIDC を使用した認証プロセス</span><span class="sxs-lookup"><span data-stu-id="27e9f-128">Authentication process with OIDC</span></span>

<span data-ttu-id="27e9f-129">`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリには、OIDC を使用した認証と認可を実装するためのいくつかのプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="27e9f-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="27e9f-130">大まかにいうと、認証は次のようにして行われます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="27e9f-131">匿名ユーザーがログイン ボタンを選択する、または [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性が適用されたページを要求すると、そのユーザーがアプリのログイン ページ (`/authentication/login`) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="27e9f-132">ログイン ページで、認証ライブラリが承認エンドポイントへのリダイレクトを準備します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="27e9f-133">承認エンドポイントは、Blazor WebAssembly の外部にあり、別のオリジンでホストすることができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="27e9f-134">エンドポイントは、ユーザーが認証されているかどうかを判断し、応答として 1 つ以上のトークンを発行する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="27e9f-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="27e9f-135">認証ライブラリは、認証応答を受信するためのログイン コールバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="27e9f-136">ユーザーが認証されていない場合、そのユーザーは基になる認証システム (通常は ASP.NET Core の ID) にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="27e9f-137">ユーザーが既に認証されている場合、承認エンドポイントは適切なトークンを生成し、ブラウザーをログイン コールバック エンドポイント (`/authentication/login-callback`) にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="27e9f-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="27e9f-138">Blazor WebAssembly アプリでログイン コールバック エンドポイント (`/authentication/login-callback`) が読み込まれると、認証応答が処理されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="27e9f-139">認証プロセスが正常に完了した場合、ユーザーは認証され、必要に応じてユーザーが要求した元の保護された URL に戻されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="27e9f-140">何らかの理由で認証プロセスが失敗した場合、ユーザーはログイン失敗ページ (`/authentication/login-failed`) に送信され、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="27e9f-141">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="27e9f-141">Support prerendering with authentication</span></span>

<span data-ttu-id="27e9f-142">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="27e9f-143">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="27e9f-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="27e9f-144">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="27e9f-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="27e9f-145">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="27e9f-146">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="27e9f-147">サーバー アプリの `Startup.Configure` メソッドで、`endpoints.MapFallbackToFile("index.html")` を `endpoints.MapFallbackToPage("/_Host")` に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="27e9f-148">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="27e9f-149">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="27e9f-150">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="27e9f-151">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-151">Update the file's contents:</span></span>

* <span data-ttu-id="27e9f-152">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="27e9f-153">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="27e9f-154">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="27e9f-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="27e9f-155">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="27e9f-156">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="27e9f-157">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="27e9f-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="27e9f-158">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="27e9f-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="27e9f-159">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="27e9f-160">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="27e9f-160">In this scenario:</span></span>

* <span data-ttu-id="27e9f-161">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="27e9f-162">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="27e9f-163">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="27e9f-164">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="27e9f-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="27e9f-165">サードパーティ ログイン プロバイダーで ID を構成します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="27e9f-166">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="27e9f-167">ユーザーがログインすると、認証プロセスの一環としてアクセス トークンと更新トークンが ID によって収集されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="27e9f-168">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="27e9f-169">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="27e9f-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="27e9f-170">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="27e9f-171">そこから、サードパーティのアクセス トークンを使用して、クライアント上の ID から直接、サードパーティ API リソースを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="27e9f-172">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-172">We don't recommend this approach.</span></span> <span data-ttu-id="27e9f-173">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="27e9f-174">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="27e9f-175">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="27e9f-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="27e9f-176">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="27e9f-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="27e9f-177">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="27e9f-178">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="27e9f-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="27e9f-179">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="27e9f-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="27e9f-180">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="27e9f-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="27e9f-181">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="27e9f-182">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="27e9f-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="27e9f-183">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="27e9f-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
