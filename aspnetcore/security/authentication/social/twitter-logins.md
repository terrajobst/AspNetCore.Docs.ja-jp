---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5182f1647acb664bf35f086fcddbe909559a62f7
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082301"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="e7887-103">ASP.NET Core を使用した Twitter の外部サインインセットアップ</span><span class="sxs-lookup"><span data-stu-id="e7887-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="e7887-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7887-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e7887-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 2.2 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e7887-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="e7887-106">Twitter でアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="e7887-106">Create the app in Twitter</span></span>

* <span data-ttu-id="e7887-107">移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。</span><span class="sxs-lookup"><span data-stu-id="e7887-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="e7887-108">まだ Twitter アカウントをお持ちでない場合は、[ **[今すぐサインアップ](https://twitter.com/signup)** ] リンクを使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="e7887-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="e7887-109">**[新しいアプリの作成]** をタップし、アプリケーション**名**、**説明**、およびパブリック**web サイト**URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。</span><span class="sxs-lookup"><span data-stu-id="e7887-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="e7887-110">**[有効な OAuth リダイレクト uri]** フィールドに追加された`https://webapp128.azurewebsites.net/signin-twitter` `/signin-twitter`開発 URI を入力します (例:)。</span><span class="sxs-lookup"><span data-stu-id="e7887-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="e7887-111">このサンプルで後で構成される Twitter 認証スキームは、OAuth `/signin-twitter`フローを実装するために、ルートで要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="e7887-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="e7887-112">URI セグメント`/signin-twitter`は、Twitter 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="e7887-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="e7887-113">[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="e7887-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="e7887-114">フォームの残りの部分を入力し、 **[Create Your Twitter application]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="e7887-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="e7887-115">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e7887-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="e7887-116">Twitter コンシューマー API キーとシークレットを格納する</span><span class="sxs-lookup"><span data-stu-id="e7887-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="e7887-117">次のコマンドを実行して`ClientId` 、 `ClientSecret` [シークレットマネージャー](xref:security/app-secrets)を安全に格納して使用します。</span><span class="sxs-lookup"><span data-stu-id="e7887-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="e7887-118">[シークレットマネージャー](xref:security/app-secrets)を使用し`Consumer Key`て`Consumer Secret` 、Twitter やなどの機密性の高い設定をアプリケーション構成にリンクします。</span><span class="sxs-lookup"><span data-stu-id="e7887-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e7887-119">このサンプルでは、トークン`Authentication:Twitter:ConsumerKey`にとと`Authentication:Twitter:ConsumerSecret`いう名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="e7887-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="e7887-120">これらのトークンは、新しい Twitter アプリケーションを作成した後の **[キーとアクセストークン]** タブにあります。</span><span class="sxs-lookup"><span data-stu-id="e7887-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="e7887-121">Twitter 認証の構成</span><span class="sxs-lookup"><span data-stu-id="e7887-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="e7887-122">`ConfigureServices` *Startup.cs*ファイルのメソッドに Twitter サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="e7887-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="e7887-123">Twitter 認証でサポートされる構成オプションの詳細については、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7887-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="e7887-124">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e7887-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="e7887-125">Twitter でのサインイン</span><span class="sxs-lookup"><span data-stu-id="e7887-125">Sign in with Twitter</span></span>

<span data-ttu-id="e7887-126">アプリを実行し、 **[ログイン]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e7887-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="e7887-127">Twitter でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e7887-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="e7887-128">**Twitter**をクリックすると、認証のために twitter にリダイレクトされるようになります。</span><span class="sxs-lookup"><span data-stu-id="e7887-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="e7887-129">Twitter の資格情報を入力すると、電子メールを設定できる web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e7887-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="e7887-130">これで、Twitter の資格情報を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="e7887-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="e7887-131">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="e7887-131">Troubleshooting</span></span>

* <span data-ttu-id="e7887-132">**ASP.NET Core 2.x のみ:** で`services.AddIdentity` *を呼び出すことによって id が構成されていない場合、認証を試みると ArgumentException が発生します。 `ConfigureServices`' SignInScheme ' オプションを指定*する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7887-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="e7887-133">このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="e7887-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="e7887-134">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="e7887-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="e7887-135">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="e7887-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e7887-136">次の手順</span><span class="sxs-lookup"><span data-stu-id="e7887-136">Next steps</span></span>

* <span data-ttu-id="e7887-137">この記事では、Twitter で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="e7887-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="e7887-138">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="e7887-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="e7887-139">Web サイトを Azure web アプリに発行したら、Twitter 開発者ポータル`ConsumerSecret`でをリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7887-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="e7887-140">設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="e7887-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="e7887-141">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="e7887-141">The configuration system is set up to read keys from environment variables.</span></span>
