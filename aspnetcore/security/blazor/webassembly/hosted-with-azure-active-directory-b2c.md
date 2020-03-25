---
title: Azure Active Directory B2C を使用して、ASP.NET Core Blazor Webashosted アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 0083f179f85371d4751fb179194417681fc1a01d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219065"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="ee632-102">Azure Active Directory B2C を使用して、ASP.NET Core Blazor Webashosted アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="ee632-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="ee632-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ee632-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="ee632-104">この記事では、認証に[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用する Blazor WebAssembly スタンドアロンアプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ee632-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="ee632-105">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="ee632-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="ee632-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="ee632-106">Create a tenant</span></span>

<span data-ttu-id="ee632-107">[「チュートリアル: Azure Active Directory B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)する」のガイダンスに従って AAD B2C テナントを作成し、次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="ee632-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="ee632-108">AAD B2C インスタンス (たとえば、末尾のスラッシュを含む `https://contoso.b2clogin.com/`)</span><span class="sxs-lookup"><span data-stu-id="ee632-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="ee632-109">テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="ee632-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="ee632-110">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="ee632-110">Register a server API app</span></span>

<span data-ttu-id="ee632-111">[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*サーバー API アプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="ee632-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ee632-112">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-112">Select **New registration**.</span></span>
1. <span data-ttu-id="ee632-113">アプリの**名前**を指定します (たとえば、 **Blazor Server AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="ee632-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ee632-114">**サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="ee632-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="ee632-115">(マルチテナント)。</span><span class="sxs-lookup"><span data-stu-id="ee632-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="ee632-116">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは **[Web]** に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="ee632-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="ee632-117">**Offline_access openid に管理者求めるプロンプトを付与** > **アクセス**許可が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee632-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ee632-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-118">Select **Register**.</span></span>

<span data-ttu-id="ee632-119">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="ee632-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="ee632-120">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="ee632-121">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="ee632-122">**スコープ名**を指定します (たとえば、`API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="ee632-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ee632-123">**管理者の同意の表示名**を指定します (たとえば、`Access API`)。</span><span class="sxs-lookup"><span data-stu-id="ee632-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="ee632-124">**管理者の同意の説明**(`Allows the app to access server app API endpoints.`など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="ee632-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="ee632-125">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee632-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="ee632-126">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-126">Select **Add scope**.</span></span>

<span data-ttu-id="ee632-127">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="ee632-127">Record the following information:</span></span>

* <span data-ttu-id="ee632-128">*サーバー API アプリ*アプリケーション ID (クライアント ID) (たとえば、`11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="ee632-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="ee632-129">ディレクトリ ID (テナント ID) (`222222222-2222-2222-2222-222222222222`など)</span><span class="sxs-lookup"><span data-stu-id="ee632-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="ee632-130">*サーバー API アプリ*アプリ ID URI (たとえば、`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`の場合、Azure portal によって既定値がクライアント ID に設定される可能性があります)</span><span class="sxs-lookup"><span data-stu-id="ee632-130">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="ee632-131">既定のスコープ (たとえば、`API.Access`)</span><span class="sxs-lookup"><span data-stu-id="ee632-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="ee632-132">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="ee632-132">Register a client app</span></span>

<span data-ttu-id="ee632-133">[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*クライアントアプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="ee632-133">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="ee632-134">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-134">Select **New registration**.</span></span>
1. <span data-ttu-id="ee632-135">アプリの**名前**を指定します (たとえば、 **Blazor Client AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="ee632-135">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="ee632-136">**サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。**</span><span class="sxs-lookup"><span data-stu-id="ee632-136">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="ee632-137">(マルチテナント)。</span><span class="sxs-lookup"><span data-stu-id="ee632-137">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="ee632-138">**[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="ee632-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="ee632-139">**Offline_access openid に管理者求めるプロンプトを付与** > **アクセス**許可が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee632-139">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="ee632-140">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-140">Select **Register**.</span></span>

<span data-ttu-id="ee632-141">**認証** > **プラットフォームの構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="ee632-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="ee632-142">`https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee632-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ee632-143">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="ee632-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="ee632-144">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="ee632-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ee632-145">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-145">Select the **Save** button.</span></span>

<span data-ttu-id="ee632-146">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="ee632-146">In **API permissions**:</span></span>

1. <span data-ttu-id="ee632-147">アプリに > ユーザー **Microsoft Graph**があることを確認**します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="ee632-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="ee632-148">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="ee632-149">**[名前]** 列から*サーバー API アプリ*を選択します (たとえば、 **Blazor server AAD B2C**)。</span><span class="sxs-lookup"><span data-stu-id="ee632-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="ee632-150">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="ee632-150">Open the **API** list.</span></span>
1. <span data-ttu-id="ee632-151">API へのアクセスを有効にします (たとえば、`API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="ee632-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="ee632-152">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="ee632-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="ee632-153">**[{テナント名} の管理者コンテンツを付与]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ee632-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="ee632-154">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="ee632-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="ee632-155">**ホーム** > の**Azure AD B2C** > **ユーザーフロー**:</span><span class="sxs-lookup"><span data-stu-id="ee632-155">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="ee632-156">サインアップとサインインのユーザーフローを作成する</span><span class="sxs-lookup"><span data-stu-id="ee632-156">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="ee632-157">少なくとも、 **Application claim** > **Display Name** user attribute を選択して、`LoginDisplay` コンポーネント (*Shared/logindisplay. razor*) に `context.User.Identity.Name` を設定します。</span><span class="sxs-lookup"><span data-stu-id="ee632-157">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="ee632-158">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="ee632-158">Record the following information:</span></span>

* <span data-ttu-id="ee632-159">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、`33333333-3333-3333-3333-333333333333`)。</span><span class="sxs-lookup"><span data-stu-id="ee632-159">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="ee632-160">アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (たとえば、`B2C_1_signupsignin`)。</span><span class="sxs-lookup"><span data-stu-id="ee632-160">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="ee632-161">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="ee632-161">Create the app</span></span>

<span data-ttu-id="ee632-162">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ee632-162">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="ee632-163">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="ee632-163">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ee632-164">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="ee632-164">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="ee632-165">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="ee632-165">Server app configuration</span></span>

<span data-ttu-id="ee632-166">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="ee632-166">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ee632-167">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="ee632-167">Authentication package</span></span>

<span data-ttu-id="ee632-168">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、`Microsoft.AspNetCore.Authentication.AzureAD.UI`によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="ee632-168">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="ee632-169">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="ee632-169">Authentication service support</span></span>

<span data-ttu-id="ee632-170">`AddAuthentication` メソッドは、アプリ内で認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="ee632-170">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="ee632-171">`AddAzureADBearer` メソッドは、Azure Active Directory によって出力されるトークンを検証するために必要な JWT ベアラーハンドラー内の特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="ee632-171">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="ee632-172">`UseAuthentication` と `UseAuthorization` 次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ee632-172">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="ee632-173">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="ee632-173">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="ee632-174">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="ee632-174">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="ee632-175">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="ee632-175">App settings</span></span>

<span data-ttu-id="ee632-176">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ee632-176">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="ee632-177">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="ee632-177">WeatherForecast controller</span></span>

<span data-ttu-id="ee632-178">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに `[Authorize]` 属性が適用された保護された API を公開します。</span><span class="sxs-lookup"><span data-stu-id="ee632-178">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="ee632-179">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="ee632-179">It's **important** to understand that:</span></span>

* <span data-ttu-id="ee632-180">この api コントローラーの `[Authorize]` 属性は、この API を承認されていないアクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="ee632-180">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="ee632-181">Blazor WebAssembly で使用される `[Authorize]` 属性は、アプリに対するヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ee632-181">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="ee632-182">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="ee632-182">Client app configuration</span></span>

<span data-ttu-id="ee632-183">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="ee632-183">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="ee632-184">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="ee632-184">Authentication package</span></span>

<span data-ttu-id="ee632-185">個々の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ee632-185">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="ee632-186">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="ee632-186">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ee632-187">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="ee632-187">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="ee632-188">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ee632-188">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="ee632-189">`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="ee632-189">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="ee632-190">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="ee632-190">Authentication service support</span></span>

<span data-ttu-id="ee632-191">ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="ee632-191">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="ee632-192">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="ee632-192">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ee632-193">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="ee632-193">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

<span data-ttu-id="ee632-194">`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="ee632-194">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ee632-195">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="ee632-195">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="ee632-196">Blazor WebAssembly テンプレートは、`dotnet new` コマンド (`{APP ID URI}/{DEFAULT SCOPE}`) に指定された既定のスコープに対して、セキュリティで保護された API のアクセストークンを要求するようにアプリを自動的に構成します。</span><span class="sxs-lookup"><span data-stu-id="ee632-196">The Blazor WebAssembly template automatically configures the app to request an access token for a secure API for the default scope provided to the `dotnet new` command (`{APP ID URI}/{DEFAULT SCOPE}`).</span></span>

<span data-ttu-id="ee632-197">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="ee632-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="ee632-198">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="ee632-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="ee632-199">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ee632-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="ee632-200">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="ee632-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="ee632-201">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ee632-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="ee632-202">Index ページ</span><span class="sxs-lookup"><span data-stu-id="ee632-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="ee632-203">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="ee632-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="ee632-204">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ee632-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="ee632-205">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ee632-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="ee632-206">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ee632-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="ee632-207">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ee632-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="ee632-208">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="ee632-208">Run the app</span></span>

<span data-ttu-id="ee632-209">サーバープロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ee632-209">Run the app from the Server project.</span></span> <span data-ttu-id="ee632-210">Visual Studio を使用する場合は、**ソリューションエクスプローラー**でサーバープロジェクトを選択し、ツールバーの **[実行]** ボタンを選択するか、 **[デバッグ]** メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="ee632-210">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ee632-211">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="ee632-211">Additional resources</span></span>

* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="ee632-212">チュートリアル: Azure Active Directory B2C テナントを作成する</span><span class="sxs-lookup"><span data-stu-id="ee632-212">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
