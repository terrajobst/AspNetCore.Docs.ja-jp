---
title: ASP.NET Core Blazor の認証と承認
author: guardrex
description: Blazor の認証と承認のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/21/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: f7ffb4c3d5a05cb916b4f00cdfaf5898634a1a6d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219026"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="461aa-103">ASP.NET Core Blazor の認証と承認</span><span class="sxs-lookup"><span data-stu-id="461aa-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="461aa-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="461aa-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="461aa-105">ASP.NET Core は、Blazor アプリのセキュリティの構成と管理をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="461aa-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="461aa-106">Blazor サーバー アプリと Blazor WebAssembly アプリのセキュリティ シナリオは異なります。</span><span class="sxs-lookup"><span data-stu-id="461aa-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="461aa-107">Blazor サーバー アプリはサーバー上で動作するため、承認チェックでは以下のことを判断できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="461aa-108">ユーザーに表示される UI オプション (たとえば、ユーザーが利用できるメニュー エントリ)。</span><span class="sxs-lookup"><span data-stu-id="461aa-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="461aa-109">アプリとコンポーネントの領域に対するアクセス規則。</span><span class="sxs-lookup"><span data-stu-id="461aa-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="461aa-110">Blazor WebAssembly アプリはクライアント上で動作します。</span><span class="sxs-lookup"><span data-stu-id="461aa-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="461aa-111">承認は、表示する UI オプションを決定するために "*のみ*" 使用されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="461aa-112">クライアント側のチェックはユーザーによって変更またはバイパスされる可能性があるため、Blazor WebAssembly アプリでは承認アクセス規則を適用できません。</span><span class="sxs-lookup"><span data-stu-id="461aa-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="461aa-113">[Razor Pages の承認規則](xref:security/authorization/razor-pages-authorization)は、ルーティング可能な Razor コンポーネントには適用されません。</span><span class="sxs-lookup"><span data-stu-id="461aa-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="461aa-114">ルーティング不可能な Razor コンポーネントが[ページに埋め込まれている](xref:blazor/integrate-components#render-components-from-a-page-or-view)場合、ページの承認規則は、Razor コンポーネントと、ページのコンテンツの残りの部分に間接的に影響します。</span><span class="sxs-lookup"><span data-stu-id="461aa-114">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

## <a name="authentication"></a><span data-ttu-id="461aa-115">認証</span><span class="sxs-lookup"><span data-stu-id="461aa-115">Authentication</span></span>

<span data-ttu-id="461aa-116">Blazor は、既存の ASP.NET Core 認証メカニズムを使用してユーザーの ID を証明します。</span><span class="sxs-lookup"><span data-stu-id="461aa-116">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="461aa-117">詳細なメカニズムは、Blazor アプリのホスティング方法、Blazor サーバーか Blazor WebAssembly かによって異なります。</span><span class="sxs-lookup"><span data-stu-id="461aa-117">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="461aa-118">Blazor サーバー認証</span><span class="sxs-lookup"><span data-stu-id="461aa-118">Blazor Server authentication</span></span>

