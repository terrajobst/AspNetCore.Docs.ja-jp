---
title: コアBlazorサーバー アプリASP.NETセキュリティ保護
author: guardrex
description: サーバー アプリに対するセキュリティBlazor上の脅威を軽減する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626019"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="a9435-103">コアブレイザサーバーアプリASP.NETセキュア</span><span class="sxs-lookup"><span data-stu-id="a9435-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="a9435-104">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a9435-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="a9435-105">Blazor Server アプリは、サーバーとクライアントが長期間の関係を維持する*ステートフル*なデータ処理モデルを採用しています。</span><span class="sxs-lookup"><span data-stu-id="a9435-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="a9435-106">持続状態は、潜在的に長寿命の接続にまたがる可能性がある[回線](xref:blazor/state-management)によって維持されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="a9435-107">ユーザーが Blazor サーバー サイトにアクセスすると、サーバーはサーバーのメモリに回線を作成します。</span><span class="sxs-lookup"><span data-stu-id="a9435-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="a9435-108">この回線は、ユーザーが UI のボタンを選択したときなど、レンダリングするコンテンツをブラウザーに示し、イベントに応答します。</span><span class="sxs-lookup"><span data-stu-id="a9435-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="a9435-109">これらのアクションを実行するために、回線はユーザーのブラウザーで JavaScript 関数を呼び出し、サーバー上の .NET メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a9435-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="a9435-110">この双方向の JavaScript ベースの対話は[、JavaScript 相互運用機能 (JS 相互運用)](xref:blazor/call-javascript-from-dotnet)と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a9435-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="a9435-111">JS 相互運用機能はインターネット上で発生し、クライアントはリモート ブラウザーを使用するため、Blazor Server アプリは Web アプリのセキュリティに関する懸念を共有します。</span><span class="sxs-lookup"><span data-stu-id="a9435-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="a9435-112">このトピックでは、Blazor Server アプリに対する一般的な脅威について説明し、インターネットに接続するアプリに焦点を当てた脅威軽減ガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a9435-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="a9435-113">社内ネットワークやイントラネットなど、制約のある環境では、次のいずれかの軽減策のガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="a9435-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="a9435-114">制約された環境では適用されません。</span><span class="sxs-lookup"><span data-stu-id="a9435-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="a9435-115">制限された環境ではセキュリティ リスクが低いため、実装にかかるコストに値しません。</span><span class="sxs-lookup"><span data-stu-id="a9435-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="a9435-116">ブラゾール サーバー プロジェクト テンプレート</span><span class="sxs-lookup"><span data-stu-id="a9435-116">Blazor Server project template</span></span>

<span data-ttu-id="a9435-117">Blazor サーバーのプロジェクト テンプレートは、プロジェクトの作成時に認証用に構成できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9435-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9435-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9435-119">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a9435-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="a9435-120">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、**[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a9435-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="a9435-121">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="a9435-122">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="a9435-122">**No Authentication**</span></span>
* <span data-ttu-id="a9435-123">**個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="a9435-124">ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="a9435-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="a9435-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)を使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="a9435-126">**職場または学校のアカウント**</span><span class="sxs-lookup"><span data-stu-id="a9435-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="a9435-127">**[Windows 認証]**</span><span class="sxs-lookup"><span data-stu-id="a9435-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a9435-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9435-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a9435-129">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a9435-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="a9435-130">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="a9435-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="a9435-131">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="a9435-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="a9435-132">`{AUTHENTICATION}` 値</span><span class="sxs-lookup"><span data-stu-id="a9435-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="a9435-133">[認証なし]</span><span class="sxs-lookup"><span data-stu-id="a9435-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="a9435-134">個人</span><span class="sxs-lookup"><span data-stu-id="a9435-134">Individual</span></span><br><span data-ttu-id="a9435-135">ASP.NET Core ID を使用するアプリに格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="a9435-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="a9435-136">個人</span><span class="sxs-lookup"><span data-stu-id="a9435-136">Individual</span></span><br><span data-ttu-id="a9435-137">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="a9435-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="a9435-138">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="a9435-138">Work or School Accounts</span></span><br><span data-ttu-id="a9435-139">単一のテナントに対する組織認証。</span><span class="sxs-lookup"><span data-stu-id="a9435-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="a9435-140">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="a9435-140">Work or School Accounts</span></span><br><span data-ttu-id="a9435-141">複数のテナントに対する組織認証です。</span><span class="sxs-lookup"><span data-stu-id="a9435-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="a9435-142">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="a9435-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="a9435-143">このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="a9435-144">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a9435-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a9435-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a9435-146">この記事の「Mac 用の<xref:blazor/get-started>Visual Studio」のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a9435-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="a9435-147">新**しい Blazor サーバー アプリの構成**ステップで、[**認証**] ドロップダウンから **[個別認証 (アプリ内)] を**選択します。</span><span class="sxs-lookup"><span data-stu-id="a9435-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="a9435-148">アプリは、ASP.NETコア ID を使用してアプリに保存された個々のユーザー用に作成されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9435-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9435-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="a9435-150">この記事の .NET Core <xref:blazor/get-started> CLI ガイダンスに従って、認証メカニズムを使用して新しい Blazor Server プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a9435-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="a9435-151">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="a9435-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="a9435-152">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="a9435-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="a9435-153">`{AUTHENTICATION}` 値</span><span class="sxs-lookup"><span data-stu-id="a9435-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="a9435-154">[認証なし]</span><span class="sxs-lookup"><span data-stu-id="a9435-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="a9435-155">個人</span><span class="sxs-lookup"><span data-stu-id="a9435-155">Individual</span></span><br><span data-ttu-id="a9435-156">ASP.NET Core ID を使用するアプリに格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="a9435-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="a9435-157">個人</span><span class="sxs-lookup"><span data-stu-id="a9435-157">Individual</span></span><br><span data-ttu-id="a9435-158">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="a9435-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="a9435-159">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="a9435-159">Work or School Accounts</span></span><br><span data-ttu-id="a9435-160">単一のテナントに対する組織認証。</span><span class="sxs-lookup"><span data-stu-id="a9435-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="a9435-161">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="a9435-161">Work or School Accounts</span></span><br><span data-ttu-id="a9435-162">複数のテナントに対する組織認証です。</span><span class="sxs-lookup"><span data-stu-id="a9435-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="a9435-163">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="a9435-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="a9435-164">このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="a9435-165">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="a9435-166">Blazor サーバー アプリにトークンを渡す</span><span class="sxs-lookup"><span data-stu-id="a9435-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="a9435-167">通常の Razor ページまたは MVC アプリと同様に、Blazor サーバー アプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="a9435-168">トークンをプロビジョニングして、認証 Cookie に保存します。</span><span class="sxs-lookup"><span data-stu-id="a9435-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="a9435-169">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a9435-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="a9435-170">完全な`Startup.ConfigureServices`例を含むサンプル コードについては、「[サーバー側の Blazor アプリケーションへのトークンの引き渡し](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="a9435-171">アクセス トークンと更新トークンを使用して、初期アプリの状態で渡すクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="a9435-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="a9435-172">Di からトークンを解決するために Blazor アプリ内で使用できる**スコープトークン**プロバイダー サービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="a9435-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="a9435-173">で`Startup.ConfigureServices`、次のサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="a9435-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="a9435-174">*_Host.cshtml*ファイルで、作成してインスタンスを`InitialApplicationState`作成し、パラメーターとしてアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="a9435-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="a9435-175">`App`コンポーネント (*App.razor*) でサービスを解決し、パラメータのデータで初期化します。</span><span class="sxs-lookup"><span data-stu-id="a9435-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="a9435-176">セキュアな API 要求を行うサービスで、トークンプロバイダーを注入し、API を呼び出すためのトークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="a9435-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="a9435-177">リソースの枯渇</span><span class="sxs-lookup"><span data-stu-id="a9435-177">Resource exhaustion</span></span>

