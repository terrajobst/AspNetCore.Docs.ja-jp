---
title: ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ
author: rick-anderson
description: このサンプルでは、Microsoft アカウントユーザー認証を既存の ASP.NET Core アプリに統合する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082338"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="c14c3-103">ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ</span><span class="sxs-lookup"><span data-stu-id="c14c3-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="c14c3-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c14c3-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c14c3-105">このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 2.2 プロジェクトを使用して、ユーザーが Microsoft アカウントでサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="c14c3-106">Microsoft 開発者ポータルでアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="c14c3-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="c14c3-107">[Azure portal アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。</span><span class="sxs-lookup"><span data-stu-id="c14c3-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="c14c3-108">Microsoft アカウントがない場合は、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="c14c3-109">サインインすると、 **[アプリの登録]** ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="c14c3-110">**新しい登録**の選択</span><span class="sxs-lookup"><span data-stu-id="c14c3-110">Select **New registration**</span></span>
* <span data-ttu-id="c14c3-111">**名前**を入力してください。</span><span class="sxs-lookup"><span data-stu-id="c14c3-111">Enter a **Name**.</span></span>
* <span data-ttu-id="c14c3-112">**サポートされているアカウントの種類**のオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="c14c3-113">**[リダイレクト URI]** に、追加した`/signin-microsoft`開発 URL を入力します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="c14c3-114">たとえば、`https://localhost:44389/signin-microsoft` のようにします。</span><span class="sxs-lookup"><span data-stu-id="c14c3-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="c14c3-115">このサンプルの後半で構成されている Microsoft 認証スキームは`/signin-microsoft` 、OAuth フローを実装するために、ルートで要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="c14c3-116">**登録**の選択</span><span class="sxs-lookup"><span data-stu-id="c14c3-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="c14c3-117">クライアントシークレットの作成</span><span class="sxs-lookup"><span data-stu-id="c14c3-117">Create client secret</span></span>

* <span data-ttu-id="c14c3-118">左側のウィンドウで、 **[証明書 & シークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="c14c3-119">**[クライアントシークレット]** で、 **[新しいクライアントシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="c14c3-120">クライアントシークレットの説明を追加します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="c14c3-121">**[追加]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-121">Select the **Add** button.</span></span>

* <span data-ttu-id="c14c3-122">**[クライアントシークレット]** で、クライアントシークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="c14c3-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="c14c3-123">URI セグメント`/signin-microsoft`は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="c14c3-124">[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Microsoft 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="c14c3-125">Microsoft クライアント ID とクライアントシークレットを保存する</span><span class="sxs-lookup"><span data-stu-id="c14c3-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="c14c3-126">次のコマンドを実行して`ClientId` 、 `ClientSecret` [シークレットマネージャー](xref:security/app-secrets)を安全に格納して使用します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="c14c3-127">[シークレットマネージャー](xref:security/app-secrets)を使用し`ClientId`て`ClientSecret` 、Microsoft やなどの機密性の高い設定をアプリケーション構成にリンクします。</span><span class="sxs-lookup"><span data-stu-id="c14c3-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c14c3-128">このサンプルでは、トークン`Authentication:Microsoft:ClientId`にとと`Authentication:Microsoft:ClientSecret`いう名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="c14c3-129">Microsoft アカウント認証を構成する</span><span class="sxs-lookup"><span data-stu-id="c14c3-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="c14c3-130">Microsoft アカウントサービスをに`Startup.ConfigureServices`追加します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="c14c3-131">Microsoft アカウント認証でサポートされる構成オプションの詳細については、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c14c3-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="c14c3-132">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="c14c3-133">Microsoft アカウントでサインインアカウント</span><span class="sxs-lookup"><span data-stu-id="c14c3-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="c14c3-134">を実行し、 **[ログイン]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c14c3-134">Run the and click **Log in**.</span></span> <span data-ttu-id="c14c3-135">Microsoft でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="c14c3-136">[Microsoft] をクリックすると、認証のために Microsoft にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="c14c3-137">Microsoft アカウントでサインインした後 (まだサインインしていない場合)、アプリに情報へのアクセスを許可するように求められます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="c14c3-138">**[はい]** をタップすると、電子メールを設定できる web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="c14c3-139">これで、Microsoft 資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="c14c3-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="c14c3-140">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c14c3-140">Troubleshooting</span></span>

* <span data-ttu-id="c14c3-141">Microsoft アカウントプロバイダーによってサインインエラーページが表示された場合は、Uri の (ハッシュタグ) の`#`すぐ後にあるエラータイトルと説明のクエリ文字列パラメーターを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c14c3-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="c14c3-142">エラーメッセージは Microsoft 認証に問題があることを示していますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定されている**リダイレクト uri**と一致していないことです。</span><span class="sxs-lookup"><span data-stu-id="c14c3-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="c14c3-143">で`services.AddIdentity` *を呼び出すことによって id が構成されていない場合、認証を試みると ArgumentException が発生します。 `ConfigureServices`' SignInScheme ' オプションを指定*する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c14c3-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c14c3-144">このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="c14c3-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="c14c3-145">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="c14c3-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c14c3-146">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c14c3-147">次の手順</span><span class="sxs-lookup"><span data-stu-id="c14c3-147">Next steps</span></span>

* <span data-ttu-id="c14c3-148">この記事では、Microsoft で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="c14c3-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="c14c3-149">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c14c3-150">Web サイトを Azure web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアントシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="c14c3-151">設定、`Authentication:Microsoft:ClientId`と`Authentication:Microsoft:ClientSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="c14c3-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c14c3-152">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="c14c3-152">The configuration system is set up to read keys from environment variables.</span></span>