<span data-ttu-id="461aa-119">Blazor サーバー アプリは、SignalR を使用して作成されたリアルタイム接続を介して動作します。</span><span class="sxs-lookup"><span data-stu-id="461aa-119">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="461aa-120">[SignalR ベースのアプリの認証](xref:signalr/authn-and-authz)は、接続が確立したときに処理されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-120">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="461aa-121">認証は、Cookie または他のベアラー トークンに基づいています。</span><span class="sxs-lookup"><span data-stu-id="461aa-121">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="461aa-122">プロジェクトの作成時に、Blazor サーバー プロジェクト テンプレートで認証を設定できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-122">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="461aa-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="461aa-123">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="461aa-124">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="461aa-124">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="461aa-125">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、**[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="461aa-125">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="461aa-126">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-126">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="461aa-127">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="461aa-127">**No Authentication**</span></span>
* <span data-ttu-id="461aa-128">**個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-128">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="461aa-129">ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="461aa-129">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="461aa-130">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="461aa-130">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="461aa-131">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="461aa-131">**Work or School Accounts**</span></span>
* <span data-ttu-id="461aa-132">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="461aa-132">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="461aa-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="461aa-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="461aa-134">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="461aa-134">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="461aa-135">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="461aa-135">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="461aa-136">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="461aa-136">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="461aa-137">`{AUTHENTICATION}` の値</span><span class="sxs-lookup"><span data-stu-id="461aa-137">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="461aa-138">認証なし</span><span class="sxs-lookup"><span data-stu-id="461aa-138">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="461aa-139">個人</span><span class="sxs-lookup"><span data-stu-id="461aa-139">Individual</span></span><br><span data-ttu-id="461aa-140">ASP.NET Core ID を使用するアプリに格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="461aa-140">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="461aa-141">個人</span><span class="sxs-lookup"><span data-stu-id="461aa-141">Individual</span></span><br><span data-ttu-id="461aa-142">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="461aa-142">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="461aa-143">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="461aa-143">Work or School Accounts</span></span><br><span data-ttu-id="461aa-144">単一のテナントに対する組織認証。</span><span class="sxs-lookup"><span data-stu-id="461aa-144">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="461aa-145">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="461aa-145">Work or School Accounts</span></span><br><span data-ttu-id="461aa-146">複数のテナントに対する組織認証です。</span><span class="sxs-lookup"><span data-stu-id="461aa-146">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="461aa-147">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="461aa-147">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="461aa-148">このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-148">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="461aa-149">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-149">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="461aa-150"> WebAssembly 認証</span><span class="sxs-lookup"><span data-stu-id="461aa-150"> WebAssembly authentication</span></span>

