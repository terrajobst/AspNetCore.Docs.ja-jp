---
title: Azure アクティブBlazorディレクトリを使用してASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976998"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="000b4-102">Azure アクティブBlazorディレクトリを使用してASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="000b4-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="000b4-103">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="000b4-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="000b4-104">認証にBlazor [Azure アクティブ ディレクトリ (AAD) を](https://azure.microsoft.com/services/active-directory/)使用する Web アセンブリ スタンドアロン アプリを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="000b4-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="000b4-105">[AAD テナントと Web アプリケーションを作成](/azure/active-directory/develop/v2-overview)します。</span><span class="sxs-lookup"><span data-stu-id="000b4-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="000b4-106">Azure ポータルの Azure**アクティブ ディレクトリ** > **アプリ登録**領域に AAD アプリを登録します。</span><span class="sxs-lookup"><span data-stu-id="000b4-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="000b4-107">アプリの**名前**を指定します (たとえば、**Blazorクライアント AAD)。**</span><span class="sxs-lookup"><span data-stu-id="000b4-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="000b4-108">**[サポートされているアカウントの種類]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="000b4-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="000b4-109">**このエクスペリエンスのためだけに、この組織ディレクトリの [アカウント**] を選択できます。</span><span class="sxs-lookup"><span data-stu-id="000b4-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="000b4-110">[**リダイレクト URI]** ドロップダウン リストを **[Web]** に`https://localhost:5001/authentication/login-callback`設定したままにし、 のリダイレクト URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="000b4-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="000b4-111">[**アクセス許可** > **の付与管理者へのアクセス許可のオープンとoffline_accessのアクセス許可]** チェック ボックスを無効にします。</span><span class="sxs-lookup"><span data-stu-id="000b4-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="000b4-112">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="000b4-112">Select **Register**.</span></span>

<span data-ttu-id="000b4-113">**認証** > **プラットフォーム構成** > **Web**で:</span><span class="sxs-lookup"><span data-stu-id="000b4-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="000b4-114">リダイレクト**URI**が`https://localhost:5001/authentication/login-callback`存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="000b4-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="000b4-115">[**暗黙的な許可**] で、[**アクセス トークン**] および [ID トークン] のチェック ボックスをオン**にします**。</span><span class="sxs-lookup"><span data-stu-id="000b4-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="000b4-116">アプリの残りの既定値は、このエクスペリエンスに対して許容されます。</span><span class="sxs-lookup"><span data-stu-id="000b4-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="000b4-117">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="000b4-117">Select the **Save** button.</span></span>

<span data-ttu-id="000b4-118">以下の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="000b4-118">Record the following information:</span></span>

* <span data-ttu-id="000b4-119">アプリケーション ID (クライアント ID) `11111111-1111-1111-1111-111111111111`(例: )</span><span class="sxs-lookup"><span data-stu-id="000b4-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="000b4-120">ディレクトリ ID (テナント ID) `22222222-2222-2222-2222-222222222222`(たとえば)</span><span class="sxs-lookup"><span data-stu-id="000b4-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="000b4-121">次のコマンドのプレースホルダーを、前に記録された情報に置き換えて、コマンド シェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="000b4-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="000b4-122">プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。</span><span class="sxs-lookup"><span data-stu-id="000b4-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="000b4-123">フォルダー名もプロジェクト名の一部になります。</span><span class="sxs-lookup"><span data-stu-id="000b4-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="000b4-124">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="000b4-124">Authentication package</span></span>

<span data-ttu-id="000b4-125">職場または学校のアカウントを使用するアプリが作成されると`SingleOrg`、アプリは自動的に[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)( )`Microsoft.Authentication.WebAssembly.Msal`のパッケージ参照を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="000b4-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="000b4-126">パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="000b4-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="000b4-127">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="000b4-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="000b4-128">上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="000b4-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="000b4-129">パッケージ`Microsoft.Authentication.WebAssembly.Msal`は推移的にパッケージを`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="000b4-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="000b4-130">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="000b4-130">Authentication service support</span></span>

<span data-ttu-id="000b4-131">ユーザー認証のサポートは、パッケージによって提供される`AddMsalAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.Authentication.WebAssembly.Msal`登録されます。</span><span class="sxs-lookup"><span data-stu-id="000b4-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="000b4-132">このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="000b4-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="000b4-133">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="000b4-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="000b4-134">この`AddMsalAuthentication`メソッドは、アプリの認証に必要なパラメーターを構成するコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="000b4-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="000b4-135">アプリを登録するときに、Azure ポータル AAD 構成からアプリを構成するために必要な値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="000b4-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="000b4-136">アクセス トークン のスコープ</span><span class="sxs-lookup"><span data-stu-id="000b4-136">Access token scopes</span></span>

<span data-ttu-id="000b4-137">Blazor WebAssembly テンプレートは、セキュリティで保護された API のアクセス トークンを要求するようにアプリを自動的に構成しません。</span><span class="sxs-lookup"><span data-stu-id="000b4-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="000b4-138">トークンをサインイン フローの一部としてプロビジョニングするには、スコープを の既定のアクセス トークン スコープに追加`MsalProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="000b4-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="000b4-139">Azure Portal がスコープ URI を提供し、API から*401 未承認*の応答を受け取ったときに**アプリが未処理の例外をスロー**する場合は、スキームとホストを含まないスコープ URI を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="000b4-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="000b4-140">たとえば、Azure ポータルでは、次のいずれかのスコープ URI 形式を提供できます。</span><span class="sxs-lookup"><span data-stu-id="000b4-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="000b4-141">スキームとホストを使用せずにスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="000b4-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="000b4-142">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="000b4-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="000b4-143">ファイルをインポートする</span><span class="sxs-lookup"><span data-stu-id="000b4-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="000b4-144">Index ページ</span><span class="sxs-lookup"><span data-stu-id="000b4-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="000b4-145">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="000b4-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="000b4-146">コンポーネントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="000b4-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="000b4-147">ログインディスプレイコンポーネント</span><span class="sxs-lookup"><span data-stu-id="000b4-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="000b4-148">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="000b4-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="000b4-149">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="000b4-149">Additional resources</span></span>

* [<span data-ttu-id="000b4-150">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="000b4-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="000b4-151">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="000b4-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
