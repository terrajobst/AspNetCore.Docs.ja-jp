---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5d0695160d90d0c5d31b8e35bc6c4cc984829333
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944214"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="64db1-103">ASP.NET Core を使用した Twitter の外部サインインセットアップ</span><span class="sxs-lookup"><span data-stu-id="64db1-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="64db1-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64db1-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64db1-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="64db1-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="64db1-106">Twitter でアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="64db1-106">Create the app in Twitter</span></span>

* <span data-ttu-id="64db1-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="64db1-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="64db1-108">移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。</span><span class="sxs-lookup"><span data-stu-id="64db1-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="64db1-109">まだ Twitter アカウントをお持ちでない場合は、[ **[今すぐサインアップ](https://twitter.com/signup)** ] リンクを使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="64db1-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="64db1-110">**[アプリの作成]** を選択します</span><span class="sxs-lookup"><span data-stu-id="64db1-110">Select **Create an app**.</span></span> <span data-ttu-id="64db1-111">**アプリ名**、**アプリケーションの説明**、およびパブリック**web サイト**の URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。</span><span class="sxs-lookup"><span data-stu-id="64db1-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="64db1-112">**[コールバック url]** フィールドに `/signin-twitter` 追加された開発 URI を入力します (例: `https://webapp128.azurewebsites.net/signin-twitter`)。</span><span class="sxs-lookup"><span data-stu-id="64db1-112">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="64db1-113">このサンプルの後半で構成されている Twitter 認証スキームは、OAuth フローを実装するために `/signin-twitter` ルートで要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="64db1-113">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="64db1-114">`/signin-twitter` URI セグメントは、Twitter 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="64db1-114">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="64db1-115">[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="64db1-115">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="64db1-116">フォームの残りの部分を入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="64db1-116">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="64db1-117">新しいアプリケーションの詳細が表示されます。</span><span class="sxs-lookup"><span data-stu-id="64db1-117">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="64db1-118">Twitter コンシューマー API キーとシークレットを格納する</span><span class="sxs-lookup"><span data-stu-id="64db1-118">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="64db1-119">次のコマンドを実行して、[シークレットマネージャー](xref:security/app-secrets)を使用して `ClientId` と `ClientSecret` を安全に格納します。</span><span class="sxs-lookup"><span data-stu-id="64db1-119">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="64db1-120">[Secret Manager](xref:security/app-secrets)を使用して、Twitter `Consumer Key` や `Consumer Secret` などの機密性の高い設定をアプリケーション構成にリンクします。</span><span class="sxs-lookup"><span data-stu-id="64db1-120">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="64db1-121">このサンプルでは、トークンに `Authentication:Twitter:ConsumerKey` と `Authentication:Twitter:ConsumerSecret`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="64db1-121">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="64db1-122">これらのトークンは、新しい Twitter アプリケーションを作成した後の **[キーとアクセストークン]** タブにあります。</span><span class="sxs-lookup"><span data-stu-id="64db1-122">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="64db1-123">Twitter 認証の構成</span><span class="sxs-lookup"><span data-stu-id="64db1-123">Configure Twitter Authentication</span></span>

<span data-ttu-id="64db1-124">*Startup.cs*ファイルの `ConfigureServices` メソッドに Twitter サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="64db1-124">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="64db1-125">Twitter 認証でサポートされる構成オプションの詳細については、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="64db1-125">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="64db1-126">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="64db1-126">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="64db1-127">Twitter でのサインイン</span><span class="sxs-lookup"><span data-stu-id="64db1-127">Sign in with Twitter</span></span>

<span data-ttu-id="64db1-128">アプリを実行し、 **[ログイン]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="64db1-128">Run the app and select **Log in**.</span></span> <span data-ttu-id="64db1-129">Twitter でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="64db1-129">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="64db1-130">**Twitter**をクリックすると、認証のために twitter にリダイレクトされるようになります。</span><span class="sxs-lookup"><span data-stu-id="64db1-130">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="64db1-131">Twitter の資格情報を入力すると、電子メールを設定できる web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="64db1-131">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="64db1-132">これで、Twitter の資格情報を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="64db1-132">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="64db1-133">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="64db1-133">Troubleshooting</span></span>

* <span data-ttu-id="64db1-134">**ASP.NET Core 2.x のみ:** 呼び出すことによって構成されていない場合の Identity`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException: 'SignInScheme' オプションを指定する必要があります*します。</span><span class="sxs-lookup"><span data-stu-id="64db1-134">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="64db1-135">このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="64db1-135">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="64db1-136">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="64db1-136">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="64db1-137">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="64db1-137">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="64db1-138">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="64db1-138">Next steps</span></span>

* <span data-ttu-id="64db1-139">この記事では、Twitter で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="64db1-139">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="64db1-140">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="64db1-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="64db1-141">Web サイトを Azure web アプリに発行したら、Twitter 開発者ポータルで `ConsumerSecret` をリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="64db1-141">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="64db1-142">設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="64db1-142">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="64db1-143">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="64db1-143">The configuration system is set up to read keys from environment variables.</span></span>
