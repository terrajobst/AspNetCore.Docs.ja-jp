---
title: ASP.NET Core Blazor の認証と承認
author: guardrex
description: Blazor の認証と承認のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501323"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="ac9f7-103">ASP.NET Core Blazor の認証と承認</span><span class="sxs-lookup"><span data-stu-id="ac9f7-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="ac9f7-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ac9f7-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="ac9f7-105">Blazor WebAssembly に適用されるこの記事のガイダンスは、ASP.NET Core Blazor WebAssembly テンプレート バージョン 3.2 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="ac9f7-106">Visual Studio バージョン 16.6 Preview 2 以降を使用していない場合は、「<xref:blazor/get-started>」のガイダンスに従って、最新の Blazor WebAssembly テンプレートを入手してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="ac9f7-107">ASP.NET Core は、Blazor アプリのセキュリティの構成と管理をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="ac9f7-108">Blazor サーバー アプリと Blazor WebAssembly アプリのセキュリティ シナリオは異なります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="ac9f7-109">Blazor サーバー アプリはサーバー上で動作するため、承認チェックでは以下のことを判断できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="ac9f7-110">ユーザーに表示される UI オプション (たとえば、ユーザーが利用できるメニュー エントリ)。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="ac9f7-111">アプリとコンポーネントの領域に対するアクセス規則。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="ac9f7-112"> WebAssembly アプリはクライアント上で動作します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="ac9f7-113">承認は、表示する UI オプションを決定するために "*のみ*" 使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="ac9f7-114">クライアント側のチェックはユーザーによって変更またはバイパスされる可能性があるため、Blazor WebAssembly アプリでは承認アクセス規則を適用できません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="ac9f7-115">[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization)は、ルーティング可能な Razor コンポーネントには適用されません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="ac9f7-116">ルーティング不可能な Razor コンポーネントが[ページに埋め込まれている](xref:blazor/integrate-components#render-components-from-a-page-or-view)場合、ページの承認規則は、Razor コンポーネントと、ページのコンテンツの残りの部分に間接的に影響します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="ac9f7-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> と <xref:Microsoft.AspNetCore.Identity.UserManager%601> は、Razor コンポーネントではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="ac9f7-118">認証</span><span class="sxs-lookup"><span data-stu-id="ac9f7-118">Authentication</span></span>

Blazor<span data-ttu-id="ac9f7-119"> は、既存の ASP.NET Core 認証メカニズムを使用してユーザーの ID を証明します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="ac9f7-120">詳細なメカニズムは、Blazor アプリのホスティング方法、Blazor WebAssembly か Blazor サーバーかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="ac9f7-121"> WebAssembly 認証</span><span class="sxs-lookup"><span data-stu-id="ac9f7-121"> WebAssembly authentication</span></span>

<span data-ttu-id="ac9f7-122">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、認証チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="ac9f7-123">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="ac9f7-124">以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-124">Add the following:</span></span>

* <span data-ttu-id="ac9f7-125">アプリのプロジェクト ファイルに、[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) のパッケージ参照。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="ac9f7-126">アプリの *_Imports.razor*ファイルに、`Microsoft.AspNetCore.Components.Authorization` 名前空間。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="ac9f7-127">認証を処理するために、組み込みまたはカスタムの `AuthenticationStateProvider` サービスの実装について、以降のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="ac9f7-128">アプリの作成と構成の詳細については、「<xref:security/blazor/webassembly/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="ac9f7-129"> サーバー認証</span><span class="sxs-lookup"><span data-stu-id="ac9f7-129"> Server authentication</span></span>