<span data-ttu-id="a9435-178">クライアントがサーバーと対話し、サーバーが過剰なリソースを消費する場合、リソースの枯渇が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="a9435-179">過度なリソース消費は主に以下に影響します。</span><span class="sxs-lookup"><span data-stu-id="a9435-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="a9435-180">Cpu</span><span class="sxs-lookup"><span data-stu-id="a9435-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="a9435-181">メモリ</span><span class="sxs-lookup"><span data-stu-id="a9435-181">Memory</span></span>](#memory)
* [<span data-ttu-id="a9435-182">クライアント接続</span><span class="sxs-lookup"><span data-stu-id="a9435-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="a9435-183">通常、サービス拒否 (DoS) 攻撃は、アプリやサーバーのリソースを使い果たそうとします。</span><span class="sxs-lookup"><span data-stu-id="a9435-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="a9435-184">ただし、リソースの枯渇は、必ずしもシステムへの攻撃の結果ではありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="a9435-185">たとえば、ユーザーの需要が高いため、有限のリソースが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="a9435-186">DoS については、[サービス拒否 (DoS) 攻撃](#denial-of-service-dos-attacks)のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="a9435-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="a9435-187">また、データベースやファイル ハンドル (ファイルの読み取りと書き込みに使用) など、Blazor フレームワークの外部にあるリソースでも、リソースの枯渇が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="a9435-188">詳細については、「<xref:performance/performance-best-practices>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="a9435-189">CPU</span><span class="sxs-lookup"><span data-stu-id="a9435-189">CPU</span></span>

<span data-ttu-id="a9435-190">CPU の枯渇は、1 つ以上のクライアントがサーバーに CPU の集中的な処理を強制的に実行させた場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="a9435-191">たとえば、*フィボナクッチ数*を計算する Blazor Server アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a9435-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="a9435-192">フィボナッチ数はフィボナッチ数列から生成され、シーケンス内の各番号は先行する 2 つの数値の合計になります。</span><span class="sxs-lookup"><span data-stu-id="a9435-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="a9435-193">応答に到達するために必要な作業量は、シーケンスの長さと初期値のサイズによって異なります。</span><span class="sxs-lookup"><span data-stu-id="a9435-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="a9435-194">アプリがクライアントの要求に制限を設けなければ、CPU を大量に消費する計算が CPU 時間を制限し、他のタスクのパフォーマンスを低下させる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="a9435-195">リソースの過剰消費は、可用性に影響を与えるセキュリティ上の問題です。</span><span class="sxs-lookup"><span data-stu-id="a9435-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="a9435-196">CPUの枯渇は、すべての一般向けアプリにとって懸念事項です。</span><span class="sxs-lookup"><span data-stu-id="a9435-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="a9435-197">通常の Web アプリでは、要求と接続はセーフガードとしてタイムアウトしますが、Blazor Server アプリは同じ保護機能を提供していません。</span><span class="sxs-lookup"><span data-stu-id="a9435-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="a9435-198">Blazor Server アプリは、CPU を大量に消費する可能性のある作業を実行する前に、適切なチェックと制限を含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="a9435-199">メモリ</span><span class="sxs-lookup"><span data-stu-id="a9435-199">Memory</span></span>

<span data-ttu-id="a9435-200">1 つ以上のクライアントがサーバーに大量のメモリを強制的に消費させた場合に、メモリの枯渇が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="a9435-201">たとえば、項目のリストを受け入れて表示するコンポーネントを持つ Blazor サーバー側のアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a9435-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="a9435-202">Blazor アプリが、許可されている項目の数やクライアントに戻される項目数に制限を設けなければ、メモリを消費する処理とレンダリングがサーバーのメモリを支配し、サーバーのパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="a9435-203">サーバーがクラッシュしたり、クラッシュしたと思われる時点まで遅くなったりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="a9435-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="a9435-204">サーバー上で発生する可能性のあるメモリ不足のシナリオに関連する項目の一覧を保守および表示する場合は、次のシナリオを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="a9435-205">プロパティまたはフィールド内`List<MyItem>`の項目は、サーバーのメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="a9435-206">アプリで項目のリストの拡張が制限されていない場合、サーバーがメモリ不足になる危険性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="a9435-207">メモリ不足の場合、現在のセッションは終了 (クラッシュ) し、そのサーバー インスタンス内のすべての同時セッションがメモリ不足の例外を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a9435-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="a9435-208">このシナリオが発生しないようにするには、同時ユーザーに項目制限を課すデータ構造を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="a9435-209">描画にページング スキームが使用されていない場合、サーバーは UI に表示されないオブジェクトに追加のメモリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="a9435-210">項目数に制限がない場合、メモリ要求によって使用可能なサーバー メモリが使い果たされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="a9435-211">このシナリオを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="a9435-212">表示時にページ分割されたリストを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="a9435-213">最初の 100 ~ 1,000 個のアイテムのみを表示し、表示されたアイテム以外のアイテムを検索するために検索条件を入力する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="a9435-214">より高度なレンダリング シナリオを実現するには、*仮想化*をサポートするリストまたはグリッドを実装します。</span><span class="sxs-lookup"><span data-stu-id="a9435-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="a9435-215">仮想化を使用すると、リストは現在ユーザーに表示されている項目のサブセットのみをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a9435-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="a9435-216">ユーザーが UI のスクロールバーを操作すると、コンポーネントは表示に必要な項目のみをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a9435-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="a9435-217">表示に現在必要のない項目はセカンダリ・ストレージに保持できるため、理想的なアプローチです。</span><span class="sxs-lookup"><span data-stu-id="a9435-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="a9435-218">表示されていない項目はメモリに保持することもできますが、これはあまり理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="a9435-219">Blazor Server アプリは、WPF、Windows フォーム、または Blazor WebAssembly などのステートフル アプリ用の他の UI フレームワークと同様のプログラミング モデルを提供します。</span><span class="sxs-lookup"><span data-stu-id="a9435-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="a9435-220">主な違いは、いくつかの UI フレームワークでは、アプリによって消費されるメモリがクライアントに属し、その個々のクライアントにのみ影響を与える点です。</span><span class="sxs-lookup"><span data-stu-id="a9435-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="a9435-221">たとえば、Blazor WebAssembly アプリはクライアント上で完全に実行され、クライアントメモリリソースのみを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="a9435-222">Blazor Server シナリオでは、アプリによって消費されるメモリはサーバーに属し、サーバー インスタンス上のクライアント間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="a9435-223">サーバー側のメモリ要求は、すべての Blazor Server アプリに対する考慮事項です。</span><span class="sxs-lookup"><span data-stu-id="a9435-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="a9435-224">ただし、ほとんどの Web アプリはステートレスであり、要求の処理中に使用されるメモリは、応答が返されたときに解放されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="a9435-225">一般的な推奨事項として、クライアント接続を保持する他のサーバー側アプリのように、クライアントが非バインドメモリの量を割り当てることを許可しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="a9435-226">Blazor Server アプリケーションで消費されるメモリは、1 つの要求よりも長い時間持続します。</span><span class="sxs-lookup"><span data-stu-id="a9435-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="a9435-227">開発中は、プロファイラーを使用するか、トレースをキャプチャしてクライアントのメモリ要求を評価できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="a9435-228">プロファイラーまたはトレースは、特定のクライアントに割り当てられたメモリをキャプチャしません。</span><span class="sxs-lookup"><span data-stu-id="a9435-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="a9435-229">開発中に特定のクライアントのメモリ使用量をキャプチャするには、ダンプをキャプチャし、ユーザーの回線にルートされているすべてのオブジェクトのメモリ要求を調べます。</span><span class="sxs-lookup"><span data-stu-id="a9435-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="a9435-230">クライアント接続</span><span class="sxs-lookup"><span data-stu-id="a9435-230">Client connections</span></span>

<span data-ttu-id="a9435-231">接続の枯渇は、1 つ以上のクライアントがサーバーへの同時接続を開きすぎて、他のクライアントが新しい接続を確立できない場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="a9435-232">Blazor クライアントは、セッションごとに 1 つの接続を確立し、ブラウザーウィンドウが開いている間は接続を開いたままにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="a9435-233">すべての接続を維持するサーバー上の要求は、Blazor アプリに固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="a9435-234">接続の永続的な性質と Blazor Server アプリのステートフルな性質を考えると、接続の枯渇は、アプリの可用性に対するリスクが大きくなります。</span><span class="sxs-lookup"><span data-stu-id="a9435-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="a9435-235">既定では、Blazor Server アプリのユーザーごとの接続数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="a9435-236">アプリで接続制限が必要な場合は、次の方法を 1 つ以上実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="a9435-237">認証を要求する場合は、承認されていないユーザーがアプリに接続する機能が自然に制限されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="a9435-238">このシナリオを有効にするには、ユーザーが新しいユーザーを必要に応じプロビジョニングできないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="a9435-239">ユーザーごとの接続数を制限します。</span><span class="sxs-lookup"><span data-stu-id="a9435-239">Limit the number of connections per user.</span></span> <span data-ttu-id="a9435-240">接続の制限は、次の方法で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="a9435-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="a9435-241">正規ユーザーがアプリにアクセスできるように (たとえば、クライアントの IP アドレスに基づいて接続制限が確立された場合) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="a9435-242">アプリ レベルで:</span><span class="sxs-lookup"><span data-stu-id="a9435-242">At the app level:</span></span>
    * <span data-ttu-id="a9435-243">エンドポイント ルーティングの拡張性。</span><span class="sxs-lookup"><span data-stu-id="a9435-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="a9435-244">アプリに接続し、ユーザーごとのアクティブなセッションを追跡するために認証を要求します。</span><span class="sxs-lookup"><span data-stu-id="a9435-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="a9435-245">制限に達した場合に新しいセッションを拒否します。</span><span class="sxs-lookup"><span data-stu-id="a9435-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="a9435-246">プロキシを使用してアプリへの WebSocket のプロキシ接続 (クライアントからアプリへの接続を多重化する[Azure SignalR サービス](/azure/azure-signalr/signalr-overview)など)。</span><span class="sxs-lookup"><span data-stu-id="a9435-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="a9435-247">これにより、単一のクライアントが確立できるよりも大きな接続容量をアプリに提供し、クライアントがサーバーへの接続を使い果たすことを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="a9435-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="a9435-248">サーバー レベル: アプリの前でプロキシ/ゲートウェイを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="a9435-249">たとえば[、Azure Front Door](/azure/frontdoor/front-door-overview)を使用すると、アプリへの Web トラフィックのグローバル ルーティングを定義、管理、および監視できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="a9435-250">サービス拒否 (DoS) 攻撃</span><span class="sxs-lookup"><span data-stu-id="a9435-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="a9435-251">サービス拒否 (DoS) 攻撃には、クライアントが原因でサーバーが 1 つ以上のリソースを使い果たしてアプリを利用不能にします。</span><span class="sxs-lookup"><span data-stu-id="a9435-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="a9435-252">Blazor Server アプリには、いくつかの既定の制限が含まれ、DoS 攻撃から保護するために、他のASP.NETコアと SignalR の制限に依存しています。</span><span class="sxs-lookup"><span data-stu-id="a9435-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="a9435-253">ブレイザサーバーアプリの制限</span><span class="sxs-lookup"><span data-stu-id="a9435-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="a9435-254">説明</span><span class="sxs-lookup"><span data-stu-id="a9435-254">Description</span></span> | <span data-ttu-id="a9435-255">Default</span><span class="sxs-lookup"><span data-stu-id="a9435-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="a9435-256">特定のサーバーが一度にメモリ内に保持する切断回線の最大数。</span><span class="sxs-lookup"><span data-stu-id="a9435-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="a9435-257">100</span><span class="sxs-lookup"><span data-stu-id="a9435-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="a9435-258">切断された回線が取り壊されるまでメモリ内に保持される最大時間。</span><span class="sxs-lookup"><span data-stu-id="a9435-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="a9435-259">3 分</span><span class="sxs-lookup"><span data-stu-id="a9435-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="a9435-260">非同期 JavaScript 関数呼び出しがタイムアウトになるまでのサーバーの最大待機時間。</span><span class="sxs-lookup"><span data-stu-id="a9435-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="a9435-261">1 分</span><span class="sxs-lookup"><span data-stu-id="a9435-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="a9435-262">サーバーが一定時間に回線あたりのメモリに保持し、堅牢な再接続をサポートするために、応答なしのレンダリング バッチの最大数。</span><span class="sxs-lookup"><span data-stu-id="a9435-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="a9435-263">制限に達すると、サーバーは、1 つ以上のバッチがクライアントによって確認されるまで、新しいレンダリング バッチの生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="a9435-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="a9435-264">10</span><span class="sxs-lookup"><span data-stu-id="a9435-264">10</span></span> |


| <span data-ttu-id="a9435-265">シグナルとASP.NETコアの制限</span><span class="sxs-lookup"><span data-stu-id="a9435-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="a9435-266">説明</span><span class="sxs-lookup"><span data-stu-id="a9435-266">Description</span></span> | <span data-ttu-id="a9435-267">Default</span><span class="sxs-lookup"><span data-stu-id="a9435-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="a9435-268">個々のメッセージのメッセージ サイズ。</span><span class="sxs-lookup"><span data-stu-id="a9435-268">Message size for an individual message.</span></span> | <span data-ttu-id="a9435-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="a9435-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="a9435-270">ブラウザ(クライアント)との対話</span><span class="sxs-lookup"><span data-stu-id="a9435-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="a9435-271">クライアントは、JS 相互運用イベントのディスパッチとレンダリング完了を通じてサーバーと対話します。</span><span class="sxs-lookup"><span data-stu-id="a9435-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="a9435-272">JS 相互運用通信は、JavaScript と .NET の両方の方法で行われます。</span><span class="sxs-lookup"><span data-stu-id="a9435-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="a9435-273">ブラウザイベントは、クライアントからサーバに非同期的に送出されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="a9435-274">サーバーは、必要に応じて UI の再レンダリングを非同期的に応答します。</span><span class="sxs-lookup"><span data-stu-id="a9435-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="a9435-275">NET から呼び出された Java スクリプト関数</span><span class="sxs-lookup"><span data-stu-id="a9435-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="a9435-276">NET メソッドから JavaScript への呼び出しの場合:</span><span class="sxs-lookup"><span data-stu-id="a9435-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="a9435-277">すべての呼び出しには、失敗した後に構成可能な<xref:System.OperationCanceledException>タイムアウトがあり、呼び出し元に a が返されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="a9435-278">呼び出し ( )`CircuitOptions.JSInteropDefaultCallTimeout`のデフォルトのタイムアウトは 1 分です。</span><span class="sxs-lookup"><span data-stu-id="a9435-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="a9435-279">この制限を構成するには、「」を参照してください<xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>。</span><span class="sxs-lookup"><span data-stu-id="a9435-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="a9435-280">キャンセル トークンを提供して、呼び出しごとにキャンセルを制御できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="a9435-281">キャンセル トークンが提供されている場合は、可能な場合は既定の呼び出しタイムアウトに依存し、クライアントへの任意の呼び出しを時間に制限します。</span><span class="sxs-lookup"><span data-stu-id="a9435-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="a9435-282">JavaScript 呼び出しの結果は信頼できません。</span><span class="sxs-lookup"><span data-stu-id="a9435-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="a9435-283">ブラウザーBlazorで実行されているアプリ クライアントは、呼び出す JavaScript 関数を検索します。</span><span class="sxs-lookup"><span data-stu-id="a9435-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="a9435-284">関数が呼び出され、結果またはエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="a9435-285">悪意のあるクライアントは、次の処理を試みる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="a9435-286">JavaScript 関数からエラーを返すことで、アプリで問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="a9435-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="a9435-287">JavaScript 関数から予期しない結果を返すことで、サーバー上で意図しない動作を発生させます。</span><span class="sxs-lookup"><span data-stu-id="a9435-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="a9435-288">上記のシナリオに対する防御策として、次の対策を講じます。</span><span class="sxs-lookup"><span data-stu-id="a9435-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="a9435-289">呼び出し中に発生する可能性のあるエラーを考慮するために[、try-catch](/dotnet/csharp/language-reference/keywords/try-catch)ステートメント内で JS 相互運用呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="a9435-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="a9435-290">詳細については、「<xref:blazor/handle-errors#javascript-interop>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="a9435-291">何らかのアクションを実行する前に、エラー メッセージを含む JS 相互運用の呼び出しから返されるデータを検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="a9435-292">ブラウザから呼び出された .NET メソッド</span><span class="sxs-lookup"><span data-stu-id="a9435-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="a9435-293">JavaScript から .NET メソッドへの呼び出しを信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="a9435-294">.NET メソッドが JavaScript に公開されている場合は、.NET メソッドがどのように呼び出されるのかを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="a9435-295">JavaScript に公開されている .NET メソッドは、アプリのパブリック エンドポイントと同じように扱います。</span><span class="sxs-lookup"><span data-stu-id="a9435-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="a9435-296">入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-296">Validate input.</span></span>
    * <span data-ttu-id="a9435-297">値が期待される範囲内にあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a9435-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="a9435-298">ユーザーが要求したアクションを実行する権限を持っていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a9435-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="a9435-299">.NET メソッド呼び出しの一部として、リソースの過剰な量を割り当てないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="a9435-300">たとえば、CPU とメモリの使用に関するチェックや制限を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="a9435-301">静的メソッドとインスタンス メソッドは JavaScript クライアントに公開できることを考慮に入れます。</span><span class="sxs-lookup"><span data-stu-id="a9435-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="a9435-302">設計で適切な制約を使用して状態を共有する必要がある場合を除き、セッション間で状態を共有しないようにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="a9435-303">依存関係挿入 (DI)`DotNetReference`によって作成されたオブジェクトを通じて公開されるメソッドの場合、オブジェクトはスコープ指定されたオブジェクトとして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="a9435-304">これは、サーバー アプリケーションが使用するすべてのBlazorDI サービスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="a9435-305">静的メソッドの場合、アプリがサーバー インスタンス上のすべてのユーザー間で明示的に状態を設計別で共有していない限り、クライアントにスコープを設定できない状態を確立しないようにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="a9435-306">パラメーター内のユーザー指定のデータを JavaScript 呼び出しに渡さないようにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="a9435-307">パラメーターでデータを渡す必要がある場合は、[クロスサイト スクリプティング (XSS)](#cross-site-scripting-xss)の脆弱性を導入せずに、JavaScript コードがデータの受け渡しを処理するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="a9435-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="a9435-308">たとえば、要素のプロパティを設定して、ユーザーが指定したデータをドキュメント オブジェクト モデル (DOM) に`innerHTML`書き込まないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="a9435-309">コンテンツ[セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval`を使用して、無効にするなどの安全でない JavaScript プリミティブを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="a9435-310">フレームワークのディスパッチの実装の上に .NET 呼び出しのカスタム ディスパッチを実装しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="a9435-311">.NET メソッドをブラウザに公開することは高度なシナリオであり、一般的Blazorな開発には推奨されません。</span><span class="sxs-lookup"><span data-stu-id="a9435-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="a9435-312">イベント</span><span class="sxs-lookup"><span data-stu-id="a9435-312">Events</span></span>

<span data-ttu-id="a9435-313">イベントは、サーバー アプリケーションへのBlazorエントリ ポイントを提供します。</span><span class="sxs-lookup"><span data-stu-id="a9435-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="a9435-314">Web アプリのエンドポイントを保護する場合と同じ規則が、ServerBlazorアプリのイベント処理にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="a9435-315">悪意のあるクライアントは、イベントのペイロードとして送信するデータを送信できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="a9435-316">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a9435-316">For example:</span></span>

* <span data-ttu-id="a9435-317">の変更イベント`<select>`は、アプリがクライアントに提示したオプションの範囲内にない値を送信できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="a9435-318">クライアント`<input>`側の検証をバイパスして、任意のテキスト データをサーバーに送信できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="a9435-319">アプリが処理するイベントのデータを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="a9435-320">フレームワークBlazor[フォーム コンポーネント](xref:blazor/forms-validation)は、基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="a9435-321">アプリでカスタム フォーム コンポーネントを使用する場合は、イベント データを適切に検証するカスタム コードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="a9435-322">サーバー イベントは非同期であるため、アプリが新しいレンダリングを生成して反応する時間がなくなる前に、複数のイベントをサーバーにディスパッチできます。</span><span class="sxs-lookup"><span data-stu-id="a9435-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="a9435-323">これには、考慮すべきセキュリティ上の影響があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-323">This has some security implications to consider.</span></span> <span data-ttu-id="a9435-324">アプリでのクライアント アクションの制限は、イベント ハンドラー内で実行する必要があり、現在レンダリングされているビューステートに依存しません。</span><span class="sxs-lookup"><span data-stu-id="a9435-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="a9435-325">ユーザーが最大 3 回のカウンタをインクリメントできるようにするカウンター コンポーネントを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="a9435-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="a9435-326">カウンタをインクリメントするボタンは、条件に応じて`count`、 の値に基づいています。</span><span class="sxs-lookup"><span data-stu-id="a9435-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="a9435-327">クライアントは、フレームワークがこのコンポーネントの新しいレンダリングを生成する前に、1 つ以上のインクリメント イベントをディスパッチできます。</span><span class="sxs-lookup"><span data-stu-id="a9435-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="a9435-328">その結果、ボタンが`count`UI によって十分に迅速に削除されないため、ユーザーによって*3 回以上*インクリメントできます。</span><span class="sxs-lookup"><span data-stu-id="a9435-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="a9435-329">3 つの`count`増分の制限を達成する正しい方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="a9435-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="a9435-330">ハンドラー内に`if (count < 3) { ... }`チェックを追加すると、インクリメント`count`の決定は、現在のアプリの状態に基づいて決定されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="a9435-331">この決定は、前の例のように UI の状態に基づいていません。</span><span class="sxs-lookup"><span data-stu-id="a9435-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="a9435-332">複数のディスパッチに対する防御</span><span class="sxs-lookup"><span data-stu-id="a9435-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="a9435-333">イベント コールバックが、外部サービスやデータベースからのデータのフェッチなど、実行時間の長い操作を非同期に呼び出す場合は、ガードを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="a9435-334">ガードは、操作が視覚的なフィードバックで進行中の間に、ユーザーが複数の操作をキューに入れないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="a9435-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="a9435-335">次のコンポーネント コード`isLoading`は`true`、`GetForecastAsync`サーバーからデータを取得する場合に設定します。</span><span class="sxs-lookup"><span data-stu-id="a9435-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="a9435-336">の`isLoading`間`true`は、ボタンは UI で無効になります。</span><span class="sxs-lookup"><span data-stu-id="a9435-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="a9435-337">前の例で示したガード パターンは、バックグラウンド操作がパターンと共に非同期に`async`-`await`実行される場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="a9435-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="a9435-338">早期にキャンセルし、廃棄後の使用を避ける</span><span class="sxs-lookup"><span data-stu-id="a9435-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="a9435-339">[「複数のディスパッチに対するガード](#guard-against-multiple-dispatches)」セクションで説明されているようにガードを使用する以外に、<xref:System.Threading.CancellationToken>を使用して、コンポーネントが破棄されるときに長時間実行される操作を取り消すことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="a9435-340">この方法には、コンポーネントでの*廃棄後の使用*を回避するという利点があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="a9435-341">大量のデータを生成するイベントを回避する</span><span class="sxs-lookup"><span data-stu-id="a9435-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="a9435-342">や`oninput``onscroll`などの DOM イベントの中には、大量のデータを生成するものもあります。</span><span class="sxs-lookup"><span data-stu-id="a9435-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="a9435-343">これらのイベントは、サーバーBlazorアプリで使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="a9435-344">追加のセキュリティ ガイダンス</span><span class="sxs-lookup"><span data-stu-id="a9435-344">Additional security guidance</span></span>

<span data-ttu-id="a9435-345">core アプリ ASP.NETのセキュリティ保護に関Blazorするガイダンスは、Server アプリに適用され、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="a9435-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="a9435-346">ログ記録と機密データ</span><span class="sxs-lookup"><span data-stu-id="a9435-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="a9435-347">HTTPS を使用して転送中の情報を保護する</span><span class="sxs-lookup"><span data-stu-id="a9435-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="a9435-348">[クロスサイト スクリプティング (XSS)](#cross-site-scripting-xss)</span><span class="sxs-lookup"><span data-stu-id="a9435-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="a9435-349">クロスオリジン保護</span><span class="sxs-lookup"><span data-stu-id="a9435-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="a9435-350">クリックジャッキング</span><span class="sxs-lookup"><span data-stu-id="a9435-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="a9435-351">リダイレクトを開く</span><span class="sxs-lookup"><span data-stu-id="a9435-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="a9435-352">ログ記録と機密データ</span><span class="sxs-lookup"><span data-stu-id="a9435-352">Logging and sensitive data</span></span>

<span data-ttu-id="a9435-353">クライアントとサーバー間の JS 相互運用機能の相互作用は、インスタンスを持つ<xref:Microsoft.Extensions.Logging.ILogger>サーバーのログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="a9435-354">実際のイベントや JS 相互運用機能の入力や出力などの機密情報のログ記録を回避できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="a9435-355">サーバーでエラーが発生すると、フレームワークはクライアントに通知し、セッションを削除します。</span><span class="sxs-lookup"><span data-stu-id="a9435-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="a9435-356">既定では、クライアントは、ブラウザーの開発者ツールで表示できる一般的なエラー メッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a9435-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="a9435-357">クライアント側エラーにはコールスタックは含まれておらず、エラーの原因の詳細は提供されませんが、サーバーログにはこのような情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a9435-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="a9435-358">開発の目的で、詳細なエラーを有効にすることで、重要なエラー情報をクライアントに提供できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="a9435-359">次の場合に詳細なエラーを有効にします。</span><span class="sxs-lookup"><span data-stu-id="a9435-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="a9435-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="a9435-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="a9435-361">`DetailedErrors`コンフィギュレーション キー。</span><span class="sxs-lookup"><span data-stu-id="a9435-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="a9435-362">たとえば、環境変数の`ASPNETCORE_DETAILEDERRORS`値を`true`に設定します。</span><span class="sxs-lookup"><span data-stu-id="a9435-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="a9435-363">インターネット上のクライアントにエラー情報を公開することは、常に避けるべきセキュリティリスクです。</span><span class="sxs-lookup"><span data-stu-id="a9435-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="a9435-364">HTTPS を使用して転送中の情報を保護する</span><span class="sxs-lookup"><span data-stu-id="a9435-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="a9435-365">サーバーはSignalR、クライアントとサーバー間の通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="a9435-366">サーバーは通常、ネゴシエSignalRートするトランスポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="a9435-367">サーバーは、サーバーとクライアントの間で送信されるデータの整合性と機密性を保証しません。</span><span class="sxs-lookup"><span data-stu-id="a9435-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="a9435-368">常に HTTPS を使用してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="a9435-369">クロスサイト スクリプティング (XSS)</span><span class="sxs-lookup"><span data-stu-id="a9435-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="a9435-370">クロスサイト スクリプティング (XSS) を使用すると、承認されていないユーザーがブラウザのコンテキストで任意のロジックを実行できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="a9435-371">侵害されたアプリは、クライアント上で任意のコードを実行する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="a9435-372">この脆弱性は、サーバーに対して悪意のある多数のアクションを実行する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="a9435-373">偽/無効イベントをサーバーにディスパッチします。</span><span class="sxs-lookup"><span data-stu-id="a9435-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="a9435-374">ディスパッチが失敗した/無効なレンダリングの完了。</span><span class="sxs-lookup"><span data-stu-id="a9435-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="a9435-375">レンダリングの完了をディスパッチしないようにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="a9435-376">相互運用機能の呼び出しを JavaScript から .NET にディスパッチします。</span><span class="sxs-lookup"><span data-stu-id="a9435-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="a9435-377">.NET から JavaScript への相互運用呼び出しの応答を変更します。</span><span class="sxs-lookup"><span data-stu-id="a9435-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="a9435-378">.NET を JS 相互運用結果にディスパッチしないようにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="a9435-379">ServerBlazorフレームワークでは、上記の脅威に対して保護するための手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="a9435-380">クライアントがレンダリング バッチを受信確認していない場合、新しい UI 更新の生成を停止します。</span><span class="sxs-lookup"><span data-stu-id="a9435-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="a9435-381">で`CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`構成されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="a9435-382">クライアントからの応答を受信せずに、1 分後に .NET から JavaScript への呼び出しをタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="a9435-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="a9435-383">で`CircuitOptions.JSInteropDefaultCallTimeout`構成されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="a9435-384">JS 相互運用中にブラウザから来るすべての入力に対して基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="a9435-385">.NET 参照は有効であり、.NET メソッドで予期される型です。</span><span class="sxs-lookup"><span data-stu-id="a9435-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="a9435-386">データの形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="a9435-387">メソッドの正しい引数の数がペイロードに存在します。</span><span class="sxs-lookup"><span data-stu-id="a9435-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="a9435-388">引数または結果は、メソッドを呼び出す前に正しく逆シリアル化できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="a9435-389">ディスパッチされたイベントからブラウザーから来るすべての入力で基本的な検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="a9435-390">イベントの種類が有効です。</span><span class="sxs-lookup"><span data-stu-id="a9435-390">The event has a valid type.</span></span>
  * <span data-ttu-id="a9435-391">イベントのデータは逆シリアル化できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="a9435-392">イベントに関連付けられたイベント ハンドラーがあります。</span><span class="sxs-lookup"><span data-stu-id="a9435-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="a9435-393">フレームワークが実装するセーフガードに加えて、アプリは、開発者が脅威から保護し、適切なアクションを実行するコード化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="a9435-394">イベントを処理する場合は、常にデータを検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="a9435-395">無効なデータを受け取った場合は、適切なアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="a9435-396">データを無視して戻ります。</span><span class="sxs-lookup"><span data-stu-id="a9435-396">Ignore the data and return.</span></span> <span data-ttu-id="a9435-397">これにより、アプリは要求の処理を続行できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="a9435-398">アプリが入力が非合法であり、正当なクライアントによって生成できなかった場合は、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="a9435-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="a9435-399">例外をスローすると、回線が引き裂かれ、セッションが終了します。</span><span class="sxs-lookup"><span data-stu-id="a9435-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="a9435-400">ログに含まれるレンダリング バッチの完了によって提供されるエラー メッセージを信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="a9435-401">エラーはクライアントによって提供され、クライアントが侵害される可能性があるため、一般的に信頼できません。</span><span class="sxs-lookup"><span data-stu-id="a9435-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="a9435-402">JavaScript メソッドと .NET メソッド間のどちらの方向にも、JS 相互運用呼び出しに関する入力を信頼しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="a9435-403">引数または結果が正しく逆シリアル化されている場合でも、引数と結果の内容が有効であることを検証するアプリは、します。</span><span class="sxs-lookup"><span data-stu-id="a9435-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="a9435-404">XSS の脆弱性が存在するには、レンダリングされたページにユーザー入力をアプリに組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="a9435-405">サーバー コンポーネントは *、.razor*ファイル内のマークアップが手続き型 C# ロジックに変換されるコンパイル時の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9435-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="a9435-406">実行時に、C# ロジックは、要素、テキスト、および子コンポーネントを記述する*レンダリング ツリー*を構築します。</span><span class="sxs-lookup"><span data-stu-id="a9435-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="a9435-407">これは、JavaScript 命令のシーケンスを介してブラウザーの DOM に適用されます (または、プリレンダリングの場合は HTML にシリアル化されます)。</span><span class="sxs-lookup"><span data-stu-id="a9435-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="a9435-408">通常の Razor 構文 (たとえば)`@someStringValue`を使用してレンダリングされたユーザー入力は、テキストを書き込むだけのコマンドを介して Razor 構文が DOM に追加されるため、XSS の脆弱性を公開しません。</span><span class="sxs-lookup"><span data-stu-id="a9435-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="a9435-409">値に HTML マークアップが含まれている場合でも、値は静的テキストとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="a9435-410">プリレンダリング時に出力される HTML エンコードは、コンテンツも静的テキストとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="a9435-411">スクリプト タグは許可されず、アプリのコンポーネント レンダー ツリーに含めるべきではありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="a9435-412">コンポーネントのマークアップにスクリプト タグが含まれている場合は、コンパイル エラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="a9435-413">コンポーネントの作成者は、Razor を使用せずに C# でコンポーネントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a9435-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="a9435-414">コンポーネントの作成者は、出力を出力する際に正しい API を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="a9435-415">たとえば、後者`builder.AddContent(0, someUserSuppliedString)`が XSS の脆弱性を引き起こす可能性があるとして、使用*し、使用しない*`builder.AddMarkupContent(0, someUserSuppliedString)`などです。</span><span class="sxs-lookup"><span data-stu-id="a9435-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="a9435-416">XSS 攻撃に対する保護の一環として、コンテンツ セキュリティ ポリシー [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)などの XSS の緩和策の実装を検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="a9435-417">詳細については、「<xref:security/cross-site-scripting>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="a9435-418">クロスオリジン保護</span><span class="sxs-lookup"><span data-stu-id="a9435-418">Cross-origin protection</span></span>

<span data-ttu-id="a9435-419">クロスオリジン攻撃には、別のオリジンのクライアントがサーバーに対してアクションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="a9435-420">悪意のあるアクションは、通常、GET要求またはフォームPOST(クロスサイトリクエストフォージェリ、CSRF)ですが、悪意のあるWebSocketを開くことも可能です。</span><span class="sxs-lookup"><span data-stu-id="a9435-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="a9435-421">サーバー アプリ[は、ハブ プロトコルを使用SignalRする他のアプリが提供するのと同じ保証を提供](xref:signalr/security)します。</span><span class="sxs-lookup"><span data-stu-id="a9435-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="a9435-422">サーバー アプリは、それを防ぐために追加の対策を講じなければ、クロス オリジンにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="a9435-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="a9435-423">クロスオリジンアクセスを無効にするには、CORS ミドルウェアをパイプラインに追加してエンドポイントの CORS を無効`DisableCorsAttribute`にし、Blazorエンドポイントメタデータに を追加するか、[クロスオリジンリソース共有を構成してSignalR](xref:signalr/security#cross-origin-resource-sharing)許可されたオリジンのセットを制限します。</span><span class="sxs-lookup"><span data-stu-id="a9435-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="a9435-424">CORS が有効になっている場合、CORS の構成によっては、アプリを保護するために追加の手順が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="a9435-425">CORS がグローバルに有効になっている場合は、呼び出しBlazor`hub.MapBlazorHub()`後にエンドポイント メタデータ`DisableCorsAttribute`にメタデータを追加することで、サーバー ハブの CORS を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a9435-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="a9435-426">詳細については、「<xref:security/anti-request-forgery>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="a9435-427">クリックジャッキング</span><span class="sxs-lookup"><span data-stu-id="a9435-427">Click-jacking</span></span>

<span data-ttu-id="a9435-428">クリックジャッキングでは、サイトを別のオリジン`<iframe>`のサイト内としてレンダリングし、ユーザーをだまして攻撃を受けているサイトでアクションを実行させることが必要です。</span><span class="sxs-lookup"><span data-stu-id="a9435-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="a9435-429">の内部でアプリがレンダリングされないようにするには、`<iframe>`コンテンツ[セキュリティ ポリシー (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `X-Frame-Options`とヘッダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="a9435-430">詳細については、 [MDN Web ドキュメント: X フレーム オプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="a9435-431">リダイレクトを開く</span><span class="sxs-lookup"><span data-stu-id="a9435-431">Open redirects</span></span>

<span data-ttu-id="a9435-432">サーバーBlazorアプリケーション セッションが開始されると、セッションの開始の一部として送信される URL の基本的な検証がサーバーによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="a9435-433">フレームワークは、回線を確立する前に、ベース URL が現在の URL の親であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a9435-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="a9435-434">フレームワークによって追加のチェックは実行されません。</span><span class="sxs-lookup"><span data-stu-id="a9435-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="a9435-435">ユーザーがクライアント上のリンクを選択すると、リンクの URL がサーバーに送信され、どのアクションを実行するかが決まります。</span><span class="sxs-lookup"><span data-stu-id="a9435-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="a9435-436">たとえば、アプリはクライアント側のナビゲーションを実行したり、新しい場所に移動するようにブラウザーに指示したりできます。</span><span class="sxs-lookup"><span data-stu-id="a9435-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="a9435-437">コンポーネントは、 の使用を通じて、プログラムによるナビゲーション`NavigationManager`要求をトリガすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a9435-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="a9435-438">このようなシナリオでは、アプリはクライアント側のナビゲーションを実行するか、新しい場所に移動するようにブラウザーに示す場合があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="a9435-439">コンポーネントは次の項目を必要とします。</span><span class="sxs-lookup"><span data-stu-id="a9435-439">Components must:</span></span>

* <span data-ttu-id="a9435-440">ナビゲーション呼び出しの引数の一部としてユーザー入力を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="a9435-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="a9435-441">引数を検証して、ターゲットがアプリで許可されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a9435-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="a9435-442">そうしないと、悪意のあるユーザーが強制的にブラウザを攻撃者が制御するサイトに移動する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a9435-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="a9435-443">このシナリオでは、攻撃者は、メソッドの呼び出しの一部として、ユーザー入力を`NavigationManager.Navigate`使用するアプリを騙します。</span><span class="sxs-lookup"><span data-stu-id="a9435-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="a9435-444">このアドバイスは、アプリの一部としてリンクをレンダリングする場合にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="a9435-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="a9435-445">可能であれば、相対リンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="a9435-445">If possible, use relative links.</span></span>
* <span data-ttu-id="a9435-446">絶対リンク先がページに含まれる前に有効であることを検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="a9435-447">詳細については、「<xref:security/preventing-open-redirects>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="a9435-448">認証と権限承認</span><span class="sxs-lookup"><span data-stu-id="a9435-448">Authentication and authorization</span></span>

<span data-ttu-id="a9435-449">認証と承認のガイダンスについては、を<xref:security/blazor/index>参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="a9435-450">セキュリティ チェックリスト</span><span class="sxs-lookup"><span data-stu-id="a9435-450">Security checklist</span></span>

<span data-ttu-id="a9435-451">次のセキュリティに関する考慮事項の一覧は、包括的ではありません。</span><span class="sxs-lookup"><span data-stu-id="a9435-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="a9435-452">イベントの引数を検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-452">Validate arguments from events.</span></span>
* <span data-ttu-id="a9435-453">JS 相互運用呼び出しからの入力と結果を検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="a9435-454">.NET から JS 相互運用呼び出しに対してユーザー入力を使用 (または事前に検証) することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="a9435-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="a9435-455">クライアントが非バインドメモリを割り当てないようにします。</span><span class="sxs-lookup"><span data-stu-id="a9435-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="a9435-456">コンポーネント内のデータ。</span><span class="sxs-lookup"><span data-stu-id="a9435-456">Data within the component.</span></span>
  * <span data-ttu-id="a9435-457">`DotNetObject`クライアントに返される参照。</span><span class="sxs-lookup"><span data-stu-id="a9435-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="a9435-458">複数のディスパッチに対するガード。</span><span class="sxs-lookup"><span data-stu-id="a9435-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="a9435-459">コンポーネントが破棄されるときに、長時間実行される操作をキャンセルします。</span><span class="sxs-lookup"><span data-stu-id="a9435-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="a9435-460">大量のデータを生成するイベントは避けてください。</span><span class="sxs-lookup"><span data-stu-id="a9435-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="a9435-461">ユーザー入力を呼び出しの一`NavigationManager.Navigate`部として使用することは避け、最初に許可された発信元のセットに対して URL のユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="a9435-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="a9435-462">UI の状態に基づいて承認の決定を行うのではなく、コンポーネントの状態からのみ行います。</span><span class="sxs-lookup"><span data-stu-id="a9435-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="a9435-463">コンテンツ[セキュリティ ポリシー (CSP) を](https://developer.mozilla.org/docs/Web/HTTP/CSP)使用して、XSS 攻撃から保護することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="a9435-464">クリックジャッキングから保護するために、CSP と[X フレーム オプション](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)の使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="a9435-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="a9435-465">CORS を有効にする場合は CORS 設定が適切であることをBlazor確認するか、アプリの CORS を明示的に無効にします。</span><span class="sxs-lookup"><span data-stu-id="a9435-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="a9435-466">Blazorアプリのサーバー側の制限が許容できないレベルのリスクを伴わずに許容可能なユーザー エクスペリエンスを提供することを確認するテスト。</span><span class="sxs-lookup"><span data-stu-id="a9435-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
