---
title: マイクロソフト アカウントをBlazor使用してASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977081"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="68088-102">マイクロソフト アカウントをBlazor使用してASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="68088-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="68088-103">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="68088-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="68088-104">認証にBlazor [Azure アクティブ ディレクトリ (AAD) を持つ Microsoft アカウント](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する WebAssembly スタンドアロン アプリを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="68088-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="68088-105">AAD テナントと Web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="68088-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="68088-106">Azure ポータルの Azure**アクティブ ディレクトリ** > **アプリ登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="68088-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="68088-107">1\.</span><span class="sxs-lookup"><span data-stu-id="68088-107">1\.</span></span> <span data-ttu-id="68088-108">アプリの**名前**を指定します (たとえば、**Blazorクライアント AAD)。**</span><span class="sxs-lookup"><span data-stu-id="68088-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="68088-109">2\.</span><span class="sxs-lookup"><span data-stu-id="68088-109">2\.</span></span> <span data-ttu-id="68088-110">[**サポートされるアカウントの種類**] で、[**組織ディレクトリのアカウント ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="68088-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="68088-111">3\.</span><span class="sxs-lookup"><span data-stu-id="68088-111">3\.</span></span> <span data-ttu-id="68088-112">[**リダイレクト URI]** ドロップダウン リストを **[Web]** に`https://localhost:5001/authentication/login-callback`設定したままにし、 のリダイレクト URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="68088-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="68088-113">4\.</span><span class="sxs-lookup"><span data-stu-id="68088-113">4\.</span></span> <span data-ttu-id="68088-114">[**アクセス許可** > **の付与管理者へのアクセス許可のオープンとoffline_accessのアクセス許可]** チェック ボックスを無効にします。</span><span class="sxs-lookup"><span data-stu-id="68088-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="68088-115">5\.</span><span class="sxs-lookup"><span data-stu-id="68088-115">5\.</span></span> <span data-ttu-id="68088-116">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="68088-116">Select **Register**.</span></span>

   <span data-ttu-id="68088-117">**認証** > **プラットフォーム構成** > **Web**で:</span><span class="sxs-lookup"><span data-stu-id="68088-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="68088-118">1\.</span><span class="sxs-lookup"><span data-stu-id="68088-118">1\.</span></span> <span data-ttu-id="68088-119">リダイレクト**URI**が`https://localhost:5001/authentication/login-callback`存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="68088-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="68088-120">2\.</span><span class="sxs-lookup"><span data-stu-id="68088-120">2\.</span></span> <span data-ttu-id="68088-121">[**暗黙的な許可**] で、[**アクセス トークン**] および [ID トークン] のチェック ボックスをオン**にします**。</span><span class="sxs-lookup"><span data-stu-id="68088-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="68088-122">3\.</span><span class="sxs-lookup"><span data-stu-id="68088-122">3\.</span></span> <span data-ttu-id="68088-123">アプリの残りの既定値は、このエクスペリエンスに対して許容されます。</span><span class="sxs-lookup"><span data-stu-id="68088-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="68088-124">4\.</span><span class="sxs-lookup"><span data-stu-id="68088-124">4\.</span></span> <span data-ttu-id="68088-125">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="68088-125">Select the **Save** button.</span></span>

   <span data-ttu-id="68088-126">アプリケーション ID (クライアント ID) (たとえば`11111111-1111-1111-1111-111111111111`) を記録します。</span><span class="sxs-lookup"><span data-stu-id="68088-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="68088-127">次のコマンドのプレースホルダーを、前に記録された情報に置き換えて、コマンド シェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="68088-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="68088-128">プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。</span><span class="sxs-lookup"><span data-stu-id="68088-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="68088-129">フォルダー名もプロジェクト名の一部になります。</span><span class="sxs-lookup"><span data-stu-id="68088-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="68088-130">アプリを作成したら、次の作業を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="68088-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="68088-131">Microsoft アカウントを使用してアプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="68088-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="68088-132">アプリを正しく構成した場合は、スタンドアロンBlazorアプリと同じ方法で Microsoft API のアクセス トークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="68088-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="68088-133">詳細については、「[クイック スタート : Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68088-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="68088-134">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="68088-134">Authentication package</span></span>

<span data-ttu-id="68088-135">職場または学校のアカウントを使用するアプリが作成されると`SingleOrg`、アプリは自動的に[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)( )`Microsoft.Authentication.WebAssembly.Msal`のパッケージ参照を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="68088-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="68088-136">パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="68088-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="68088-137">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="68088-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="68088-138">上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="68088-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="68088-139">パッケージ`Microsoft.Authentication.WebAssembly.Msal`は推移的にパッケージを`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="68088-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="68088-140">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="68088-140">Authentication service support</span></span>

<span data-ttu-id="68088-141">ユーザー認証のサポートは、パッケージによって提供される`AddMsalAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.Authentication.WebAssembly.Msal`登録されます。</span><span class="sxs-lookup"><span data-stu-id="68088-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="68088-142">このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="68088-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="68088-143">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="68088-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="68088-144">この`AddMsalAuthentication`メソッドは、アプリの認証に必要なパラメーターを構成するコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="68088-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="68088-145">アプリを登録するときに、Microsoft アカウント構成からアプリを構成するために必要な値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="68088-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="68088-146">アクセス トークン のスコープ</span><span class="sxs-lookup"><span data-stu-id="68088-146">Access token scopes</span></span>

<span data-ttu-id="68088-147">Blazor WebAssembly テンプレートは、セキュリティで保護された API のアクセス トークンを要求するようにアプリを自動的に構成しません。</span><span class="sxs-lookup"><span data-stu-id="68088-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="68088-148">トークンをサインイン フローの一部としてプロビジョニングするには、スコープを の既定のアクセス トークン スコープに追加`MsalProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="68088-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="68088-149">Azure Portal がスコープ URI を提供し、API から*401 未承認*の応答を受け取ったときに**アプリが未処理の例外をスロー**する場合は、スキームとホストを含まないスコープ URI を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="68088-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="68088-150">たとえば、Azure ポータルでは、次のいずれかのスコープ URI 形式を提供できます。</span><span class="sxs-lookup"><span data-stu-id="68088-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="68088-151">スキームとホストを使用せずにスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="68088-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="68088-152">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68088-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="68088-153">ファイルをインポートする</span><span class="sxs-lookup"><span data-stu-id="68088-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="68088-154">Index ページ</span><span class="sxs-lookup"><span data-stu-id="68088-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="68088-155">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="68088-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="68088-156">コンポーネントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="68088-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="68088-157">ログインディスプレイコンポーネント</span><span class="sxs-lookup"><span data-stu-id="68088-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="68088-158">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="68088-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="68088-159">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="68088-159">Additional resources</span></span>

* [<span data-ttu-id="68088-160">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="68088-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="68088-161">クイック スタート: アプリケーションを Microsoft ID プラットフォームに登録する</span><span class="sxs-lookup"><span data-stu-id="68088-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="68088-162">クイック スタート: Web API を公開するようにアプリケーションを構成する</span><span class="sxs-lookup"><span data-stu-id="68088-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