Blazor<span data-ttu-id="ac9f7-130"> サーバー アプリは、SignalR を使用して作成されたリアルタイム接続を介して動作します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="ac9f7-131">[SignalR ベースのアプリの認証](xref:signalr/authn-and-authz)は、接続が確立したときに処理されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="ac9f7-132">認証は、Cookie または他のベアラー トークンに基づいています。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="ac9f7-133">アプリの作成と構成の詳細については、「<xref:security/blazor/server>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="ac9f7-134">AuthenticationStateProvider サービス</span><span class="sxs-lookup"><span data-stu-id="ac9f7-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="ac9f7-135">組み込みの `AuthenticationStateProvider` サーバー アプリでは、ASP.NET Core の `HttpContext.User` から認証状態データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="ac9f7-136">このようにして、認証状態は既存の ASP.NET Core 認証メカニズムと統合されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="ac9f7-137">`AuthenticationStateProvider` は、認証状態を取得するために `AuthorizeView` コンポーネントと `CascadingAuthenticationState` コンポーネントによって使用される基となるサービスです。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="ac9f7-138">通常、`AuthenticationStateProvider` は直接使用しません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="ac9f7-139">この記事で後述する [AuthorizeView コンポーネント](#authorizeview-component)または [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="ac9f7-140">`AuthenticationStateProvider` を直接使用することの主な欠点は、基となる認証状態データが変更されてもコンポーネントに自動的に通知されないことです。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="ac9f7-141">次の例に示すように、`AuthenticationStateProvider` サービスから現在のユーザーの <xref:System.Security.Claims.ClaimsPrincipal> データを提供できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="ac9f7-142">`user.Identity.IsAuthenticated` が `true` である場合、ユーザーが <xref:System.Security.Claims.ClaimsPrincipal> であるため、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="ac9f7-143">依存関係の注入 (DI) とサービスの詳細については、<xref:blazor/dependency-injection> と <xref:fundamentals/dependency-injection> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="ac9f7-144">カスタム AuthenticationStateProvider の実装</span><span class="sxs-lookup"><span data-stu-id="ac9f7-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="ac9f7-145">アプリでカスタム プロバイダーを必要とする場合は、`AuthenticationStateProvider` を実装し、`GetAuthenticationStateAsync` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="ac9f7-146">Blazor WebAssembly アプリでは、`CustomAuthStateProvider` サービスは *Program.cs* の `Main` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="ac9f7-147">Blazor サーバー アプリでは、`CustomAuthStateProvider` サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="ac9f7-148">前の例で `CustomAuthStateProvider` を使用すると、すべてのユーザーはユーザー名 `mrfibuli` で認証されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="ac9f7-149">認証状態をカスケード パラメーターとして公開する</span><span class="sxs-lookup"><span data-stu-id="ac9f7-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="ac9f7-150">ユーザーによってトリガーされたアクションを実行する場合など、認証状態データが手続き型ロジックに必要な場合は、型 `Task<AuthenticationState>` のカスケード パラメーターを定義して認証状態データを取得します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="ac9f7-151">`user.Identity.IsAuthenticated` が `true` である場合、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="ac9f7-152">`App` コンポーネント (*App.razor*) 内の `AuthorizeRouteView` および `CascadingAuthenticationState` コンポーネントを使用して、`Task<AuthenticationState>` カスケード パラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="ac9f7-153">Blazor WebAssembly アプリで、オプションと承認のためのサービスを `Program.Main` に追加します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="ac9f7-154">Blazor サーバー アプリでは、オプションと承認のためのサービスが既に存在するため、これ以上の操作は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="ac9f7-155">承認</span><span class="sxs-lookup"><span data-stu-id="ac9f7-155">Authorization</span></span>

<span data-ttu-id="ac9f7-156">ユーザーが認証されると、ユーザーが実行できる操作を制御する "*承認*" 規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="ac9f7-157">通常、アクセスは以下の条件に基づいて許可または拒否されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="ac9f7-158">ユーザーが認証されている (サインインしている)。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="ac9f7-159">ユーザーに "*ロール*" が割り当てられている。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-159">A user is in a *role*.</span></span>
* <span data-ttu-id="ac9f7-160">ユーザーに "*要求*" がある。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-160">A user has a *claim*.</span></span>
* <span data-ttu-id="ac9f7-161">"*ポリシー*" が満たされている。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="ac9f7-162">これらの各概念は、ASP.NET Core MVC または Razor Pages アプリと同じです。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="ac9f7-163">ASP.NET Core のセキュリティの詳細については、[ASP.NET Core のセキュリティと ID](xref:security/index) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="ac9f7-164">AuthorizeView コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ac9f7-164">AuthorizeView component</span></span>

