---
title: 認証ライブラリを使用して、ASP.NET Core Blazor Webasスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: f9cc2884dcd94c729c45a056ae4327a2c75d34be
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083591"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="1cca2-102">認証ライブラリを使用して、ASP.NET Core Blazor Webasスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="1cca2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="1cca2-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1cca2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1cca2-104">`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリを使用する Blazor WebAssembly スタンドアロンアプリを作成するには、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1cca2-104">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="1cca2-105">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-105">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1cca2-106">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="1cca2-106">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="1cca2-107">Visual Studio で[Blazor WebAssembly を作成](xref:blazor/get-started)します。</span><span class="sxs-lookup"><span data-stu-id="1cca2-107">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="1cca2-108">[**ユーザーアカウントをアプリ内に格納**する] オプションを使用して、**認証**を**個々のユーザーアカウント**に設定します。</span><span class="sxs-lookup"><span data-stu-id="1cca2-108">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1cca2-109">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="1cca2-109">Authentication package</span></span>

<span data-ttu-id="1cca2-110">個々のユーザーアカウントを使用するようにアプリを作成すると、アプリは、アプリのプロジェクトファイル内の `Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージのパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1cca2-110">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="1cca2-111">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="1cca2-111">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1cca2-112">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="1cca2-112">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="1cca2-113">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-113">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1cca2-114">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="1cca2-114">Authentication service support</span></span>

<span data-ttu-id="1cca2-115">ユーザー認証のサポートは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージによって提供される `AddOidcAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-115">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="1cca2-116">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="1cca2-116">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1cca2-117">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="1cca2-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="1cca2-118">スタンドアロンアプリの認証サポートは、Open ID Connect (OIDC) を使用して提供されます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-118">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="1cca2-119">`AddOidcAuthentication` メソッドは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-119">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="1cca2-120">アプリの構成に必要な値は、Google、Microsoft、その他の OIDC に準拠しているプロバイダーなどの IP から取得できます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-120">The values required for configuring the app can be obtained from the IP, such as Google, Microsoft, or other OIDC-compliant provider.</span></span> <span data-ttu-id="1cca2-121">アプリを登録するときに値を取得します。これは通常、オンラインポータルで実行されます。</span><span class="sxs-lookup"><span data-stu-id="1cca2-121">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="index-page"></a><span data-ttu-id="1cca2-122">Index ページ</span><span class="sxs-lookup"><span data-stu-id="1cca2-122">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="1cca2-123">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="1cca2-123">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1cca2-124">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1cca2-124">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1cca2-125">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1cca2-125">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1cca2-126">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="1cca2-126">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
