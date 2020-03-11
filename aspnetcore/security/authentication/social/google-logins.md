---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/30/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 83f45143eca1be43410880bfd875a3fce1d2e9c9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654920"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="4174b-103">ASP.NET Core での Google 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="4174b-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="4174b-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4174b-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4174b-105">このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4174b-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="4174b-106">Google API コンソールプロジェクトとクライアント ID を作成する</span><span class="sxs-lookup"><span data-stu-id="4174b-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="4174b-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4174b-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="4174b-108">[「Google サインインを web アプリに統合](https://developers.google.com/identity/sign-in/web/devconsole-project)する」に移動し、 **[プロジェクトの構成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4174b-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="4174b-109">**[OAuth クライアントの構成]** ダイアログで、 **[Web サーバー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4174b-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="4174b-110">[承認された**リダイレクト uri**のテキスト入力] ボックスで、リダイレクト uri を設定します。</span><span class="sxs-lookup"><span data-stu-id="4174b-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="4174b-111">たとえば、`https://localhost:44312/signin-google` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="4174b-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="4174b-112">**クライアント ID**と**クライアントシークレット**を保存します。</span><span class="sxs-lookup"><span data-stu-id="4174b-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="4174b-113">サイトをデプロイするときに、新しいパブリック url を**Google コンソール**から登録します。</span><span class="sxs-lookup"><span data-stu-id="4174b-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="4174b-114">ストア Google ClientID と ClientSecret</span><span class="sxs-lookup"><span data-stu-id="4174b-114">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="4174b-115">Google `Client ID` や `Client Secret` などの機微な設定を[シークレットマネージャー](xref:security/app-secrets)に保存します。</span><span class="sxs-lookup"><span data-stu-id="4174b-115">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4174b-116">このチュートリアルでは、トークンに `Authentication:Google:ClientId` と `Authentication:Google:ClientSecret`を指定します。</span><span class="sxs-lookup"><span data-stu-id="4174b-116">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4174b-117">Api[コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。</span><span class="sxs-lookup"><span data-stu-id="4174b-117">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="4174b-118">Google 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="4174b-118">Configure Google authentication</span></span>

<span data-ttu-id="4174b-119">`Startup.ConfigureServices`に Google サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="4174b-119">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="4174b-120">Google でサインイン</span><span class="sxs-lookup"><span data-stu-id="4174b-120">Sign in with Google</span></span>

* <span data-ttu-id="4174b-121">アプリを実行し、 **[ログイン]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="4174b-121">Run the app and click **Log in**.</span></span> <span data-ttu-id="4174b-122">Google でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4174b-122">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="4174b-123">**[Google]** ボタンをクリックします。これにより、認証のために google にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="4174b-123">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="4174b-124">Google の資格情報を入力すると、web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="4174b-124">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="4174b-125">Google 認証でサポートされる構成オプションの詳細については、<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4174b-125">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="4174b-126">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4174b-126">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="4174b-127">既定のコールバック URI を変更する</span><span class="sxs-lookup"><span data-stu-id="4174b-127">Change the default callback URI</span></span>

<span data-ttu-id="4174b-128">`/signin-google` URI セグメントは、Google 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="4174b-128">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="4174b-129">[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Google 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="4174b-129">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4174b-130">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="4174b-130">Troubleshooting</span></span>

* <span data-ttu-id="4174b-131">サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。</span><span class="sxs-lookup"><span data-stu-id="4174b-131">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="4174b-132">`ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合は、 *ArgumentException: ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="4174b-132">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="4174b-133">このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="4174b-133">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="4174b-134">初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。</span><span class="sxs-lookup"><span data-stu-id="4174b-134">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="4174b-135">**[移行の適用]** を選択してデータベースを作成し、ページを更新してエラーを解消します。</span><span class="sxs-lookup"><span data-stu-id="4174b-135">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4174b-136">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="4174b-136">Next steps</span></span>

* <span data-ttu-id="4174b-137">この記事では、Google で認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="4174b-137">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="4174b-138">同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="4174b-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="4174b-139">アプリを Azure に発行したら、Google API コンソールで `ClientSecret` をリセットします。</span><span class="sxs-lookup"><span data-stu-id="4174b-139">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="4174b-140">Azure portal で、`Authentication:Google:ClientId` と `Authentication:Google:ClientSecret` をアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="4174b-140">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="4174b-141">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="4174b-141">The configuration system is set up to read keys from environment variables.</span></span>
