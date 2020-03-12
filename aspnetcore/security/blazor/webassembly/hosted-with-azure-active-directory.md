---
title: Azure Active Directory を使用して、ASP.NET Core Blazor Webashosted アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0803e436d66ef7df3c68739e674a8652fde11166
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083663"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="8d721-102">Azure Active Directory を使用して、ASP.NET Core Blazor Webashosted アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="8d721-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="8d721-103">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8d721-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



<span data-ttu-id="8d721-104">この記事では、認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、 [Blazor webassembly ホスト型アプリ](xref:blazor/hosting-models#blazor-webassembly)を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8d721-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="8d721-105">AAD B2C でのアプリの登録とソリューションの作成</span><span class="sxs-lookup"><span data-stu-id="8d721-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="8d721-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="8d721-106">Create a tenant</span></span>

<span data-ttu-id="8d721-107">[「クイックスタート: テナントを設定](/azure/active-directory/develop/quickstart-create-new-tenant)する」のガイダンスに従って、AAD でテナントを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d721-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="8d721-108">サーバー API アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="8d721-108">Register a server API app</span></span>

<span data-ttu-id="8d721-109">[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*サーバー API アプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="8d721-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="8d721-110">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-110">Select **New registration**.</span></span>
1. <span data-ttu-id="8d721-111">アプリの**名前**を指定します ( **Blazor Server AAD**など)。</span><span class="sxs-lookup"><span data-stu-id="8d721-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="8d721-112">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="8d721-113">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="8d721-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="8d721-114">このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは **[Web]** に設定し、リダイレクト uri は入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="8d721-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="8d721-115">アクセス\*\*許可 > \*\* **openid and offline_access permissions に管理者求めるプロンプトを付与する** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="8d721-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="8d721-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-116">Select **Register**.</span></span>

<span data-ttu-id="8d721-117">**[API のアクセス許可]** で、アプリがサインインまたはプロファイルへのアクセスを必要としないため、 **Microsoft Graph** > [読み取り] アクセス許可を削除**します。**</span><span class="sxs-lookup"><span data-stu-id="8d721-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="8d721-118">で**API を公開**します。</span><span class="sxs-lookup"><span data-stu-id="8d721-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="8d721-119">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="8d721-120">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="8d721-121">**スコープ名**を指定します (たとえば、`API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="8d721-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="8d721-122">**管理者の同意の表示名**を指定します (たとえば、`Access API`)。</span><span class="sxs-lookup"><span data-stu-id="8d721-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="8d721-123">**管理者の同意の説明**(`Allows the app to access server app API endpoints.`など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="8d721-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="8d721-124">**状態**が**有効**に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8d721-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="8d721-125">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-125">Select **Add scope**.</span></span>

<span data-ttu-id="8d721-126">次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="8d721-126">Record the following information:</span></span>

* <span data-ttu-id="8d721-127">*サーバー API アプリ*アプリケーション ID (クライアント ID) (たとえば、`11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="8d721-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="8d721-128">ディレクトリ ID (テナント ID) (`222222222-2222-2222-2222-222222222222`など)</span><span class="sxs-lookup"><span data-stu-id="8d721-128">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="8d721-129">AAD テナントドメイン (`contoso.onmicrosoft.com`など)</span><span class="sxs-lookup"><span data-stu-id="8d721-129">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="8d721-130">既定のスコープ (たとえば、`API.Access`)</span><span class="sxs-lookup"><span data-stu-id="8d721-130">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="8d721-131">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="8d721-131">Register a client app</span></span>

<span data-ttu-id="8d721-132">[「クイックスタート: アプリケーションを Microsoft identity platform に登録](/azure/active-directory/develop/quickstart-register-app)する」およびそれ以降の Azure AAD のトピックのガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*クライアントアプリ*の AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="8d721-132">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="8d721-133">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-133">Select **New registration**.</span></span>
1. <span data-ttu-id="8d721-134">アプリの**名前**を指定します ( **Blazor クライアント AAD**など)。</span><span class="sxs-lookup"><span data-stu-id="8d721-134">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="8d721-135">**サポートされているアカウントの種類**を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-135">Choose a **Supported account types**.</span></span> <span data-ttu-id="8d721-136">このエクスペリエンスのために、[**この組織ディレクトリのみ**(シングルテナント)] でアカウントを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="8d721-136">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="8d721-137">**[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。</span><span class="sxs-lookup"><span data-stu-id="8d721-137">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="8d721-138">アクセス\*\*許可 > \*\* **openid and offline_access permissions に管理者求めるプロンプトを付与する** チェックボックスをオフにします。</span><span class="sxs-lookup"><span data-stu-id="8d721-138">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="8d721-139">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-139">Select **Register**.</span></span>

<span data-ttu-id="8d721-140">**認証** > **プラットフォームの構成** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="8d721-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="8d721-141">`https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="8d721-141">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="8d721-142">**暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="8d721-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="8d721-143">アプリの残りの既定値は、このエクスペリエンスで許容されます。</span><span class="sxs-lookup"><span data-stu-id="8d721-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="8d721-144">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-144">Select the **Save** button.</span></span>

<span data-ttu-id="8d721-145">**API のアクセス許可**:</span><span class="sxs-lookup"><span data-stu-id="8d721-145">In **API permissions**:</span></span>

1. <span data-ttu-id="8d721-146">アプリに > ユーザー **Microsoft Graph**があることを確認**します。読み取り**アクセス許可。</span><span class="sxs-lookup"><span data-stu-id="8d721-146">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="8d721-147">[**アクセス許可の追加]、** **[api**の追加] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-147">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="8d721-148">**[名前]** 列から*サーバー API アプリ*を選択します ( **Blazor server AAD**など)。</span><span class="sxs-lookup"><span data-stu-id="8d721-148">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="8d721-149">**API**の一覧を開きます。</span><span class="sxs-lookup"><span data-stu-id="8d721-149">Open the **API** list.</span></span>
1. <span data-ttu-id="8d721-150">API へのアクセスを有効にします (たとえば、`API.Access`)。</span><span class="sxs-lookup"><span data-stu-id="8d721-150">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="8d721-151">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="8d721-151">Select **Add permissions**.</span></span>
1. <span data-ttu-id="8d721-152">**[{テナント名} の管理者コンテンツを付与]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8d721-152">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="8d721-153">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="8d721-153">Select **Yes** to confirm.</span></span>

<span data-ttu-id="8d721-154">*クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、`33333333-3333-3333-3333-333333333333`)。</span><span class="sxs-lookup"><span data-stu-id="8d721-154">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="8d721-155">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="8d721-155">Create the app</span></span>

<span data-ttu-id="8d721-156">次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8d721-156">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="8d721-157">プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。</span><span class="sxs-lookup"><span data-stu-id="8d721-157">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="8d721-158">フォルダー名もプロジェクトの名前の一部になります。</span><span class="sxs-lookup"><span data-stu-id="8d721-158">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="8d721-159">既定のアクセストークンスコープに対する構成の重要な変更については、「[認証サービスのサポート](#Authentication service support)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d721-159">See the [Authentication service support](#Authentication service support) section for an important configuration change to the default access token scope.</span></span> <span data-ttu-id="8d721-160">Blazor WebAssembly テンプレートによって提供される値は、テンプレートから*クライアントアプリ*を作成した後で手動で変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d721-160">The value provided by the Blazor WebAssembly template must be manually changed after the *Client app* is created from the template.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="8d721-161">サーバーアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8d721-161">Server app configuration</span></span>

<span data-ttu-id="8d721-162">*このセクションは、ソリューションの**サーバー**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="8d721-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="8d721-163">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="8d721-163">Authentication package</span></span>

<span data-ttu-id="8d721-164">ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、`Microsoft.AspNetCore.Authentication.AzureAD.UI`によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="8d721-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="8d721-165">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="8d721-165">Authentication service support</span></span>

<span data-ttu-id="8d721-166">`AddAuthentication` メソッドは、アプリ内で認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="8d721-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="8d721-167">`AddAzureADBearer` メソッドは、Azure Active Directory によって出力されるトークンを検証するために必要な JWT ベアラーハンドラー内の特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="8d721-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="8d721-168">`UseAuthentication` と `UseAuthorization` 次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8d721-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="8d721-169">アプリは、受信要求のトークンの解析と検証を試みます。</span><span class="sxs-lookup"><span data-stu-id="8d721-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="8d721-170">適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d721-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a><span data-ttu-id="8d721-171">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="8d721-171">App settings</span></span>

<span data-ttu-id="8d721-172">*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8d721-172">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="8d721-173">WeatherForecast コントローラー</span><span class="sxs-lookup"><span data-stu-id="8d721-173">WeatherForecast controller</span></span>

<span data-ttu-id="8d721-174">WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに `[Authorize]` 属性が適用された保護された API を公開します。</span><span class="sxs-lookup"><span data-stu-id="8d721-174">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="8d721-175">次の**点**を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="8d721-175">It's **important** to understand that:</span></span>

* <span data-ttu-id="8d721-176">この api コントローラーの `[Authorize]` 属性は、この API を承認されていないアクセスから保護する唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="8d721-176">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="8d721-177">Blazor WebAssembly で使用される `[Authorize]` 属性は、アプリに対するヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d721-177">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="8d721-178">クライアントアプリの構成</span><span class="sxs-lookup"><span data-stu-id="8d721-178">Client app configuration</span></span>

<span data-ttu-id="8d721-179">*このセクションは、ソリューションの**クライアント**アプリに関連しています。*</span><span class="sxs-lookup"><span data-stu-id="8d721-179">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="8d721-180">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="8d721-180">Authentication package</span></span>

<span data-ttu-id="8d721-181">職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8d721-181">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="8d721-182">このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="8d721-182">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8d721-183">アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="8d721-183">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="8d721-184">前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8d721-184">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="8d721-185">`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="8d721-185">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="8d721-186">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="8d721-186">Authentication service support</span></span>

<span data-ttu-id="8d721-187">ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="8d721-187">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="8d721-188">このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="8d721-188">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8d721-189">*Program.cs*</span><span class="sxs-lookup"><span data-stu-id="8d721-189">*Program.cs*:</span></span>

<span data-ttu-id="8d721-190">*クライアントアプリ*が生成されると、既定のアクセストークンのスコープは `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`の形式になります。</span><span class="sxs-lookup"><span data-stu-id="8d721-190">When the *Client app* is generated, the default access token scope is of the format `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`.</span></span> <span data-ttu-id="8d721-191">**スコープ値の `api://` の部分を削除します。**</span><span class="sxs-lookup"><span data-stu-id="8d721-191">**Remove the `api://` portion of the scope value.**</span></span> <span data-ttu-id="8d721-192">この問題は、今後のプレビューリリースで解決されます。</span><span class="sxs-lookup"><span data-stu-id="8d721-192">This issue will be addressed in a future preview release.</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> <span data-ttu-id="8d721-193">既定のアクセストークンのスコープは、`{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (`11111111-1111-1111-1111-111111111111/API.Access`など) の形式である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d721-193">The default access token scope must be in the format `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (for example, `11111111-1111-1111-1111-111111111111/API.Access`).</span></span> <span data-ttu-id="8d721-194">スキームまたはスキームとホストがスコープ設定に提供されている場合 (Azure Portal に示されているように)、*サーバー API アプリ*から承認されて*いない 401*の応答を受信すると、*クライアントアプリ*は未処理の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="8d721-194">If a scheme or scheme and host is provided to the scope setting (as shown in the Azure Portal), the *Client app* throws an unhandled exception when it receives a *401 Unauthorized* response from the *Server API app*.</span></span>

<span data-ttu-id="8d721-195">`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。</span><span class="sxs-lookup"><span data-stu-id="8d721-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="8d721-196">アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。</span><span class="sxs-lookup"><span data-stu-id="8d721-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="8d721-197">既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。</span><span class="sxs-lookup"><span data-stu-id="8d721-197">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="8d721-198">サインイン要求に既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="8d721-198">Included by default in the sign in request.</span></span>
* <span data-ttu-id="8d721-199">認証直後にアクセストークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d721-199">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="8d721-200">すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d721-200">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="8d721-201">必要に応じて追加の API アプリ用に追加のスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8d721-201">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a><span data-ttu-id="8d721-202">Index ページ</span><span class="sxs-lookup"><span data-stu-id="8d721-202">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a><span data-ttu-id="8d721-203">アプリコンポーネント</span><span class="sxs-lookup"><span data-stu-id="8d721-203">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="8d721-204">RedirectToLogin コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8d721-204">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="8d721-205">LoginDisplay コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8d721-205">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="8d721-206">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8d721-206">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="8d721-207">FetchData コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8d721-207">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8d721-208">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8d721-208">Additional resources</span></span>

* <xref:security/authentication/azure-active-directory/index>
