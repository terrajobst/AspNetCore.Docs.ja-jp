---
title: Azure Active Directory B2C を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0ea42943c908d8cf9d083c1cfc568c1835588ce9
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083657"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="bb7c7-102">Azure Active Directory B2C を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="bb7c7-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="bb7c7-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb7c7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="bb7c7-104">[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用して認証を行う Blazor WebAssembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="bb7c7-105">テナントを作成し、Azure Portal で web アプリを登録するには、次のトピックのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="bb7c7-106">[AAD B2C のテナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)し、次の情報を記録 &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="bb7c7-107">1\.</span><span class="sxs-lookup"><span data-stu-id="bb7c7-107">1\.</span></span> <span data-ttu-id="bb7c7-108">AAD B2C インスタンス (たとえば、末尾のスラッシュを含む `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="bb7c7-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="bb7c7-109">2\.</span><span class="sxs-lookup"><span data-stu-id="bb7c7-109">2\.</span></span> <span data-ttu-id="bb7c7-110">テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="bb7c7-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="bb7c7-111">アプリの登録時に次の選択を行うために、 [web アプリケーション &ndash; 登録](/azure/active-directory-b2c/tutorial-register-applications)します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="bb7c7-112">1\.</span><span class="sxs-lookup"><span data-stu-id="bb7c7-112">1\.</span></span> <span data-ttu-id="bb7c7-113">[ **Web アプリ/WEB API** **] を [はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="bb7c7-114">2\.</span><span class="sxs-lookup"><span data-stu-id="bb7c7-114">2\.</span></span> <span data-ttu-id="bb7c7-115">[**暗黙的フローを許可**する **] を [はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="bb7c7-116">3\.</span><span class="sxs-lookup"><span data-stu-id="bb7c7-116">3\.</span></span> <span data-ttu-id="bb7c7-117">`https://localhost:5001/authentication/login-callback`の**応答 URL**を追加します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="bb7c7-118">アプリケーション ID (クライアント ID) を記録します (たとえば、`11111111-1111-1111-1111-111111111111`)。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="bb7c7-119">[ユーザーフローを作成](/azure/active-directory-b2c/tutorial-create-user-flows)&サインアップとサインインのユーザーフローを作成します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) & ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="bb7c7-120">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (たとえば、`B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-120">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="bb7c7-121">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="bb7c7-122">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bb7c7-123">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="bb7c7-124">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="bb7c7-124">Authentication package</span></span>

<span data-ttu-id="bb7c7-125">個々の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-125">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="bb7c7-126">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bb7c7-127">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="bb7c7-128">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="bb7c7-129">`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="bb7c7-130">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="bb7c7-130">Authentication service support</span></span>

<span data-ttu-id="bb7c7-131">ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="bb7c7-132">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bb7c7-133">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="bb7c7-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="bb7c7-134">`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="bb7c7-135">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="bb7c7-136">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-136">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="bb7c7-137">サインインフローの一部としてトークンをプロビジョニングするには、`MsalProviderOptions`の既定のアクセストークンスコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="bb7c7-137">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="bb7c7-138">Index ページ</span><span class="sxs-lookup"><span data-stu-id="bb7c7-138">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="bb7c7-139">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="bb7c7-139">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="bb7c7-140">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bb7c7-140">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="bb7c7-141">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bb7c7-141">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="bb7c7-142">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bb7c7-142">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bb7c7-143">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="bb7c7-143">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="bb7c7-144">チュートリアル: Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="bb7c7-144">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
