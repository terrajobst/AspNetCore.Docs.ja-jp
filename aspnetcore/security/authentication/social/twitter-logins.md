---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 4710c033018710ce3620f8d7221ae2253b2c0b69
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172518"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="23c98-103">ASP.NET Core を使用した Twitter の外部サインインセットアップ</span><span class="sxs-lookup"><span data-stu-id="23c98-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="23c98-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23c98-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="23c98-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23c98-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="23c98-106">Twitter でアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="23c98-106">Create the app in Twitter</span></span>

* <span data-ttu-id="23c98-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="23c98-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="23c98-108">[https://apps.twitter.com/](https://apps.twitter.com/)に移動し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="23c98-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="23c98-109">まだ Twitter アカウントをお持ちでない場合は、[ **[今すぐサインアップ](https://twitter.com/signup)** ] リンクを使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="23c98-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="23c98-110">**[アプリの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="23c98-110">Select **Create an app**.</span></span> <span data-ttu-id="23c98-111">**アプリ名**、**アプリケーションの説明**、およびパブリック**web サイト**の URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。</span><span class="sxs-lookup"><span data-stu-id="23c98-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="23c98-112">[ **Twitter でのサインインを有効**にする] の横にあるチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="23c98-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="23c98-113">AspNetCore では、ユーザーは既定で電子メールアドレスを持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="23c98-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="23c98-114">**[アクセス許可]** タブにアクセスし、 **[編集]** ボタンをクリックして、 **[ユーザーに電子メールアドレスを要求]** する の横にあるチェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="23c98-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="23c98-115">**[コールバック url]** フィールドに `/signin-twitter` 追加された開発 URI を入力します (例: `https://webapp128.azurewebsites.net/signin-twitter`)。</span><span class="sxs-lookup"><span data-stu-id="23c98-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="23c98-116">このサンプルの後半で構成されている Twitter 認証スキームは、OAuth フローを実装するために `/signin-twitter` ルートで要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="23c98-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="23c98-117">`/signin-twitter` URI セグメントは、Twitter 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="23c98-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="23c98-118">[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="23c98-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="23c98-119">フォームの残りの部分を入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="23c98-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="23c98-120">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="23c98-120">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="23c98-121">Twitter コンシューマー API キーとシークレットを格納する</span><span class="sxs-lookup"><span data-stu-id="23c98-121">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="23c98-122">次のコマンドを実行して、[シークレットマネージャー](xref:security/app-secrets)を使用して `ClientId` と `ClientSecret` を安全に格納します。</span><span class="sxs-lookup"><span data-stu-id="23c98-122">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="23c98-123">[Secret Manager](xref:security/app-secrets)を使用して、Twitter `Consumer Key` や `Consumer Secret` などの機密性の高い設定をアプリケーション構成にリンクします。</span><span class="sxs-lookup"><span data-stu-id="23c98-123">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="23c98-124">このサンプルでは、トークンに `Authentication:Twitter:ConsumerKey` と `Authentication:Twitter:ConsumerSecret`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="23c98-124">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="23c98-125">これらのトークンは、新しい Twitter アプリケーションを作成した後の **[キーとアクセストークン]** タブにあります。</span><span class="sxs-lookup"><span data-stu-id="23c98-125">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="23c98-126">Twitter 認証の構成</span><span class="sxs-lookup"><span data-stu-id="23c98-126">Configure Twitter Authentication</span></span>

<span data-ttu-id="23c98-127">*Startup.cs*ファイルの `ConfigureServices` メソッドに Twitter サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="23c98-127">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="23c98-128">Twitter 認証でサポートされる構成オプションの詳細については、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23c98-128">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="23c98-129">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23c98-129">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="23c98-130">Twitter でのサインイン</span><span class="sxs-lookup"><span data-stu-id="23c98-130">Sign in with Twitter</span></span>

<span data-ttu-id="23c98-131">アプリを実行し、 **[ログイン]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="23c98-131">Run the app and select **Log in**.</span></span> <span data-ttu-id="23c98-132">Twitter でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="23c98-132">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="23c98-133">**Twitter**をクリックすると、認証のために twitter にリダイレクトされるようになります。</span><span class="sxs-lookup"><span data-stu-id="23c98-133">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="23c98-134">Twitter の資格情報を入力すると、電子メールを設定できる web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="23c98-134">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="23c98-135">これで、Twitter の資格情報を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="23c98-135">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="23c98-136">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="23c98-136">Troubleshooting</span></span>

* <span data-ttu-id="23c98-137">**ASP.NET Core 2.x のみ:** `ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="23c98-137">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="23c98-138">このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="23c98-138">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="23c98-139">初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。</span><span class="sxs-lookup"><span data-stu-id="23c98-139">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="23c98-140">**[移行の適用]** をタップしてデータベースを作成し、更新してエラーを続行します。</span><span class="sxs-lookup"><span data-stu-id="23c98-140">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="23c98-141">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="23c98-141">Next steps</span></span>

* <span data-ttu-id="23c98-142">この記事では、Twitter で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="23c98-142">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="23c98-143">同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="23c98-143">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="23c98-144">Web サイトを Azure web アプリに発行したら、Twitter 開発者ポータルで `ConsumerSecret` をリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="23c98-144">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="23c98-145">Azure portal で、`Authentication:Twitter:ConsumerKey` と `Authentication:Twitter:ConsumerSecret` をアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="23c98-145">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="23c98-146">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="23c98-146">The configuration system is set up to read keys from environment variables.</span></span>
