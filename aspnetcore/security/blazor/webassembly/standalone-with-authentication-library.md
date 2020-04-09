---
title: 認証ライブラリを使用BlazorしてASP.NET Core WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977042"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="d7e54-102">認証ライブラリを使用BlazorしてASP.NET Core WebAssembly スタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="d7e54-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="d7e54-103">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7e54-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d7e54-104">*Azure アクティブ ディレクトリ (AAD) および Azure アクティブ ディレクトリ B2C (AAD B2C) については、このトピックのガイダンスに従わないでください。この目次ノードの AAD および AAD B2C のトピックを参照してください。*</span><span class="sxs-lookup"><span data-stu-id="d7e54-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="d7e54-105">ライブラリを使用Blazor`Microsoft.AspNetCore.Components.WebAssembly.Authentication`する WebAssembly スタンドアロン アプリを作成するには、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="d7e54-106">プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d7e54-107">フォルダー名もプロジェクト名の一部になります。</span><span class="sxs-lookup"><span data-stu-id="d7e54-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="d7e54-108">Visual Studio で[、Web アセンブリBlazorアプリを作成](xref:blazor/get-started)します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="d7e54-109">[アプリ内ユーザー アカウントを保存する] オプションを使用して **、[認証**] を **[個別\*\*\*\*のユーザー アカウント**] に設定します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="d7e54-110">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="d7e54-110">Authentication package</span></span>

<span data-ttu-id="d7e54-111">個々のユーザー アカウントを使用するアプリを作成すると、アプリは自動的にアプリのプロジェクト`Microsoft.AspNetCore.Components.WebAssembly.Authentication`ファイル内のパッケージのパッケージ参照を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d7e54-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="d7e54-112">パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="d7e54-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d7e54-113">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="d7e54-114">上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="d7e54-115">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="d7e54-115">Authentication service support</span></span>

<span data-ttu-id="d7e54-116">ユーザー認証のサポートは、パッケージによって提供される`AddOidcAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.AspNetCore.Components.WebAssembly.Authentication`登録されます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="d7e54-117">このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d7e54-118">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="d7e54-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="d7e54-119">スタンドアロン アプリの認証サポートは、オープン ID 接続 (OIDC) を使用して提供されます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="d7e54-120">この`AddOidcAuthentication`メソッドは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="d7e54-121">アプリの構成に必要な値は、OIDC 準拠の IP から取得できます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="d7e54-122">通常、オンライン ポータルで発生するアプリを登録するときに値を取得します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="d7e54-123">アクセス トークン のスコープ</span><span class="sxs-lookup"><span data-stu-id="d7e54-123">Access token scopes</span></span>

<span data-ttu-id="d7e54-124">Blazor WebAssembly テンプレートは、セキュリティで保護された API のアクセス トークンを要求するようにアプリを自動的に構成しません。</span><span class="sxs-lookup"><span data-stu-id="d7e54-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="d7e54-125">トークンをサインイン フローの一部としてプロビジョニングするには、スコープを の既定のトークン スコープに追加します`OidcProviderOptions`。</span><span class="sxs-lookup"><span data-stu-id="d7e54-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="d7e54-126">Azure Portal がスコープ URI を提供し、API から*401 未承認*の応答を受け取ったときに**アプリが未処理の例外をスロー**する場合は、スキームとホストを含まないスコープ URI を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="d7e54-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="d7e54-127">たとえば、Azure ポータルでは、次のいずれかのスコープ URI 形式を提供できます。</span><span class="sxs-lookup"><span data-stu-id="d7e54-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="d7e54-128">スキームとホストを使用せずにスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="d7e54-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="d7e54-129">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7e54-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="d7e54-130">ファイルをインポートする</span><span class="sxs-lookup"><span data-stu-id="d7e54-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="d7e54-131">Index ページ</span><span class="sxs-lookup"><span data-stu-id="d7e54-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="d7e54-132">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d7e54-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="d7e54-133">コンポーネントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="d7e54-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="d7e54-134">ログインディスプレイコンポーネント</span><span class="sxs-lookup"><span data-stu-id="d7e54-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="d7e54-135">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d7e54-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d7e54-136">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="d7e54-136">Additional resources</span></span>

* [<span data-ttu-id="d7e54-137">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="d7e54-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
