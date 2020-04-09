---
title: Azure のアクティブBlazorディレクトリ B2C を使用して、ASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977055"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a9d52-102">Azure のアクティブBlazorディレクトリ B2C を使用して、ASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する</span><span class="sxs-lookup"><span data-stu-id="a9d52-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a9d52-103">[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a9d52-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a9d52-104">認証にBlazor [Azure アクティブ ディレクトリ (AAD) B2C を](/azure/active-directory-b2c/overview)使用する Web アセンブリ スタンドアロン アプリを作成するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="a9d52-105">テナントを作成し、Azure ポータルで Web アプリを登録するには、次のトピックのガイダンスに従います。</span><span class="sxs-lookup"><span data-stu-id="a9d52-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="a9d52-106">[AAD B2C テナント](/azure/active-directory-b2c/tutorial-create-tenant)&ndash;の作成 次の情報を記録します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="a9d52-107">1\.</span><span class="sxs-lookup"><span data-stu-id="a9d52-107">1\.</span></span> <span data-ttu-id="a9d52-108">AAD B2C インスタンス (`https://contoso.b2clogin.com/`末尾のスラッシュを含むなど)</span><span class="sxs-lookup"><span data-stu-id="a9d52-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="a9d52-109">2\.</span><span class="sxs-lookup"><span data-stu-id="a9d52-109">2\.</span></span> <span data-ttu-id="a9d52-110">AAD B2C テナント ドメイン`contoso.onmicrosoft.com`(たとえば)</span><span class="sxs-lookup"><span data-stu-id="a9d52-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="a9d52-111">[Web アプリケーション](/azure/active-directory-b2c/tutorial-register-applications)&ndash;の登録 アプリの登録時に次の項目を選択します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="a9d52-112">1\.</span><span class="sxs-lookup"><span data-stu-id="a9d52-112">1\.</span></span> <span data-ttu-id="a9d52-113">**[Web アプリ/ Web API] を** **[はい]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="a9d52-114">2\.</span><span class="sxs-lookup"><span data-stu-id="a9d52-114">2\.</span></span> <span data-ttu-id="a9d52-115">**[暗黙的なフローを許可する]** を **[はい**] に設定します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="a9d52-116">3\.</span><span class="sxs-lookup"><span data-stu-id="a9d52-116">3\.</span></span> <span data-ttu-id="a9d52-117">**の返信 URL** `https://localhost:5001/authentication/login-callback`を追加します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="a9d52-118">アプリケーション ID (クライアント ID) (たとえば`11111111-1111-1111-1111-111111111111`) を記録します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="a9d52-119">[ユーザー フロー](/azure/active-directory-b2c/tutorial-create-user-flows)&ndash;の作成 サインアップとサインイン ユーザー フローを作成します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="a9d52-120">少なくとも、**アプリケーション クレーム** > **の表示名**ユーザー属性を選択して`context.User.Identity.Name``LoginDisplay`、コンポーネント (*Shared/LoginDisplay.razor*) に設定します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="a9d52-121">アプリ用に作成されたサインアップユーザーおよびサインイン ユーザー のフロー名 (など`B2C_1_signupsignin`) を記録します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="a9d52-122">次のコマンドのプレースホルダーを、前に記録された情報に置き換えて、コマンド シェルでコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="a9d52-123">プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。</span><span class="sxs-lookup"><span data-stu-id="a9d52-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a9d52-124">フォルダー名もプロジェクト名の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a9d52-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a9d52-125">認証パッケージ</span><span class="sxs-lookup"><span data-stu-id="a9d52-125">Authentication package</span></span>

<span data-ttu-id="a9d52-126">個々の B2C アカウント (`IndividualB2C`) を使用するアプリが作成されると、アプリは自動的に Microsoft[認証ライブラリ](/azure/active-directory/develop/msal-overview)( )`Microsoft.Authentication.WebAssembly.Msal`のパッケージ参照を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a9d52-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a9d52-127">パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。</span><span class="sxs-lookup"><span data-stu-id="a9d52-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a9d52-128">アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a9d52-129">上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a9d52-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a9d52-130">パッケージ`Microsoft.Authentication.WebAssembly.Msal`は推移的にパッケージを`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a9d52-131">認証サービスのサポート</span><span class="sxs-lookup"><span data-stu-id="a9d52-131">Authentication service support</span></span>

<span data-ttu-id="a9d52-132">ユーザー認証のサポートは、パッケージによって提供される`AddMsalAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.Authentication.WebAssembly.Msal`登録されます。</span><span class="sxs-lookup"><span data-stu-id="a9d52-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a9d52-133">このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a9d52-134">*Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="a9d52-134">*Program.cs*:</span></span>

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

<span data-ttu-id="a9d52-135">この`AddMsalAuthentication`メソッドは、アプリの認証に必要なパラメーターを構成するコールバックを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="a9d52-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a9d52-136">アプリを登録するときに、Azure ポータル AAD 構成からアプリを構成するために必要な値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="a9d52-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="a9d52-137">アクセス トークン のスコープ</span><span class="sxs-lookup"><span data-stu-id="a9d52-137">Access token scopes</span></span>

<span data-ttu-id="a9d52-138">Blazor WebAssembly テンプレートは、セキュリティで保護された API のアクセス トークンを要求するようにアプリを自動的に構成しません。</span><span class="sxs-lookup"><span data-stu-id="a9d52-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a9d52-139">トークンをサインイン フローの一部としてプロビジョニングするには、スコープを の既定のアクセス トークン スコープに追加`MsalProviderOptions`します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a9d52-140">Azure Portal がスコープ URI を提供し、API から*401 未承認*の応答を受け取ったときに**アプリが未処理の例外をスロー**する場合は、スキームとホストを含まないスコープ URI を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="a9d52-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a9d52-141">たとえば、Azure ポータルでは、次のいずれかのスコープ URI 形式を提供できます。</span><span class="sxs-lookup"><span data-stu-id="a9d52-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a9d52-142">スキームとホストを使用せずにスコープ URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="a9d52-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a9d52-143">詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9d52-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="a9d52-144">ファイルをインポートする</span><span class="sxs-lookup"><span data-stu-id="a9d52-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a9d52-145">Index ページ</span><span class="sxs-lookup"><span data-stu-id="a9d52-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a9d52-146">アプリ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a9d52-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a9d52-147">コンポーネントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="a9d52-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a9d52-148">ログインディスプレイコンポーネント</span><span class="sxs-lookup"><span data-stu-id="a9d52-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a9d52-149">認証コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a9d52-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a9d52-150">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a9d52-150">Additional resources</span></span>

* [<span data-ttu-id="a9d52-151">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="a9d52-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a9d52-152">チュートリアル:Azure Active Directory B2C テナントの作成</span><span class="sxs-lookup"><span data-stu-id="a9d52-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a9d52-153">Microsoft ID プラットフォームのドキュメント</span><span class="sxs-lookup"><span data-stu-id="a9d52-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
