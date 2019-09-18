---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082483"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="51b2e-103">ASP.NET Core での Google 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="51b2e-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="51b2e-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="51b2e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51b2e-105">[レガシ Google + api は2019年3月7日からシャットダウンされまし](https://developers.google.com/+/api-shutdown)た。</span><span class="sxs-lookup"><span data-stu-id="51b2e-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="51b2e-106">Google + サインインと開発者は、新しい Google サインインシステムに移行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="51b2e-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="51b2e-107">Google 認証用の ASP.NET Core 2.1 および2.2 パッケージは、変更内容に合わせて更新されます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="51b2e-108">ASP.NET Core の詳細と一時的な軽減策については、 [GitHub の問題](https://github.com/aspnet/AspNetCore/issues/6486)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51b2e-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="51b2e-109">このチュートリアルは、新しいセットアッププロセスで更新されました。</span><span class="sxs-lookup"><span data-stu-id="51b2e-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="51b2e-110">このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 2.2 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="51b2e-111">Google API コンソールプロジェクトとクライアント ID を作成する</span><span class="sxs-lookup"><span data-stu-id="51b2e-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="51b2e-112">[「Google サインインを web アプリに統合](https://developers.google.com/identity/sign-in/web/devconsole-project)する」に移動し、 **[プロジェクトの構成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="51b2e-113">**[OAuth クライアントの構成]** ダイアログで、 **[Web サーバー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="51b2e-114">[承認された**リダイレクト uri**のテキスト入力] ボックスで、リダイレクト uri を設定します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="51b2e-115">たとえば、`https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="51b2e-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="51b2e-116">**クライアント ID**と**クライアントシークレット**を保存します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="51b2e-117">サイトをデプロイするときに、新しいパブリック url を**Google コンソール**から登録します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="51b2e-118">ストア Google ClientID と ClientSecret</span><span class="sxs-lookup"><span data-stu-id="51b2e-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="51b2e-119">Google `Client ID`や`Client Secret` [Secret Manager](xref:security/app-secrets)などの機密性の高い設定を格納します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="51b2e-120">このチュートリアルでは、トークン`Authentication:Google:ClientId`にとと`Authentication:Google:ClientSecret`いう名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="51b2e-121">Api[コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="51b2e-122">Google 認証を構成する</span><span class="sxs-lookup"><span data-stu-id="51b2e-122">Configure Google authentication</span></span>

<span data-ttu-id="51b2e-123">Google サービスをに`Startup.ConfigureServices`追加します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-123">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="51b2e-124">Google のサインイン</span><span class="sxs-lookup"><span data-stu-id="51b2e-124">Sign in with Google</span></span>

* <span data-ttu-id="51b2e-125">アプリを実行し、 **[ログイン]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="51b2e-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="51b2e-126">Google でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="51b2e-127">**[Google]** ボタンをクリックします。これにより、認証のために google にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="51b2e-128">Google の資格情報を入力すると、web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="51b2e-129">Google 認証<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>でサポートされる構成オプションの詳細については、「API リファレンス」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51b2e-129">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="51b2e-130">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="51b2e-131">既定のコールバック URI を変更する</span><span class="sxs-lookup"><span data-stu-id="51b2e-131">Change the default callback URI</span></span>

<span data-ttu-id="51b2e-132">URI セグメント`/signin-google`Google の認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="51b2e-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="51b2e-133">既定のコールバック URI を変更するには、継承を使用して、Google の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラス。</span><span class="sxs-lookup"><span data-stu-id="51b2e-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="51b2e-134">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="51b2e-134">Troubleshooting</span></span>

* <span data-ttu-id="51b2e-135">サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。</span><span class="sxs-lookup"><span data-stu-id="51b2e-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="51b2e-136">で`services.AddIdentity` *を呼び出すことによって id が構成されていない場合、ArgumentException で結果を認証しようとしています。 `ConfigureServices`' SignInScheme ' オプションを指定*する必要があります。</span><span class="sxs-lookup"><span data-stu-id="51b2e-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="51b2e-137">このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="51b2e-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="51b2e-138">取得する場合は、初期移行を適用することで、サイト データベースが作成されていない*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="51b2e-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="51b2e-139">**[移行の適用]** を選択してデータベースを作成し、ページを更新してエラーを解消します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-139">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="51b2e-140">次の手順</span><span class="sxs-lookup"><span data-stu-id="51b2e-140">Next steps</span></span>

* <span data-ttu-id="51b2e-141">この記事では、Google で認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="51b2e-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="51b2e-142">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="51b2e-143">アプリを Azure に発行したら、Google API `ClientSecret`コンソールでをリセットします。</span><span class="sxs-lookup"><span data-stu-id="51b2e-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="51b2e-144">設定、`Authentication:Google:ClientId`と`Authentication:Google:ClientSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="51b2e-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="51b2e-145">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="51b2e-145">The configuration system is set up to read keys from environment variables.</span></span>
