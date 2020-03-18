---
title: Azure Active Directory B2C を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: b4d32e91b4013cbea37baecb972a535d2874d3d1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434461"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="538ce-102">Azure Active Directory B2C を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="538ce-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="538ce-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="538ce-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="538ce-104">[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用して認証を行う Blazor WebAssembly スタンドアロンアプリを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="538ce-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="538ce-105">テナントを作成し、Azure Portal で web アプリを登録するには、次のトピックのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="538ce-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="538ce-106">[AAD B2C のテナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)し、次の情報を記録 &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="538ce-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="538ce-107">1\.</span><span class="sxs-lookup"><span data-stu-id="538ce-107">1\.</span></span> <span data-ttu-id="538ce-108">AAD B2C インスタンス (たとえば、末尾のスラッシュを含む `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="538ce-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="538ce-109">2\.</span><span class="sxs-lookup"><span data-stu-id="538ce-109">2\.</span></span> <span data-ttu-id="538ce-110">テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="538ce-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="538ce-111">アプリの登録時に次の選択を行うために、 [web アプリケーション &ndash; 登録](/azure/active-directory-b2c/tutorial-register-applications)します。</span><span class="sxs-lookup"><span data-stu-id="538ce-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="538ce-112">1\.</span><span class="sxs-lookup"><span data-stu-id="538ce-112">1\.</span></span> <span data-ttu-id="538ce-113">[ **Web アプリ/WEB API** **] を [はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="538ce-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="538ce-114">2\.</span><span class="sxs-lookup"><span data-stu-id="538ce-114">2\.</span></span> <span data-ttu-id="538ce-115">[**暗黙的フローを許可**する **] を [はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="538ce-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="538ce-116">3\.</span><span class="sxs-lookup"><span data-stu-id="538ce-116">3\.</span></span> <span data-ttu-id="538ce-117">`https://localhost:5001/authentication/login-callback`の**応答 URL**を追加します。</span><span class="sxs-lookup"><span data-stu-id="538ce-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="538ce-118">アプリケーション ID (クライアント ID) を記録します (たとえば、`11111111-1111-1111-1111-111111111111`)。</span><span class="sxs-lookup"><span data-stu-id="538ce-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="538ce-119">サインアップとサインインのユーザーフローを作成 &ndash;[ユーザーフローを作成](/azure/active-directory-b2c/tutorial-create-user-flows)します。</span><span class="sxs-lookup"><span data-stu-id="538ce-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="538ce-120">少なくとも、 **Application claim** > **Display Name** user attribute を選択して、`LoginDisplay` コンポーネント (*Shared/logindisplay. razor*) に `context.User.Identity.Name` を設定します。</span><span class="sxs-lookup"><span data-stu-id="538ce-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="538ce-121">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (たとえば、`B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="538ce-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="538ce-122">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="538ce-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="538ce-123">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="538ce-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="538ce-124">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="538ce-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="538ce-125">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="538ce-125">Authentication package</span></span>

<span data-ttu-id="538ce-126">個々の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="538ce-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="538ce-127">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="538ce-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="538ce-128">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="538ce-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="538ce-129">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="538ce-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="538ce-130">`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="538ce-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="538ce-131">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="538ce-131">Authentication service support</span></span>

<span data-ttu-id="538ce-132">ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="538ce-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="538ce-133">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="538ce-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="538ce-134">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="538ce-134">*Program.cs*:</span></span>

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

<span data-ttu-id="538ce-135">`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="538ce-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="538ce-136">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="538ce-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="538ce-137">Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="538ce-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="538ce-138">サインインフローの一部としてトークンをプロビジョニングするには、`MsalProviderOptions`の既定のアクセストークンスコープにスコープを追加します。</span><span class="sxs-lookup"><span data-stu-id="538ce-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a><span data-ttu-id="538ce-139">Index ページ</span><span class="sxs-lookup"><span data-stu-id="538ce-139">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="538ce-140">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="538ce-140">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="538ce-141">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="538ce-141">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="538ce-142">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="538ce-142">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="538ce-143">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="538ce-143">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="538ce-144">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="538ce-144">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="538ce-145">チュートリアル: Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="538ce-145">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
