---
title: Azure アクティブBlazorディレクトリ B2C を使用して、ASP.NETコア Web アセンブリホスト アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 4c79f7530e18b9f70262812a64abb55122701d15
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977159"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="7487e-102">Azure アクティブBlazorディレクトリ B2C を使用して、ASP.NETコア Web アセンブリホスト アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="7487e-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="7487e-103">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7487e-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="7487e-104">この記事では、認証に Azure Blazor [Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用する WebAssembly スタンドアロン アプリを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="7487e-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="7487e-105">AAD B2C でアプリを登録してソリューションを作成する</span><span class="sxs-lookup"><span data-stu-id="7487e-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="7487e-106">テナントの作成</span><span class="sxs-lookup"><span data-stu-id="7487e-106">Create a tenant</span></span>

<span data-ttu-id="7487e-107">チュートリアル: AAD [B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)し、次の情報を記録する Azure Active Directory B2C テナントを作成するのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="7487e-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="7487e-108">AAD B2C インスタンス (`https://contoso.b2clogin.com/`末尾のスラッシュを含むなど)</span><span class="sxs-lookup"><span data-stu-id="7487e-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="7487e-109">AAD B2C テナント ドメイン`contoso.onmicrosoft.com`(たとえば)</span><span class="sxs-lookup"><span data-stu-id="7487e-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="7487e-110">サーバー API アプリの登録</span><span class="sxs-lookup"><span data-stu-id="7487e-110">Register a server API app</span></span>

<span data-ttu-id="7487e-111">[チュートリアル: Azure の Azure のポータル](/azure/active-directory-b2c/tutorial-register-applications)の Azure アクティブ ディレクトリ**アプリ登録**領域でサーバー API アプリの AAD*アプリ*を登録する Azure Active **Directory** > B2C でアプリケーションを登録するのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="7487e-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="7487e-112">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-112">Select **New registration**.</span></span>
1. <span data-ttu-id="7487e-113">アプリの**名前**を指定します (たとえば、**Blazorサーバー AAD B2C)。**</span><span class="sxs-lookup"><span data-stu-id="7487e-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="7487e-114">[**サポートされているアカウントの種類]** で **、[組織ディレクトリ内のアカウント] または任意の ID プロバイダを選択します。Azure AD B2C を使用してユーザーを認証する場合。**</span><span class="sxs-lookup"><span data-stu-id="7487e-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="7487e-115">(マルチテナント) このエクスペリエンスの場合。</span><span class="sxs-lookup"><span data-stu-id="7487e-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="7487e-116">このシナリオでは *、Server API アプリ*は**リダイレクト URI**を必要としないので、ドロップ ダウンセットを**Web**に設定したままにして、リダイレクト URI を入力しないでください。</span><span class="sxs-lookup"><span data-stu-id="7487e-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="7487e-117">**[権限** > **を付与する管理者に、オープンに対する権限の追加とoffline_accessのアクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7487e-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="7487e-118">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-118">Select **Register**.</span></span>

<span data-ttu-id="7487e-119">公開**API :**</span><span class="sxs-lookup"><span data-stu-id="7487e-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="7487e-120">**[Scope の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="7487e-121">**[Save and continue]\(保存して続行\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="7487e-122">**スコープ名**(たとえば)`API.Access`を指定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7487e-123">**管理者の同意**表示名を指定します (たとえば`Access API`)。</span><span class="sxs-lookup"><span data-stu-id="7487e-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="7487e-124">**管理者の同意の説明**(たとえば)`Allows the app to access server app API endpoints.`を入力します。</span><span class="sxs-lookup"><span data-stu-id="7487e-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="7487e-125">**[状態**] が **[有効]** に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7487e-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="7487e-126">**[スコープの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-126">Select **Add scope**.</span></span>

<span data-ttu-id="7487e-127">以下の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="7487e-127">Record the following information:</span></span>

* <span data-ttu-id="7487e-128">*サーバー API アプリ*アプリケーション ID (クライアント ID) `11111111-1111-1111-1111-111111111111`(例: )</span><span class="sxs-lookup"><span data-stu-id="7487e-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="7487e-129">アプリ ID URI (、、、または指定したカスタム値など`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111``api://11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="7487e-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="7487e-130">ディレクトリ ID (テナント ID) `222222222-2222-2222-2222-222222222222`(たとえば)</span><span class="sxs-lookup"><span data-stu-id="7487e-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="7487e-131">*サーバー API アプリ*アプリ ID URI (`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`たとえば、Azure ポータルの値がクライアント ID に既定値になる場合など)</span><span class="sxs-lookup"><span data-stu-id="7487e-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="7487e-132">既定のスコープ (たとえば`API.Access`)</span><span class="sxs-lookup"><span data-stu-id="7487e-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="7487e-133">クライアント アプリを登録する</span><span class="sxs-lookup"><span data-stu-id="7487e-133">Register a client app</span></span>

<span data-ttu-id="7487e-134">[チュートリアル: Azure の Azure のポータル](/azure/active-directory-b2c/tutorial-register-applications)の Azure アクティブ ディレクトリ**アプリ登録**領域でクライアント*アプリ*の AAD アプリを登録するもう一度 Azure Active **Directory** > B2C でアプリケーションを登録するのガイダンスに従います。</span><span class="sxs-lookup"><span data-stu-id="7487e-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="7487e-135">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-135">Select **New registration**.</span></span>
1. <span data-ttu-id="7487e-136">アプリの**名前**を指定します (たとえば、**Blazorクライアント AAD B2C)。**</span><span class="sxs-lookup"><span data-stu-id="7487e-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="7487e-137">[**サポートされているアカウントの種類]** で **、[組織ディレクトリ内のアカウント] または任意の ID プロバイダを選択します。Azure AD B2C を使用してユーザーを認証する場合。**</span><span class="sxs-lookup"><span data-stu-id="7487e-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="7487e-138">(マルチテナント) このエクスペリエンスの場合。</span><span class="sxs-lookup"><span data-stu-id="7487e-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="7487e-139">[**リダイレクト URI]** ドロップダウン リストを **[Web]** に`https://localhost:5001/authentication/login-callback`設定したままにし、 のリダイレクト URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="7487e-140">**[権限** > **を付与する管理者に、オープンに対する権限の追加とoffline_accessのアクセス許可**が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7487e-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="7487e-141">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-141">Select **Register**.</span></span>

<span data-ttu-id="7487e-142">**認証** > **プラットフォーム構成** > **Web**で:</span><span class="sxs-lookup"><span data-stu-id="7487e-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7487e-143">リダイレクト**URI**が`https://localhost:5001/authentication/login-callback`存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="7487e-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7487e-144">[**暗黙的な許可**] で、[**アクセス トークン**] および [ID トークン] のチェック ボックスをオン**にします**。</span><span class="sxs-lookup"><span data-stu-id="7487e-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7487e-145">アプリの残りの既定値は、このエクスペリエンスに対して許容されます。</span><span class="sxs-lookup"><span data-stu-id="7487e-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7487e-146">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-146">Select the **Save** button.</span></span>

<span data-ttu-id="7487e-147">**API 権限**で:</span><span class="sxs-lookup"><span data-stu-id="7487e-147">In **API permissions**:</span></span>

1. <span data-ttu-id="7487e-148">アプリに**グラフ** > **ユーザーの読み取り**アクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7487e-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="7487e-149">[**アクセス許可の追加**とそれに続く**マイ API] の**選択を行います。</span><span class="sxs-lookup"><span data-stu-id="7487e-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="7487e-150">**[名前**] 列から *[サーバー API] アプリ*を選択します (**Blazorたとえば、サーバー AAD B2C)。**</span><span class="sxs-lookup"><span data-stu-id="7487e-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="7487e-151">**API**リストを開きます。</span><span class="sxs-lookup"><span data-stu-id="7487e-151">Open the **API** list.</span></span>
1. <span data-ttu-id="7487e-152">API へのアクセスを有効にします`API.Access`(たとえば、 )。</span><span class="sxs-lookup"><span data-stu-id="7487e-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7487e-153">**[アクセス許可の追加]** を選択します.</span><span class="sxs-lookup"><span data-stu-id="7487e-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="7487e-154">[{**テナント名} の管理者コンテンツを付与**] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="7487e-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="7487e-155">**[はい]** を選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="7487e-156">**ホーム** > **Azure AD B2C** > **ユーザー フロー**:</span><span class="sxs-lookup"><span data-stu-id="7487e-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="7487e-157">サインアップとサインイン ユーザー フローを作成する</span><span class="sxs-lookup"><span data-stu-id="7487e-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="7487e-158">少なくとも、**アプリケーション クレーム** > **の表示名**ユーザー属性を選択して`context.User.Identity.Name``LoginDisplay`、コンポーネント (*Shared/LoginDisplay.razor*) に設定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="7487e-159">以下の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="7487e-159">Record the following information:</span></span>

* <span data-ttu-id="7487e-160">クライアント*アプリケーション*アプリケーション ID (クライアント ID) `33333333-3333-3333-3333-333333333333`(たとえば) を記録します。</span><span class="sxs-lookup"><span data-stu-id="7487e-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="7487e-161">アプリ用に作成されたサインアップユーザーおよびサインイン ユーザー のフロー名 (など`B2C_1_signupsignin`) を記録します。</span><span class="sxs-lookup"><span data-stu-id="7487e-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="7487e-162">アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="7487e-162">Create the app</span></span>

<span data-ttu-id="7487e-163">次のコマンドのプレースホルダーを、前に記録された情報に置き換えて、コマンド シェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7487e-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="7487e-164">プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。</span><span class="sxs-lookup"><span data-stu-id="7487e-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7487e-165">フォルダー名もプロジェクト名の一部になります。</span><span class="sxs-lookup"><span data-stu-id="7487e-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7487e-166">アプリ ID URI を`app-id-uri`オプションに渡しますが、クライアント アプリで構成の変更が必要になる場合があります([アクセス トークンのスコープ](#access-token-scopes)セクションで説明します)。</span><span class="sxs-lookup"><span data-stu-id="7487e-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7487e-167">サーバー アプリの構成</span><span class="sxs-lookup"><span data-stu-id="7487e-167">Server app configuration</span></span>

<span data-ttu-id="7487e-168">*このセクションは、ソリューションの**サーバー**アプリに関連します。*</span><span class="sxs-lookup"><span data-stu-id="7487e-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7487e-169">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="7487e-169">Authentication package</span></span>

<span data-ttu-id="7487e-170">ASP.NETコア Web API への呼び出しの認証と承認の`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`サポートは、 によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="7487e-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="7487e-171">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="7487e-171">Authentication service support</span></span>

<span data-ttu-id="7487e-172">この`AddAuthentication`メソッドは、アプリ内で認証サービスを設定し、JWT Bearer ハンドラーを既定の認証方法として構成します。</span><span class="sxs-lookup"><span data-stu-id="7487e-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="7487e-173">この`AddAzureADB2CBearer`メソッドは、Azure Active Directory B2C によって生成されたトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="7487e-174">`UseAuthentication`を`UseAuthorization`確認し、次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="7487e-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="7487e-175">アプリは、受信要求でトークンを解析し、検証しようとします。</span><span class="sxs-lookup"><span data-stu-id="7487e-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="7487e-176">適切な資格情報を使用せずに保護されたリソースにアクセスしようとする要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="7487e-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="7487e-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="7487e-177">User.Identity.Name</span></span>

<span data-ttu-id="7487e-178">デフォルトでは、`User.Identity.Name`は設定されません。</span><span class="sxs-lookup"><span data-stu-id="7487e-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="7487e-179">要求の種類から値を受け取るように`name`アプリを構成するには、in の[トークン検証パラメーター.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>構成`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="7487e-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="7487e-180">アプリケーション設定</span><span class="sxs-lookup"><span data-stu-id="7487e-180">App settings</span></span>

<span data-ttu-id="7487e-181">*appsettings.json*ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7487e-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="7487e-182">天気予報コントローラ</span><span class="sxs-lookup"><span data-stu-id="7487e-182">WeatherForecast controller</span></span>

<span data-ttu-id="7487e-183">天気予報コントローラ (*コントローラ/天気予報コントローラ.cs)* は、コントローラに適用された`[Authorize]`属性を持つ保護された API を公開します。</span><span class="sxs-lookup"><span data-stu-id="7487e-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="7487e-184">次の点を理解することが**重要**です。</span><span class="sxs-lookup"><span data-stu-id="7487e-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="7487e-185">この`[Authorize]`API コントローラの属性は、この API を不正アクセスから保護する唯一のものです。</span><span class="sxs-lookup"><span data-stu-id="7487e-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="7487e-186">WebAssembly アプリでBlazor使用される属性は`[Authorize]`、アプリが正しく動作するためにユーザーに承認される必要があることを示すヒントとしてのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="7487e-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="7487e-187">クライアント アプリの構成</span><span class="sxs-lookup"><span data-stu-id="7487e-187">Client app configuration</span></span>

<span data-ttu-id="7487e-188">*このセクションは、ソリューションの**クライアント**アプリに関連します。*</span><span class="sxs-lookup"><span data-stu-id="7487e-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7487e-189">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="7487e-189">Authentication package</span></span>

<span data-ttu-id="7487e-190">個々の B2C アカウント (`IndividualB2C`) を使用するアプリが作成されると、アプリは自動的に Microsoft[認証ライブラリ](/azure/active-directory/develop/msal-overview)( )`Microsoft.Authentication.WebAssembly.Msal`のパッケージ参照を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7487e-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="7487e-191">パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="7487e-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7487e-192">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="7487e-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="7487e-193">上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7487e-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="7487e-194">パッケージ`Microsoft.Authentication.WebAssembly.Msal`は推移的にパッケージを`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="7487e-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="7487e-195">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="7487e-195">Authentication service support</span></span>

<span data-ttu-id="7487e-196">ユーザー認証のサポートは、パッケージによって提供される`AddMsalAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.Authentication.WebAssembly.Msal`登録されます。</span><span class="sxs-lookup"><span data-stu-id="7487e-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="7487e-197">このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7487e-198">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="7487e-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="7487e-199">この`AddMsalAuthentication`メソッドは、アプリの認証に必要なパラメーターを構成するコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="7487e-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7487e-200">アプリを登録するときに、Azure ポータル AAD 構成からアプリを構成するために必要な値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="7487e-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="7487e-201">アクセス トークン のスコープ</span><span class="sxs-lookup"><span data-stu-id="7487e-201">Access token scopes</span></span>

<span data-ttu-id="7487e-202">既定のアクセス トークン スコープは、次のアクセス トークン スコープのリストを表します。</span><span class="sxs-lookup"><span data-stu-id="7487e-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="7487e-203">既定では、サインイン要求に含まれます。</span><span class="sxs-lookup"><span data-stu-id="7487e-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="7487e-204">認証後すぐにアクセス トークンをプロビジョニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7487e-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="7487e-205">すべてのスコープは、Azure アクティブ ディレクトリ ルールごとに同じアプリに属している必要があります。</span><span class="sxs-lookup"><span data-stu-id="7487e-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="7487e-206">必要に応じて、追加の API アプリにスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7487e-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="7487e-207">Azure Portal がスコープ URI を提供し、API から*401 未承認*の応答を受け取ったときに**アプリが未処理の例外をスロー**する場合は、スキームとホストを含まないスコープ URI を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="7487e-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="7487e-208">たとえば、Azure ポータルでは、次のいずれかのスコープ URI 形式を提供できます。</span><span class="sxs-lookup"><span data-stu-id="7487e-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="7487e-209">スキームとホストを使用せずにスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="7487e-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="7487e-210">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7487e-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="7487e-211">ファイルをインポートする</span><span class="sxs-lookup"><span data-stu-id="7487e-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7487e-212">Index ページ</span><span class="sxs-lookup"><span data-stu-id="7487e-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="7487e-213">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7487e-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7487e-214">コンポーネントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="7487e-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7487e-215">ログインディスプレイコンポーネント</span><span class="sxs-lookup"><span data-stu-id="7487e-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="7487e-216">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="7487e-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7487e-217">コンポーネントをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="7487e-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7487e-218">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="7487e-218">Run the app</span></span>

<span data-ttu-id="7487e-219">サーバー プロジェクトからアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="7487e-219">Run the app from the Server project.</span></span> <span data-ttu-id="7487e-220">Visual Studio を使用する場合は、**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="7487e-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->
[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7487e-221">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7487e-221">Additional resources</span></span>

* [<span data-ttu-id="7487e-222">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="7487e-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="7487e-223">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="7487e-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="7487e-224">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="7487e-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
