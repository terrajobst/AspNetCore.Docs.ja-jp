---
title: Microsoft アカウントを使用して、ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219260"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="97531-102">Microsoft アカウントを使用して、ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="97531-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="97531-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="97531-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="97531-104">認証に[Microsoft アカウントと Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する Blazor WebAssembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="97531-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="97531-105">AAD テナントと web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="97531-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="97531-106">Azure portal の**Azure Active Directory** > **アプリの登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="97531-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="97531-107">1\.</span><span class="sxs-lookup"><span data-stu-id="97531-107">1\.</span></span> <span data-ttu-id="97531-108">アプリの**名前**を指定します ( **Blazor クライアント AAD**など)。</span><span class="sxs-lookup"><span data-stu-id="97531-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="97531-109">2\.</span><span class="sxs-lookup"><span data-stu-id="97531-109">2\.</span></span> <span data-ttu-id="97531-110">**[サポートされているアカウントの種類]** で、**任意の組織ディレクトリのアカウント**を選択します。</span><span class="sxs-lookup"><span data-stu-id="97531-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="97531-111">3\.</span><span class="sxs-lookup"><span data-stu-id="97531-111">3\.</span></span> <span data-ttu-id="97531-112">**[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="97531-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="97531-113">4\.</span><span class="sxs-lookup"><span data-stu-id="97531-113">4\.</span></span> <span data-ttu-id="97531-114">アクセス\*\*許可 > \*\* **openid and offline_access permissions に管理者求めるプロンプトを付与する** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="97531-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="97531-115">5\.</span><span class="sxs-lookup"><span data-stu-id="97531-115">5\.</span></span> <span data-ttu-id="97531-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97531-116">Select **Register**.</span></span>

   <span data-ttu-id="97531-117">**認証** > **プラットフォームの構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="97531-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="97531-118">1\.</span><span class="sxs-lookup"><span data-stu-id="97531-118">1\.</span></span> <span data-ttu-id="97531-119">`https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="97531-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="97531-120">2\.</span><span class="sxs-lookup"><span data-stu-id="97531-120">2\.</span></span> <span data-ttu-id="97531-121">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="97531-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="97531-122">3\.</span><span class="sxs-lookup"><span data-stu-id="97531-122">3\.</span></span> <span data-ttu-id="97531-123">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="97531-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="97531-124">4\.</span><span class="sxs-lookup"><span data-stu-id="97531-124">4\.</span></span> <span data-ttu-id="97531-125">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97531-125">Select the **Save** button.</span></span>

   <span data-ttu-id="97531-126">アプリケーション ID (クライアント ID) を記録します (たとえば、`11111111-1111-1111-1111-111111111111`)。</span><span class="sxs-lookup"><span data-stu-id="97531-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="97531-127">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="97531-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="97531-128">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="97531-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="97531-129">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="97531-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="97531-130">アプリを作成すると、次のことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="97531-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="97531-131">Microsoft アカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="97531-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="97531-132">アプリが正しく構成されている場合は、スタンドアロンの Blazor アプリの場合と同じ方法を使用して、Microsoft Api のアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="97531-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="97531-133">詳細については、「[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97531-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="97531-134">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="97531-134">Authentication package</span></span>

<span data-ttu-id="97531-135">職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="97531-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="97531-136">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="97531-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="97531-137">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="97531-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="97531-138">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97531-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="97531-139">`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="97531-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="97531-140">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="97531-140">Authentication service support</span></span>

<span data-ttu-id="97531-141">ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="97531-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="97531-142">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="97531-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="97531-143">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="97531-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="97531-144">`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="97531-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="97531-145">アプリの構成に必要な値は、アプリを登録するときに Microsoft アカウントの構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="97531-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="97531-146">Index ページ</span><span class="sxs-lookup"><span data-stu-id="97531-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="97531-147">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="97531-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="97531-148">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="97531-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="97531-149">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="97531-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="97531-150">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="97531-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="97531-151">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="97531-151">Additional resources</span></span>

* [<span data-ttu-id="97531-152">クイックスタート: Microsoft id プラットフォームにアプリケーションを登録する</span><span class="sxs-lookup"><span data-stu-id="97531-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="97531-153">クイックスタート: web Api を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="97531-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
