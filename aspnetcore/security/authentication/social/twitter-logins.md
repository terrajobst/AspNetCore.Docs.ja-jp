---
title: ASP.NET Core での twitter の外部サインイン設定
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Twitter アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: d816ed27898639b0af6896a51ac035d5526c5d29
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814068"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="b5376-103">ASP.NET Core での twitter の外部サインイン設定</span><span class="sxs-lookup"><span data-stu-id="b5376-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="b5376-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b5376-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b5376-105">このサンプルは、ユーザーを有効にする方法を示しています。 [、Twitter アカウントでサインイン](https://dev.twitter.com/web/sign-in/desktop-browser)で作成されたサンプルの ASP.NET Core 2.2 プロジェクトを使用して、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="b5376-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="b5376-106">Twitter でアプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5376-106">Create the app in Twitter</span></span>

* <span data-ttu-id="b5376-107">移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。</span><span class="sxs-lookup"><span data-stu-id="b5376-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="b5376-108">Twitter アカウントがない場合は、使用、 **[今すぐサインアップ](https://twitter.com/signup)** リンクを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5376-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="b5376-109">タップ**Create New App**アプリケーション記入と**名前**、**説明**とパブリック**web サイト**URI (このことは一時的なものまで、ドメイン名を登録)。</span><span class="sxs-lookup"><span data-stu-id="b5376-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="b5376-110">開発 URI を入力と`/signin-twitter`に追加されます、**有効な OAuth リダイレクト Uri**フィールド (例: `https://webapp128.azurewebsites.net/signin-twitter`)。</span><span class="sxs-lookup"><span data-stu-id="b5376-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="b5376-111">このサンプルでは後で構成されている Twitter 認証の構成はで、要求を自動的に処理`/signin-twitter`OAuth フローを実装するルート。</span><span class="sxs-lookup"><span data-stu-id="b5376-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="b5376-112">URI セグメント`/signin-twitter`Twitter の認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="b5376-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="b5376-113">既定のコールバック URI を変更するには、継承を使用して、Twitter 認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラス。</span><span class="sxs-lookup"><span data-stu-id="b5376-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="b5376-114">フォームの残りの部分を入力し、タップ**Twitter アプリケーションを作成**です。</span><span class="sxs-lookup"><span data-stu-id="b5376-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="b5376-115">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5376-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="b5376-116">Twitter のコンシューマー API キーとシークレットを格納します。</span><span class="sxs-lookup"><span data-stu-id="b5376-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="b5376-117">安全に保存するには、次のコマンドを実行`ClientId`と`ClientSecret`を使用して[Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="b5376-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

<span data-ttu-id="b5376-118">Twitter などの機密設定をリンク`Consumer Key`と`Consumer Secret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="b5376-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b5376-119">このサンプルの目的で、名前トークン`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`します。</span><span class="sxs-lookup"><span data-stu-id="b5376-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="b5376-120">これらのトークンで確認できます、 **Keys and Access Tokens**新しい Twitter アプリケーションを作成した後タブ。</span><span class="sxs-lookup"><span data-stu-id="b5376-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="b5376-121">Twitter 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="b5376-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="b5376-122">Twitter サービスを追加、`ConfigureServices`メソッド*Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="b5376-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="b5376-123">参照してください、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 認証でサポートされる構成オプションの詳細について、API リファレンス。</span><span class="sxs-lookup"><span data-stu-id="b5376-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="b5376-124">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b5376-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="b5376-125">Sign in with Twitter</span><span class="sxs-lookup"><span data-stu-id="b5376-125">Sign in with Twitter</span></span>

<span data-ttu-id="b5376-126">アプリを実行し、選択**ログイン**します。</span><span class="sxs-lookup"><span data-stu-id="b5376-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="b5376-127">Twitter でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b5376-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="b5376-128">クリックすると**Twitter**認証の Twitter にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="b5376-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="b5376-129">Twitter の資格情報を入力した後、電子メールを設定する web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="b5376-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="b5376-130">これで、Twitter の資格情報を使用してをログインしています。</span><span class="sxs-lookup"><span data-stu-id="b5376-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="b5376-131">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b5376-131">Troubleshooting</span></span>

* <span data-ttu-id="b5376-132">**ASP.NET Core 2.x のみ。** ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。</span><span class="sxs-lookup"><span data-stu-id="b5376-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="b5376-133">このサンプルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="b5376-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="b5376-134">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="b5376-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="b5376-135">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="b5376-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b5376-136">次の手順</span><span class="sxs-lookup"><span data-stu-id="b5376-136">Next steps</span></span>

* <span data-ttu-id="b5376-137">この記事では、Twitter に認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="b5376-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="b5376-138">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="b5376-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="b5376-139">リセットする必要があります、web サイトを Azure web アプリを発行すると、 `ConsumerSecret` Twitter の開発者ポータルでします。</span><span class="sxs-lookup"><span data-stu-id="b5376-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="b5376-140">設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="b5376-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="b5376-141">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="b5376-141">The configuration system is set up to read keys from environment variables.</span></span>