<span data-ttu-id="ac9f7-165">`AuthorizeView` コンポーネントでは、ユーザーに表示を許可するかどうかに応じて UI が選択的に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="ac9f7-166">このアプローチは、ユーザーに対してデータを "*表示する*" だけで済み、手続き型ロジックでユーザーの ID を使用する必要がない場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="ac9f7-167">このコンポーネントでは、型 `AuthenticationState` の `context` 変数が公開されており、これを使用して、サインインしたユーザーに関する情報にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="ac9f7-168">ユーザーが認証されていない場合は、表示用に異なるコンテンツを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-168">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="ac9f7-169">`AuthorizeView` コンポーネントは、`NavMenu` コンポーネント (*Shared/NavMenu.razor*) で使用して `NavLink` のリスト項目 (`<li>...</li>`) を表示できますが、この方法では、表示された出力からリスト項目が削除されるだけであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="ac9f7-170">ユーザーがコンポーネントに移動するのを防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="ac9f7-171">`<Authorized>` および `<NotAuthorized>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="ac9f7-172">UI オプションまたはアクセスを制御するロールやポリシーなどの承認条件については、「[承認](#authorization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="ac9f7-173">承認条件が指定されていない場合、`AuthorizeView` では既定のポリシーが使用され、以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="ac9f7-174">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="ac9f7-175">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="ac9f7-176">ロールベースとリソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="ac9f7-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="ac9f7-177">`AuthorizeView` コンポーネントは、"*ロールベース*" または "*ポリシーベース*" の承認をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="ac9f7-178">ロールベースの承認の場合は、`Roles` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="ac9f7-179">詳細については、「<xref:security/authorization/roles>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="ac9f7-180">ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="ac9f7-181">要求ベースの承認は、ポリシーベースの承認の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="ac9f7-182">たとえば、ユーザーが特定の要求持つことを必須にするポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="ac9f7-183">詳細については、「<xref:security/authorization/policies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="ac9f7-184">これらの API は、Blazor サーバー アプリまたは Blazor WebAssembly アプリのどちらでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ac9f7-185">`Roles` も `Policy` も指定されていない場合、`AuthorizeView` には既定のポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="ac9f7-186">非同期認証中に表示されるコンテンツ</span><span class="sxs-lookup"><span data-stu-id="ac9f7-186">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="ac9f7-187"> では、認証状態を "*非同期的に*" 決定することができます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-187"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="ac9f7-188">このアプローチの主なシナリオは、認証のために外部エンドポイントに要求を送信する Blazor WebAssembly アプリです。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="ac9f7-189">認証が進行中の間、`AuthorizeView` には既定でコンテンツが表示されません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="ac9f7-190">認証が行われている間にコンテンツを表示するには、`<Authorizing>` 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="ac9f7-191">通常、このアプローチは Blazor サーバー アプリには適用されません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="ac9f7-192"> サーバー アプリでは、状態が確立されるとすぐに認証状態が認識されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="ac9f7-193">`Authorizing` コンテンツは Blazor サーバー アプリの `AuthorizeView` コンポーネントで提供できますが、コンテンツは表示されません。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="ac9f7-194">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="ac9f7-194">[Authorize] attribute</span></span>

