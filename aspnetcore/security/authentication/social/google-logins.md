---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントユーザー認証を統合する方法について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/28/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 663029ecab99efd4f63f8deca026957c19c64710
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034318"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="04237-103">ASP.NET Core での Google 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="04237-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="04237-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="04237-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="04237-105">このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="04237-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="04237-106">Google API コンソールプロジェクトとクライアント ID を作成する</span><span class="sxs-lookup"><span data-stu-id="04237-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="04237-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="04237-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="04237-108">[「Google サインインを web アプリに統合](https://developers.google.com/identity/sign-in/web/devconsole-project)する」に移動し、 **[プロジェクトの構成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="04237-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="04237-109">**[OAuth クライアントの構成]** ダイアログで、 **[Web サーバー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="04237-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="04237-110">[承認された**リダイレクト uri**のテキスト入力] ボックスで、リダイレクト uri を設定します。</span><span class="sxs-lookup"><span data-stu-id="04237-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="04237-111">たとえば、`https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="04237-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="04237-112">**クライアント ID**と**クライアントシークレット**を保存します。</span><span class="sxs-lookup"><span data-stu-id="04237-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="04237-113">サイトをデプロイするときに、新しいパブリック url を**Google コンソール**から登録します。</span><span class="sxs-lookup"><span data-stu-id="04237-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="04237-114">Google ClientID と ClientSecret を格納する</span><span class="sxs-lookup"><span data-stu-id="04237-114">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="04237-115">Google `Client ID` や `Client Secret` などの機微な設定を[シークレットマネージャー](xref:security/app-secrets)に保存します。</span><span class="sxs-lookup"><span data-stu-id="04237-115">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="04237-116">このチュートリアルでは、トークンに `Authentication:Google:ClientId` と `Authentication:Google:ClientSecret`を指定します。</span><span class="sxs-lookup"><span data-stu-id="04237-116">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="04237-117">Api[コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。</span><span class="sxs-lookup"><span data-stu-id="04237-117">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="04237-118">Google 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="04237-118">Configure Google authentication</span></span>

<span data-ttu-id="04237-119">`Startup.ConfigureServices`に Google サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="04237-119">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="04237-120">Google でのサインイン</span><span class="sxs-lookup"><span data-stu-id="04237-120">Sign in with Google</span></span>

* <span data-ttu-id="04237-121">アプリを実行し、 **[ログイン]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="04237-121">Run the app and click **Log in**.</span></span> <span data-ttu-id="04237-122">Google でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="04237-122">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="04237-123">**[Google]** ボタンをクリックします。これにより、認証のために google にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="04237-123">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="04237-124">Google の資格情報を入力すると、web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="04237-124">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="04237-125">Google 認証でサポートされる構成オプションの詳細については、<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="04237-125">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="04237-126">これは、ユーザーに関するさまざまな情報を要求するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="04237-126">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="04237-127">既定のコールバック URI を変更する</span><span class="sxs-lookup"><span data-stu-id="04237-127">Change the default callback URI</span></span>

<span data-ttu-id="04237-128">`/signin-google` URI セグメントは、Google 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="04237-128">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="04237-129">[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Google 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="04237-129">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="04237-130">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="04237-130">Troubleshooting</span></span>

* <span data-ttu-id="04237-131">サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。</span><span class="sxs-lookup"><span data-stu-id="04237-131">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="04237-132">`ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合は、 *ArgumentException: ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="04237-132">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="04237-133">このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="04237-133">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="04237-134">初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。</span><span class="sxs-lookup"><span data-stu-id="04237-134">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="04237-135">**[移行の適用]** を選択してデータベースを作成し、ページを更新してエラーを解消します。</span><span class="sxs-lookup"><span data-stu-id="04237-135">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="04237-136">次のステップ</span><span class="sxs-lookup"><span data-stu-id="04237-136">Next steps</span></span>

* <span data-ttu-id="04237-137">この記事では、Google で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="04237-137">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="04237-138">同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="04237-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="04237-139">アプリを Azure に発行したら、Google API コンソールで `ClientSecret` をリセットします。</span><span class="sxs-lookup"><span data-stu-id="04237-139">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="04237-140">Azure portal で、`Authentication:Google:ClientId` と `Authentication:Google:ClientSecret` をアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="04237-140">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="04237-141">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="04237-141">The configuration system is set up to read keys from environment variables.</span></span>
