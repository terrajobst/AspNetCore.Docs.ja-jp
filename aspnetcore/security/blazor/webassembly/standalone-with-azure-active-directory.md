---
title: Azure Active Directory を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 76bcac29d86a236e56c0eaea24a694c4845ecbcf
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083585"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="3c984-102">Azure Active Directory を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="3c984-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="3c984-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3c984-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="3c984-104">認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、Blazor WebAssembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="3c984-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="3c984-105">[AAD テナントと web アプリケーションを作成し](/azure/active-directory/develop/v2-overview)ます。</span><span class="sxs-lookup"><span data-stu-id="3c984-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="3c984-106">Azure portal の**Azure Active Directory** > **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="3c984-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="3c984-107">アプリの**名前**を指定します ( **Blazor クライアント AAD**など)。</span><span class="sxs-lookup"><span data-stu-id="3c984-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="3c984-108">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c984-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="3c984-109">この組織の**ディレクトリにあるアカウント**は、このエクスペリエンスのためにのみ選択できます。</span><span class="sxs-lookup"><span data-stu-id="3c984-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="3c984-110">**[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="3c984-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="3c984-111">アクセス\*\*許可 > \*\* **openid and offline_access permissions に管理者求めるプロンプトを付与する** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="3c984-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="3c984-112">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c984-112">Select **Register**.</span></span>

<span data-ttu-id="3c984-113">**認証** > **プラットフォームの構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="3c984-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="3c984-114">`https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="3c984-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="3c984-115">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3c984-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="3c984-116">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="3c984-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="3c984-117">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3c984-117">Select the **Save** button.</span></span>

<span data-ttu-id="3c984-118">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="3c984-118">Record the following information:</span></span>

* <span data-ttu-id="3c984-119">アプリケーション ID (クライアント ID) (たとえば、`11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="3c984-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="3c984-120">ディレクトリ ID (テナント ID) (`22222222-2222-2222-2222-222222222222`など)</span><span class="sxs-lookup"><span data-stu-id="3c984-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="3c984-121">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3c984-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="3c984-122">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="3c984-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3c984-123">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="3c984-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="3c984-124">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="3c984-124">Authentication package</span></span>

<span data-ttu-id="3c984-125">職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="3c984-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="3c984-126">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3c984-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3c984-127">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="3c984-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="3c984-128">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3c984-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="3c984-129">`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3c984-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="3c984-130">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="3c984-130">Authentication service support</span></span>

<span data-ttu-id="3c984-131">ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="3c984-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="3c984-132">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="3c984-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="3c984-133">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="3c984-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="3c984-134">`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="3c984-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="3c984-135">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="3c984-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="3c984-136">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="3c984-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="3c984-137">サインインフローの一部としてトークンをプロビジョニングするには、`MsalProviderOptions`の既定のアクセストークンスコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="3c984-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="3c984-138">既定のアクセストークンのスコープは、`{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (`11111111-1111-1111-1111-111111111111/API.Access`など) の形式である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3c984-138">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="3c984-139">スキームまたはスキームとホストがスコープ設定に提供されている場合 (Azure Portal に示されているように)、*サーバー API アプリ*から承認されて*いない 401*の応答を受信すると、*クライアントアプリ*は未処理の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="3c984-139">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

## <a name="index-page"></a><span data-ttu-id="3c984-140">Index ページ</span><span class="sxs-lookup"><span data-stu-id="3c984-140">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="3c984-141">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c984-141">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="3c984-142">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c984-142">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="3c984-143">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c984-143">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="3c984-144">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3c984-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3c984-145">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3c984-145">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