<span data-ttu-id="ac9f7-195">`[Authorize]` 属性は Razor コンポーネント内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="ac9f7-196">Blazor ルーター経由で到達した `@page` コンポーネントにのみ `[Authorize]` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="ac9f7-197">承認はルーティングの一面としてのみ実行され、ページ内にレンダリングされた子コンポーネントに対しては実行され "*ません*"。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="ac9f7-198">ページ内の特定部分の表示を承認するには、代わりに `AuthorizeView` を使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="ac9f7-199">`[Authorize]` 属性は、ロールベースまたはポリシーベースの承認もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="ac9f7-200">ロールベースの承認の場合は、`Roles` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="ac9f7-201">ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="ac9f7-202">`Roles` も `Policy` も指定されていない場合、`[Authorize]` には既定のポリシーが使用されます。これは既定で以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="ac9f7-203">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="ac9f7-204">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="ac9f7-205">Router コンポーネントを使用して承認されていないコンテンツをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="ac9f7-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="ac9f7-206">`Router` コンポーネントを `AuthorizeRouteView` コンポーネントとともに使用すると、以下の場合にアプリがカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="ac9f7-207">コンテンツが見つからない。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-207">Content isn't found.</span></span>
* <span data-ttu-id="ac9f7-208">ユーザーはコンポーネントに適用されている `[Authorize]` 条件に失敗します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="ac9f7-209">`[Authorize]` 属性については、「[`[Authorize]` 属性](#authorize-attribute)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="ac9f7-210">非同期認証が実行中です。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="ac9f7-211">既定の Blazor サーバー プロジェクト テンプレートでは、`App` component (*App.razor*) によりカスタム コンテンツの設定方法が示されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="ac9f7-212">`<NotFound>`、`<NotAuthorized>`、および `<Authorizing>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="ac9f7-213">`<NotAuthorized>` 要素が指定されていない場合、`AuthorizeRouteView` には次のフォールバック メッセージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="ac9f7-214">認証状態の変更に関する通知</span><span class="sxs-lookup"><span data-stu-id="ac9f7-214">Notification about authentication state changes</span></span>

<span data-ttu-id="ac9f7-215">基となる認証状態データが変わったとアプリで判断された場合 (たとえば、ユーザーがサインアウトした、または別のユーザーがロールを変更したなど)、[カスタムの AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) はオプションで `AuthenticationStateProvider` 基底クラスのメソッド `NotifyAuthenticationStateChanged` を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="ac9f7-216">これにより、新しいデータを使用して再表示するように、認証状態データ (`AuthorizeView` など) がコンシューマーに通知されます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="ac9f7-217">手続き型ロジック</span><span class="sxs-lookup"><span data-stu-id="ac9f7-217">Procedural logic</span></span>

<span data-ttu-id="ac9f7-218">手続き型ロジックの一部としてアプリで承認規則をチェックする必要がある場合、型 `Task<AuthenticationState>` のカスケード パラメーターを使用してユーザーの <xref:System.Security.Claims.ClaimsPrincipal> を取得します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="ac9f7-219">ポリシーを評価するために、`Task<AuthenticationState>` を `IAuthorizationService` などの他のサービスと組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="ac9f7-220">Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Authorization` と `Microsoft.AspNetCore.Components.Authorization` の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="ac9f7-221">これらの名前空間は、アプリの *_Imports.razor* ファイルに追加することで、グローバルに提供できます。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="ac9f7-222">Blazor WebAssembly アプリでの承認</span><span class="sxs-lookup"><span data-stu-id="ac9f7-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="ac9f7-223">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、承認チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="ac9f7-224">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="ac9f7-225">**常に、クライアント側アプリからアクセスされるすべての API エンドポイント内のサーバー上で承認チェックを実行します。**</span><span class="sxs-lookup"><span data-stu-id="ac9f7-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="ac9f7-226">詳細については、<xref:security/blazor/webassembly/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="ac9f7-227">エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="ac9f7-227">Troubleshoot errors</span></span>

<span data-ttu-id="ac9f7-228">一般的なエラー:</span><span class="sxs-lookup"><span data-stu-id="ac9f7-228">Common errors:</span></span>

* <span data-ttu-id="ac9f7-229">**承認には、型 Task\<AuthenticationState> のカスケード パラメーターが必要です。これを実行するには CascadingAuthenticationState の使用を検討します。**</span><span class="sxs-lookup"><span data-stu-id="ac9f7-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="ac9f7-230">`authenticationStateTask` **に対して**`null` 値を受け取ります</span><span class="sxs-lookup"><span data-stu-id="ac9f7-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="ac9f7-231">認証が有効な Blazor サーバー テンプレートを使用してプロジェクトが作成されなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="ac9f7-232">UI ツリーの一部に `<CascadingAuthenticationState>` をラップします。たとえば、`App` コンポーネント (*App.razor*) で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="ac9f7-233">`CascadingAuthenticationState` には `Task<AuthenticationState>` カスケード パラメーターが用意されており、次に基となる `AuthenticationStateProvider` DI サービスから受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ac9f7-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac9f7-234">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ac9f7-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="ac9f7-235">[すばらしい Blazor: 認証](https://github.com/AdrienTorris/awesome-blazor#authentication) コミュニティのサンプル リンク</span><span class="sxs-lookup"><span data-stu-id="ac9f7-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
