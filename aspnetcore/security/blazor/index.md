---
title: ASP.NET Core Blazor の認証と承認
author: guardrex
description: Blazor の認証と承認のシナリオについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 2ebc4d72191dff33a7fb6170650be67c3836cdaa
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73964002"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="41de2-103">ASP.NET Core Blazor の認証と承認</span><span class="sxs-lookup"><span data-stu-id="41de2-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="41de2-104">作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="41de2-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="41de2-105">ASP.NET Core は、Blazor アプリのセキュリティの構成と管理をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="41de2-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="41de2-106">Blazor サーバー アプリと Blazor WebAssembly アプリのセキュリティ シナリオは異なります。</span><span class="sxs-lookup"><span data-stu-id="41de2-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="41de2-107">Blazor サーバー アプリはサーバー上で動作するため、承認チェックでは以下のことを判断できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="41de2-108">ユーザーに表示される UI オプション (たとえば、ユーザーが利用できるメニュー エントリ)。</span><span class="sxs-lookup"><span data-stu-id="41de2-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="41de2-109">アプリとコンポーネントの領域に対するアクセス規則。</span><span class="sxs-lookup"><span data-stu-id="41de2-109">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="41de2-110"> WebAssembly アプリはクライアント上で動作します。</span><span class="sxs-lookup"><span data-stu-id="41de2-110"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="41de2-111">承認は、表示する UI オプションを決定するために "*のみ*" 使用されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="41de2-112">クライアント側のチェックはユーザーによって変更またはバイパスされる可能性があるため、Blazor WebAssembly アプリでは承認アクセス規則を適用できません。</span><span class="sxs-lookup"><span data-stu-id="41de2-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="41de2-113">認証</span><span class="sxs-lookup"><span data-stu-id="41de2-113">Authentication</span></span>

Blazor<span data-ttu-id="41de2-114"> は、既存の ASP.NET Core 認証メカニズムを使用してユーザーの ID を証明します。</span><span class="sxs-lookup"><span data-stu-id="41de2-114"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="41de2-115">詳細なメカニズムは、Blazor アプリのホスティング方法、Blazor サーバーか Blazor WebAssembly かによって異なります。</span><span class="sxs-lookup"><span data-stu-id="41de2-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="41de2-116"> サーバー認証</span><span class="sxs-lookup"><span data-stu-id="41de2-116"> Server authentication</span></span>

Blazor<span data-ttu-id="41de2-117"> サーバー アプリは、SignalR を使用して作成されたリアルタイム接続を介して動作します。</span><span class="sxs-lookup"><span data-stu-id="41de2-117"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="41de2-118">[SignalR ベースのアプリの認証](xref:signalr/authn-and-authz)は、接続が確立したときに処理されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="41de2-119">認証は、Cookie または他のベアラー トークンに基づいています。</span><span class="sxs-lookup"><span data-stu-id="41de2-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="41de2-120">プロジェクトの作成時に、Blazor サーバー プロジェクト テンプレートで認証を設定できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="41de2-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41de2-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41de2-122">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="41de2-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="41de2-123">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで **[Blazor サーバー アプリ]** テンプレートを選択した後、 **[認証]** の下の **[変更]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="41de2-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="41de2-124">ダイアログが開き、他の ASP.NET Core プロジェクトで使用できるものと同じ一連の認証メカニズムが表示されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="41de2-125">**認証なし**</span><span class="sxs-lookup"><span data-stu-id="41de2-125">**No Authentication**</span></span>
* <span data-ttu-id="41de2-126">**個人のユーザー アカウント** &ndash; ユーザー アカウントは次に格納できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="41de2-127">ASP.NET Core の [ID](xref:security/authentication/identity) システムを使用するアプリ内。</span><span class="sxs-lookup"><span data-stu-id="41de2-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="41de2-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)。</span><span class="sxs-lookup"><span data-stu-id="41de2-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="41de2-129">**職場または学校アカウント**</span><span class="sxs-lookup"><span data-stu-id="41de2-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="41de2-130">**Windows 認証**</span><span class="sxs-lookup"><span data-stu-id="41de2-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="41de2-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41de2-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="41de2-132">認証メカニズムを使用して新しい Blazor サーバー プロジェクトを作成するには、<xref:blazor/get-started> の記事の Visual Studio Code のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="41de2-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="41de2-133">次の表に、使用できる認証値 (`{AUTHENTICATION}`) を示します。</span><span class="sxs-lookup"><span data-stu-id="41de2-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="41de2-134">認証メカニズム</span><span class="sxs-lookup"><span data-stu-id="41de2-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="41de2-135">`{AUTHENTICATION}` の値</span><span class="sxs-lookup"><span data-stu-id="41de2-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="41de2-136">[認証なし]</span><span class="sxs-lookup"><span data-stu-id="41de2-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="41de2-137">個人</span><span class="sxs-lookup"><span data-stu-id="41de2-137">Individual</span></span><br><span data-ttu-id="41de2-138">ASP.NET Core ID を使用するアプリに格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="41de2-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="41de2-139">個人</span><span class="sxs-lookup"><span data-stu-id="41de2-139">Individual</span></span><br><span data-ttu-id="41de2-140">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) に格納されているユーザー。</span><span class="sxs-lookup"><span data-stu-id="41de2-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="41de2-141">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="41de2-141">Work or School Accounts</span></span><br><span data-ttu-id="41de2-142">単一のテナントに対する組織認証。</span><span class="sxs-lookup"><span data-stu-id="41de2-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="41de2-143">職場または学校アカウント</span><span class="sxs-lookup"><span data-stu-id="41de2-143">Work or School Accounts</span></span><br><span data-ttu-id="41de2-144">複数のテナントに対する組織認証です。</span><span class="sxs-lookup"><span data-stu-id="41de2-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="41de2-145">Windows 認証</span><span class="sxs-lookup"><span data-stu-id="41de2-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="41de2-146">このコマンドで、`{APP NAME}` プレースホルダーに指定された値の名前が付けられたフォルダーが作成され、そのフォルダー名がアプリ名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="41de2-147">詳細については、「.NET Core ガイド」の [dotnet new](/dotnet/core/tools/dotnet-new) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

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

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="41de2-148"> WebAssembly 認証</span><span class="sxs-lookup"><span data-stu-id="41de2-148"> WebAssembly authentication</span></span>