<span data-ttu-id="461aa-151">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、認証チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="461aa-151">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="461aa-152">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="461aa-152">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="461aa-153">アプリのプロジェクト ファイルに、[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="461aa-153">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="461aa-154">Blazor WebAssembly アプリ用のカスタム `AuthenticationStateProvider` サービスの実装については、以降のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="461aa-154">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="461aa-155">AuthenticationStateProvider サービス</span><span class="sxs-lookup"><span data-stu-id="461aa-155">AuthenticationStateProvider service</span></span>

Blazor<span data-ttu-id="461aa-156"> サーバー アプリには、ASP.NET Core の `HttpContext.User` から認証状態データを取得する組み込みの `AuthenticationStateProvider` サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="461aa-156"> Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="461aa-157">このようにして、認証状態は既存の ASP.NET Core サーバー側認証メカニズムと統合されています。</span><span class="sxs-lookup"><span data-stu-id="461aa-157">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="461aa-158">`AuthenticationStateProvider` は、認証状態を取得するために `AuthorizeView` コンポーネントと `CascadingAuthenticationState` コンポーネントによって使用される基となるサービスです。</span><span class="sxs-lookup"><span data-stu-id="461aa-158">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="461aa-159">通常、`AuthenticationStateProvider` は直接使用しません。</span><span class="sxs-lookup"><span data-stu-id="461aa-159">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="461aa-160">この記事で後述する [AuthorizeView コンポーネント](#authorizeview-component)または [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-160">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="461aa-161">`AuthenticationStateProvider` を直接使用することの主な欠点は、基となる認証状態データが変更されてもコンポーネントに自動的に通知されないことです。</span><span class="sxs-lookup"><span data-stu-id="461aa-161">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="461aa-162">次の例に示すように、`AuthenticationStateProvider` サービスから現在のユーザーの <xref:System.Security.Claims.ClaimsPrincipal> データを提供できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-162">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="461aa-163">`user.Identity.IsAuthenticated` が `true` である場合、ユーザーが <xref:System.Security.Claims.ClaimsPrincipal> であるため、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-163">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="461aa-164">依存関係の注入 (DI) とサービスの詳細については、<xref:blazor/dependency-injection> と <xref:fundamentals/dependency-injection> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-164">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="461aa-165">カスタム AuthenticationStateProvider の実装</span><span class="sxs-lookup"><span data-stu-id="461aa-165">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="461aa-166">Blazor WebAssembly アプリを構築している場合、またはアプリの仕様でカスタム プロバイダーが必要な場合は、プロバイダーを実装して `GetAuthenticationStateAsync` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="461aa-166">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
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
}
```

<span data-ttu-id="461aa-167">Blazor WebAssembly アプリでは、`CustomAuthStateProvider` サービスは *Program.cs* の `Main` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="461aa-167">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.Extensions.DependencyInjection;
using BlazorSample.Services;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddScoped<AuthenticationStateProvider, 
            CustomAuthStateProvider>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="461aa-168">`CustomAuthStateProvider` を使用すると、すべてのユーザーはユーザー名 `mrfibuli` で認証されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-168">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="461aa-169">認証状態をカスケード パラメーターとして公開する</span><span class="sxs-lookup"><span data-stu-id="461aa-169">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="461aa-170">ユーザーによってトリガーされたアクションを実行する場合など、認証状態データが手続き型ロジックに必要な場合は、型 `Task<AuthenticationState>` のカスケード パラメーターを定義して認証状態データを取得します。</span><span class="sxs-lookup"><span data-stu-id="461aa-170">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="461aa-171">Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Components.Authorization` 名前空間 (`@using Microsoft.AspNetCore.Components.Authorization`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="461aa-171">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="461aa-172">`user.Identity.IsAuthenticated` が `true` である場合、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-172">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="461aa-173">*App.razor* ファイル内の `AuthorizeRouteView` および `CascadingAuthenticationState` コンポーネントを使用して、`Task<AuthenticationState>` カスケード パラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="461aa-173">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the *App.razor* file:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization

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

<span data-ttu-id="461aa-174">オプションと承認のためのサービスを `Program.Main` に追加します。</span><span class="sxs-lookup"><span data-stu-id="461aa-174">Add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

## <a name="authorization"></a><span data-ttu-id="461aa-175">承認</span><span class="sxs-lookup"><span data-stu-id="461aa-175">Authorization</span></span>

<span data-ttu-id="461aa-176">ユーザーが認証されると、ユーザーが実行できる操作を制御する "*承認*" 規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-176">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="461aa-177">通常、アクセスは以下の条件に基づいて許可または拒否されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-177">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="461aa-178">ユーザーが認証されている (サインインしている)。</span><span class="sxs-lookup"><span data-stu-id="461aa-178">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="461aa-179">ユーザーに "*ロール*" が割り当てられている。</span><span class="sxs-lookup"><span data-stu-id="461aa-179">A user is in a *role*.</span></span>
* <span data-ttu-id="461aa-180">ユーザーに "*要求*" がある。</span><span class="sxs-lookup"><span data-stu-id="461aa-180">A user has a *claim*.</span></span>
* <span data-ttu-id="461aa-181">"*ポリシー*" が満たされている。</span><span class="sxs-lookup"><span data-stu-id="461aa-181">A *policy* is satisfied.</span></span>

<span data-ttu-id="461aa-182">これらの各概念は、ASP.NET Core MVC または Razor Pages アプリと同じです。</span><span class="sxs-lookup"><span data-stu-id="461aa-182">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="461aa-183">ASP.NET Core のセキュリティの詳細については、[ASP.NET Core のセキュリティと ID](xref:security/index) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-183">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="461aa-184">AuthorizeView コンポーネント</span><span class="sxs-lookup"><span data-stu-id="461aa-184">AuthorizeView component</span></span>

<span data-ttu-id="461aa-185">`AuthorizeView` コンポーネントでは、ユーザーに表示を許可するかどうかに応じて UI が選択的に表示されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-185">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="461aa-186">このアプローチは、ユーザーに対してデータを "*表示する*" だけで済み、手続き型ロジックでユーザーの ID を使用する必要がない場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="461aa-186">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="461aa-187">このコンポーネントでは、型 `AuthenticationState` の `context` 変数が公開されており、これを使用して、サインインしたユーザーに関する情報にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="461aa-187">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="461aa-188">ユーザーが認証されていない場合は、表示用に異なるコンテンツを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="461aa-188">You can also supply different content for display if the user isn't authenticated:</span></span>

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

<span data-ttu-id="461aa-189">`<Authorized>` および `<NotAuthorized>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="461aa-189">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="461aa-190">UI オプションまたはアクセスを制御するロールやポリシーなどの承認条件については、「[承認](#authorization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-190">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="461aa-191">承認条件が指定されていない場合、`AuthorizeView` では既定のポリシーが使用され、以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="461aa-191">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="461aa-192">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="461aa-192">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="461aa-193">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="461aa-193">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="461aa-194">ロールベースとリソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="461aa-194">Role-based and policy-based authorization</span></span>

<span data-ttu-id="461aa-195">`AuthorizeView` コンポーネントは、"*ロールベース*" または "*ポリシーベース*" の承認をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="461aa-195">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="461aa-196">ロールベースの承認の場合は、`Roles` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-196">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="461aa-197">詳細については、「<xref:security/authorization/roles>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-197">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="461aa-198">ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-198">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="461aa-199">要求ベースの承認は、ポリシーベースの承認の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="461aa-199">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="461aa-200">たとえば、ユーザーが特定の要求持つことを必須にするポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-200">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="461aa-201">詳細については、「<xref:security/authorization/policies>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-201">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="461aa-202">これらの API は、Blazor サーバー アプリまたは Blazor WebAssembly アプリのどちらでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-202">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="461aa-203">`Roles` も `Policy` も指定されていない場合、`AuthorizeView` には既定のポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-203">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="461aa-204">非同期認証中に表示されるコンテンツ</span><span class="sxs-lookup"><span data-stu-id="461aa-204">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="461aa-205"> では、認証状態を "*非同期的に*" 決定することができます。</span><span class="sxs-lookup"><span data-stu-id="461aa-205"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="461aa-206">このアプローチの主なシナリオは、認証のために外部エンドポイントに要求を送信する Blazor WebAssembly アプリです。</span><span class="sxs-lookup"><span data-stu-id="461aa-206">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="461aa-207">認証が進行中の間、`AuthorizeView` には既定でコンテンツが表示されません。</span><span class="sxs-lookup"><span data-stu-id="461aa-207">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="461aa-208">認証が行われている間にコンテンツを表示するには、`<Authorizing>` 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-208">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

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

<span data-ttu-id="461aa-209">通常、このアプローチは Blazor サーバー アプリには適用されません。</span><span class="sxs-lookup"><span data-stu-id="461aa-209">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="461aa-210"> サーバー アプリでは、状態が確立されるとすぐに認証状態が認識されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-210"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="461aa-211">`Authorizing` コンテンツは Blazor サーバー アプリの `AuthorizeView` コンポーネントで提供できますが、コンテンツは表示されません。</span><span class="sxs-lookup"><span data-stu-id="461aa-211">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="461aa-212">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="461aa-212">[Authorize] attribute</span></span>

<span data-ttu-id="461aa-213">`[Authorize]` 属性は Razor コンポーネント内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-213">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="461aa-214">Blazor WebAssembly アプリ コンポーネントで、このセクション内の例に `Microsoft.AspNetCore.Authorization` 名前空間 (`@using Microsoft.AspNetCore.Authorization`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="461aa-214">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="461aa-215">Blazor ルーター経由で到達した `@page` コンポーネントにのみ `[Authorize]` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-215">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="461aa-216">承認はルーティングの一面としてのみ実行され、ページ内にレンダリングされた子コンポーネントに対しては実行され "*ません*"。</span><span class="sxs-lookup"><span data-stu-id="461aa-216">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="461aa-217">ページ内の特定部分の表示を承認するには、代わりに `AuthorizeView` を使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-217">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="461aa-218">`[Authorize]` 属性は、ロールベースまたはポリシーベースの承認もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="461aa-218">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="461aa-219">ロールベースの承認の場合は、`Roles` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-219">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="461aa-220">ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="461aa-220">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="461aa-221">`Roles` も `Policy` も指定されていない場合、`[Authorize]` には既定のポリシーが使用されます。これは既定で以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="461aa-221">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="461aa-222">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="461aa-222">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="461aa-223">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="461aa-223">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="461aa-224">Router コンポーネントを使用して承認されていないコンテンツをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="461aa-224">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="461aa-225">`Router` コンポーネントを `AuthorizeRouteView` コンポーネントとともに使用すると、以下の場合にアプリがカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="461aa-225">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="461aa-226">コンテンツが見つからない。</span><span class="sxs-lookup"><span data-stu-id="461aa-226">Content isn't found.</span></span>
* <span data-ttu-id="461aa-227">ユーザーはコンポーネントに適用されている `[Authorize]` 条件に失敗します。</span><span class="sxs-lookup"><span data-stu-id="461aa-227">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="461aa-228">`[Authorize]` 属性については、「[`[Authorize]` 属性](#authorize-attribute)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-228">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="461aa-229">非同期認証が実行中です。</span><span class="sxs-lookup"><span data-stu-id="461aa-229">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="461aa-230">既定の Blazor サーバー プロジェクト テンプレートでは、*App.razor* ファイルがカスタム コンテンツの設定方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="461aa-230">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

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

<span data-ttu-id="461aa-231">`<NotFound>`、`<NotAuthorized>`、および `<Authorizing>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="461aa-231">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="461aa-232">`<NotAuthorized>` 要素が指定されていない場合、`AuthorizeRouteView` には次のフォールバック メッセージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-232">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="461aa-233">認証状態の変更に関する通知</span><span class="sxs-lookup"><span data-stu-id="461aa-233">Notification about authentication state changes</span></span>

<span data-ttu-id="461aa-234">基となる認証状態データが変わったとアプリが判断した場合 (たとえば、ユーザーがサインアウトした、または別のユーザーがロールを変更したなど)、カスタムの `AuthenticationStateProvider` はオプションで `AuthenticationStateProvider` 基底クラスのメソッド `NotifyAuthenticationStateChanged` を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="461aa-234">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="461aa-235">これにより、新しいデータを使用して再表示するように、認証状態データ (`AuthorizeView` など) がコンシューマーに通知されます。</span><span class="sxs-lookup"><span data-stu-id="461aa-235">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="461aa-236">手続き型ロジック</span><span class="sxs-lookup"><span data-stu-id="461aa-236">Procedural logic</span></span>

<span data-ttu-id="461aa-237">手続き型ロジックの一部としてアプリで承認規則をチェックする必要がある場合、型 `Task<AuthenticationState>` のカスケード パラメーターを使用してユーザーの <xref:System.Security.Claims.ClaimsPrincipal> を取得します。</span><span class="sxs-lookup"><span data-stu-id="461aa-237">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="461aa-238">ポリシーを評価するために、`Task<AuthenticationState>` を `IAuthorizationService` などの他のサービスと組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="461aa-238">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

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
> <span data-ttu-id="461aa-239">Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Authorization` と `Microsoft.AspNetCore.Components.Authorization` の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="461aa-239">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="461aa-240">Blazor WebAssembly アプリでの承認</span><span class="sxs-lookup"><span data-stu-id="461aa-240">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="461aa-241">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、承認チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="461aa-241">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="461aa-242">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="461aa-242">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="461aa-243">**常に、クライアント側アプリからアクセスされるすべての API エンドポイント内のサーバー上で承認チェックを実行します。**</span><span class="sxs-lookup"><span data-stu-id="461aa-243">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="461aa-244">詳細については、<xref:security/blazor/webassembly/index> の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="461aa-244">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="461aa-245">エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="461aa-245">Troubleshoot errors</span></span>

<span data-ttu-id="461aa-246">一般的なエラー:</span><span class="sxs-lookup"><span data-stu-id="461aa-246">Common errors:</span></span>

* <span data-ttu-id="461aa-247">**承認には、型 Task\<AuthenticationState> のカスケード パラメーターが必要です。これを実行するには CascadingAuthenticationState の使用を検討します。**</span><span class="sxs-lookup"><span data-stu-id="461aa-247">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="461aa-248">`authenticationStateTask`\*\* に対して\*\*`null` 値を受け取ります</span><span class="sxs-lookup"><span data-stu-id="461aa-248">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="461aa-249">認証が有効な Blazor サーバー テンプレートを使用してプロジェクトが作成されなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="461aa-249">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="461aa-250">次のように、*App.razor* などの UI ツリーの一部に `<CascadingAuthenticationState>` をラップします。</span><span class="sxs-lookup"><span data-stu-id="461aa-250">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="461aa-251">`CascadingAuthenticationState` には `Task<AuthenticationState>` カスケード パラメーターが用意されており、次に基となる `AuthenticationStateProvider` DI サービスから受け取ります。</span><span class="sxs-lookup"><span data-stu-id="461aa-251">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="461aa-252">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="461aa-252">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="461aa-253">[すばらしい Blazor: 認証](https://github.com/AdrienTorris/awesome-blazor#authentication) コミュニティのサンプル リンク</span><span class="sxs-lookup"><span data-stu-id="461aa-253">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