<span data-ttu-id="41de2-149">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、認証チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="41de2-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="41de2-150">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="41de2-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="41de2-151">アプリのプロジェクト ファイルに、[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="41de2-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="41de2-152">Blazor WebAssembly アプリ用のカスタム `AuthenticationStateProvider` サービスの実装については、以降のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="41de2-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="41de2-153">AuthenticationStateProvider サービス</span><span class="sxs-lookup"><span data-stu-id="41de2-153">AuthenticationStateProvider service</span></span>

Blazor<span data-ttu-id="41de2-154"> サーバー アプリには、ASP.NET Core の `HttpContext.User` から認証状態データを取得する組み込みの `AuthenticationStateProvider` サービスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="41de2-154"> Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="41de2-155">このようにして、認証状態は既存の ASP.NET Core サーバー側認証メカニズムと統合されています。</span><span class="sxs-lookup"><span data-stu-id="41de2-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="41de2-156">`AuthenticationStateProvider` は、認証状態を取得するために `AuthorizeView` コンポーネントと `CascadingAuthenticationState` コンポーネントによって使用される基となるサービスです。</span><span class="sxs-lookup"><span data-stu-id="41de2-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="41de2-157">通常、`AuthenticationStateProvider` は直接使用しません。</span><span class="sxs-lookup"><span data-stu-id="41de2-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="41de2-158">この記事で後述する [AuthorizeView コンポーネント](#authorizeview-component)または [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) のアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="41de2-159">`AuthenticationStateProvider` を直接使用することの主な欠点は、基となる認証状態データが変更されてもコンポーネントに自動的に通知されないことです。</span><span class="sxs-lookup"><span data-stu-id="41de2-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="41de2-160">次の例に示すように、`AuthenticationStateProvider` サービスから現在のユーザーの <xref:System.Security.Claims.ClaimsPrincipal> データを提供できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```cshtml
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

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

<span data-ttu-id="41de2-161">`user.Identity.IsAuthenticated` が `true` である場合、ユーザーが <xref:System.Security.Claims.ClaimsPrincipal> であるため、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="41de2-162">依存関係の注入 (DI) とサービスの詳細については、<xref:blazor/dependency-injection> と <xref:fundamentals/dependency-injection> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="41de2-163">カスタム AuthenticationStateProvider の実装</span><span class="sxs-lookup"><span data-stu-id="41de2-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="41de2-164">Blazor WebAssembly アプリを構築している場合、またはアプリの仕様でカスタム プロバイダーが必要な場合は、プロバイダーを実装して `GetAuthenticationStateAsync` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="41de2-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

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

<span data-ttu-id="41de2-165">`CustomAuthStateProvider` サービスは `Startup.ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="41de2-165">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Components.Authorization;
// using BlazorSample.Services;

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="41de2-166">`CustomAuthStateProvider` を使用すると、すべてのユーザーはユーザー名 `mrfibuli` で認証されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="41de2-167">認証状態をカスケード パラメーターとして公開する</span><span class="sxs-lookup"><span data-stu-id="41de2-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="41de2-168">ユーザーによってトリガーされたアクションを実行する場合など、認証状態データが手続き型ロジックに必要な場合は、型 `Task<AuthenticationState>` のカスケード パラメーターを定義して認証状態データを取得します。</span><span class="sxs-lookup"><span data-stu-id="41de2-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

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
> <span data-ttu-id="41de2-169">Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Components.Authorization` 名前空間 (`@using Microsoft.AspNetCore.Components.Authorization`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="41de2-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="41de2-170">`user.Identity.IsAuthenticated` が `true` である場合、要求を列挙し、役割のメンバーシップを評価できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="41de2-171">`AuthorizeRouteView` および `CascadingAuthenticationState` のコンポーネントを使用して `Task<AuthenticationState>` カスケード パラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="41de2-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

```cshtml
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

## <a name="authorization"></a><span data-ttu-id="41de2-172">承認</span><span class="sxs-lookup"><span data-stu-id="41de2-172">Authorization</span></span>

<span data-ttu-id="41de2-173">ユーザーが認証されると、ユーザーが実行できる操作を制御する "*承認*" 規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-173">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="41de2-174">通常、アクセスは以下の条件に基づいて許可または拒否されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-174">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="41de2-175">ユーザーが認証されている (サインインしている)。</span><span class="sxs-lookup"><span data-stu-id="41de2-175">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="41de2-176">ユーザーに "*ロール*" が割り当てられている。</span><span class="sxs-lookup"><span data-stu-id="41de2-176">A user is in a *role*.</span></span>
* <span data-ttu-id="41de2-177">ユーザーに "*要求*" がある。</span><span class="sxs-lookup"><span data-stu-id="41de2-177">A user has a *claim*.</span></span>
* <span data-ttu-id="41de2-178">"*ポリシー*" が満たされている。</span><span class="sxs-lookup"><span data-stu-id="41de2-178">A *policy* is satisfied.</span></span>

<span data-ttu-id="41de2-179">これらの各概念は、ASP.NET Core MVC または Razor Pages アプリと同じです。</span><span class="sxs-lookup"><span data-stu-id="41de2-179">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="41de2-180">ASP.NET Core のセキュリティの詳細については、[ASP.NET Core のセキュリティと ID](xref:security/index) の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-180">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="41de2-181">AuthorizeView コンポーネント</span><span class="sxs-lookup"><span data-stu-id="41de2-181">AuthorizeView component</span></span>

<span data-ttu-id="41de2-182">`AuthorizeView` コンポーネントでは、ユーザーに表示を許可するかどうかに応じて UI が選択的に表示されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-182">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="41de2-183">このアプローチは、ユーザーに対してデータを "*表示する*" だけで済み、手続き型ロジックでユーザーの ID を使用する必要がない場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="41de2-183">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="41de2-184">このコンポーネントでは、型 `AuthenticationState` の `context` 変数が公開されており、これを使用して、サインインしたユーザーに関する情報にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="41de2-184">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="41de2-185">ユーザーが認証されていない場合は、表示用に異なるコンテンツを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="41de2-185">You can also supply different content for display if the user isn't authenticated:</span></span>

```cshtml
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

<span data-ttu-id="41de2-186">`<Authorized>` および `<NotAuthorized>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="41de2-186">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="41de2-187">UI オプションまたはアクセスを制御するロールやポリシーなどの承認条件については、「[承認](#authorization)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-187">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="41de2-188">承認条件が指定されていない場合、`AuthorizeView` では既定のポリシーが使用され、以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="41de2-188">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="41de2-189">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="41de2-189">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="41de2-190">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="41de2-190">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="41de2-191">ロールベースとリソースベースの承認</span><span class="sxs-lookup"><span data-stu-id="41de2-191">Role-based and policy-based authorization</span></span>

<span data-ttu-id="41de2-192">`AuthorizeView` コンポーネントは、"*ロールベース*" または "*ポリシーベース*" の承認をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="41de2-192">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="41de2-193">ロールベースの承認の場合は、`Roles` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-193">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="41de2-194">詳細については、<xref:security/authorization/roles> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-194">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="41de2-195">ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-195">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="41de2-196">要求ベースの承認は、ポリシーベースの承認の特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="41de2-196">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="41de2-197">たとえば、ユーザーが特定の要求持つことを必須にするポリシーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-197">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="41de2-198">詳細については、<xref:security/authorization/policies> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-198">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="41de2-199">これらの API は、Blazor サーバー アプリまたは Blazor WebAssembly アプリのどちらでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-199">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="41de2-200">`Roles` も `Policy` も指定されていない場合、`AuthorizeView` には既定のポリシーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-200">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="41de2-201">非同期認証中に表示されるコンテンツ</span><span class="sxs-lookup"><span data-stu-id="41de2-201">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="41de2-202"> では、認証状態を "*非同期的に*" 決定することができます。</span><span class="sxs-lookup"><span data-stu-id="41de2-202"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="41de2-203">このアプローチの主なシナリオは、認証のために外部エンドポイントに要求を送信する Blazor WebAssembly アプリです。</span><span class="sxs-lookup"><span data-stu-id="41de2-203">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="41de2-204">認証が進行中の間、`AuthorizeView` には既定でコンテンツが表示されません。</span><span class="sxs-lookup"><span data-stu-id="41de2-204">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="41de2-205">認証が行われている間にコンテンツを表示するには、`<Authorizing>` 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-205">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```cshtml
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

<span data-ttu-id="41de2-206">通常、このアプローチは Blazor サーバー アプリには適用されません。</span><span class="sxs-lookup"><span data-stu-id="41de2-206">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="41de2-207"> サーバー アプリでは、状態が確立されるとすぐに認証状態が認識されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-207"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="41de2-208">`Authorizing` コンテンツは Blazor サーバー アプリの `AuthorizeView` コンポーネントで提供できますが、コンテンツは表示されません。</span><span class="sxs-lookup"><span data-stu-id="41de2-208">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="41de2-209">[Authorize] 属性</span><span class="sxs-lookup"><span data-stu-id="41de2-209">[Authorize] attribute</span></span>

<span data-ttu-id="41de2-210">`[Authorize]` 属性は Razor コンポーネント内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-210">The `[Authorize]` attribute can be used in Razor components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="41de2-211">Blazor WebAssembly アプリ コンポーネントで、このセクション内の例に `Microsoft.AspNetCore.Authorization` 名前空間 (`@using Microsoft.AspNetCore.Authorization`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="41de2-211">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="41de2-212">Blazor ルーター経由で到達した `@page` コンポーネントにのみ `[Authorize]` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-212">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="41de2-213">承認はルーティングの一面としてのみ実行され、ページ内にレンダリングされた子コンポーネントに対しては実行され "*ません*"。</span><span class="sxs-lookup"><span data-stu-id="41de2-213">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="41de2-214">ページ内の特定部分の表示を承認するには、代わりに `AuthorizeView` を使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-214">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="41de2-215">`[Authorize]` 属性は、ロールベースまたはポリシーベースの承認もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="41de2-215">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="41de2-216">ロールベースの承認の場合は、`Roles` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-216">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="41de2-217">ポリシーベースの承認の場合は、`Policy` パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="41de2-217">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="41de2-218">`Roles` も `Policy` も指定されていない場合、`[Authorize]` には既定のポリシーが使用されます。これは既定で以下が扱われます。</span><span class="sxs-lookup"><span data-stu-id="41de2-218">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="41de2-219">承認済みの認証された (サインインした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="41de2-219">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="41de2-220">未承認の認証されていない (サインアウトした) ユーザー。</span><span class="sxs-lookup"><span data-stu-id="41de2-220">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="41de2-221">Router コンポーネントを使用して承認されていないコンテンツをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="41de2-221">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="41de2-222">`Router` コンポーネントを `AuthorizeRouteView` コンポーネントとともに使用すると、以下の場合にアプリがカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="41de2-222">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="41de2-223">コンテンツが見つからない。</span><span class="sxs-lookup"><span data-stu-id="41de2-223">Content isn't found.</span></span>
* <span data-ttu-id="41de2-224">ユーザーはコンポーネントに適用されている `[Authorize]` 条件に失敗します。</span><span class="sxs-lookup"><span data-stu-id="41de2-224">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="41de2-225">`[Authorize]` 属性については、「[[Authorize] 属性](#authorize-attribute)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="41de2-225">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="41de2-226">非同期認証が実行中です。</span><span class="sxs-lookup"><span data-stu-id="41de2-226">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="41de2-227">既定の Blazor サーバー プロジェクト テンプレートでは、*App.razor* ファイルがカスタム コンテンツの設定方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="41de2-227">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```cshtml
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

<span data-ttu-id="41de2-228">`<NotFound>`、`<NotAuthorized>`、および `<Authorizing>` タグには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="41de2-228">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="41de2-229">`<NotAuthorized>` 要素が指定されていない場合、`AuthorizeRouteView` には次のフォールバック メッセージが使用されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-229">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="41de2-230">認証状態の変更に関する通知</span><span class="sxs-lookup"><span data-stu-id="41de2-230">Notification about authentication state changes</span></span>

<span data-ttu-id="41de2-231">基となる認証状態データが変わったとアプリが判断した場合 (たとえば、ユーザーがサインアウトした、または別のユーザーがロールを変更したなど)、カスタムの `AuthenticationStateProvider` はオプションで `AuthenticationStateProvider` 基底クラスのメソッド `NotifyAuthenticationStateChanged` を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="41de2-231">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="41de2-232">これにより、新しいデータを使用して再表示するように、認証状態データ (`AuthorizeView` など) がコンシューマーに通知されます。</span><span class="sxs-lookup"><span data-stu-id="41de2-232">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="41de2-233">手続き型ロジック</span><span class="sxs-lookup"><span data-stu-id="41de2-233">Procedural logic</span></span>

<span data-ttu-id="41de2-234">手続き型ロジックの一部としてアプリで承認規則をチェックする必要がある場合、型 `Task<AuthenticationState>` のカスケード パラメーターを使用してユーザーの <xref:System.Security.Claims.ClaimsPrincipal> を取得します。</span><span class="sxs-lookup"><span data-stu-id="41de2-234">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="41de2-235">ポリシーを評価するために、`Task<AuthenticationState>` を `IAuthorizationService` などの他のサービスと組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="41de2-235">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```cshtml
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
> <span data-ttu-id="41de2-236">Blazor WebAssembly アプリ コンポーネントで、`Microsoft.AspNetCore.Authorization` と `Microsoft.AspNetCore.Components.Authorization` の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="41de2-236">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```cshtml
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="41de2-237">Blazor WebAssembly アプリでの承認</span><span class="sxs-lookup"><span data-stu-id="41de2-237">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="41de2-238">Blazor WebAssembly アプリでは、すべてのクライアント側コードがユーザーによって変更される可能性があるため、承認チェックがバイパスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="41de2-238">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="41de2-239">JavaScript SPA フレームワークや任意のオペレーティング システム用のネイティブ アプリを含め、すべてのクライアント側アプリのテクノロジにも同じことが当てはまります。</span><span class="sxs-lookup"><span data-stu-id="41de2-239">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="41de2-240">**常に、クライアント側アプリからアクセスされるすべての API エンドポイント内のサーバー上で承認チェックを実行します。**</span><span class="sxs-lookup"><span data-stu-id="41de2-240">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="41de2-241">エラーをトラブルシューティングする</span><span class="sxs-lookup"><span data-stu-id="41de2-241">Troubleshoot errors</span></span>

<span data-ttu-id="41de2-242">一般的なエラー:</span><span class="sxs-lookup"><span data-stu-id="41de2-242">Common errors:</span></span>

* <span data-ttu-id="41de2-243">**承認には、型 Task\<AuthenticationState> のカスケード パラメーターが必要です。これを実行するには CascadingAuthenticationState の使用を検討します。**</span><span class="sxs-lookup"><span data-stu-id="41de2-243">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="41de2-244">`authenticationStateTask` **に対して**`null` 値を受け取ります</span><span class="sxs-lookup"><span data-stu-id="41de2-244">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="41de2-245">認証が有効な Blazor サーバー テンプレートを使用してプロジェクトが作成されなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="41de2-245">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="41de2-246">次のように、*App.razor* などの UI ツリーの一部に `<CascadingAuthenticationState>` をラップします。</span><span class="sxs-lookup"><span data-stu-id="41de2-246">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="41de2-247">`CascadingAuthenticationState` には `Task<AuthenticationState>` カスケード パラメーターが用意されており、次に基となる `AuthenticationStateProvider` DI サービスから受け取ります。</span><span class="sxs-lookup"><span data-stu-id="41de2-247">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41de2-248">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="41de2-248">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
