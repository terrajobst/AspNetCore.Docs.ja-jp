---
title: ASP.NET Core での Microsoft アカウントの外部ログインのセットアップ
author: rick-anderson
description: このサンプルでは、既存の ASP.NET Core アプリケーションを Microsoft アカウント ユーザーの認証の統合を示します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815576"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="aaa26-103">ASP.NET Core での Microsoft アカウントの外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="aaa26-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="aaa26-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aaa26-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aaa26-105">このサンプルは、ASP.NET Core 2.2 プロジェクトが作成を使用して、Microsoft アカウントでサインインするユーザーを有効にする方法を示します、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="aaa26-106">Microsoft 開発者ポータルで、アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="aaa26-107">移動し、 [Azure portal - アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページで、作成するか、Microsoft アカウントにサインインします。</span><span class="sxs-lookup"><span data-stu-id="aaa26-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="aaa26-108">Microsoft アカウントを持っていない場合は、選択**作成**です。</span><span class="sxs-lookup"><span data-stu-id="aaa26-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="aaa26-109">サインインした後にリダイレクトされたら、**アプリの登録**ページ。</span><span class="sxs-lookup"><span data-stu-id="aaa26-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="aaa26-110">選択**新規登録**</span><span class="sxs-lookup"><span data-stu-id="aaa26-110">Select **New registration**</span></span>
* <span data-ttu-id="aaa26-111">入力、**名前**します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-111">Enter a **Name**.</span></span>
* <span data-ttu-id="aaa26-112">オプションを選択**勘定科目の種類がサポートされている**します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="aaa26-113">**リダイレクト URI**で、開発の URL を入力`/signin-microsoft`追加されます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="aaa26-114">たとえば、`https://localhost:44389/signin-microsoft` のようにします。</span><span class="sxs-lookup"><span data-stu-id="aaa26-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="aaa26-115">このサンプルでは後で構成されている Microsoft の認証構成はで、要求を自動的に処理`/signin-microsoft`OAuth フローを実装するルート。</span><span class="sxs-lookup"><span data-stu-id="aaa26-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="aaa26-116">選択**登録**</span><span class="sxs-lookup"><span data-stu-id="aaa26-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="aaa26-117">クライアント シークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-117">Create client secret</span></span>

* <span data-ttu-id="aaa26-118">左側のウィンドウで次のように選択します。**証明書およびシークレット**します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="aaa26-119">**クライアント シークレット**、**新しいクライアント シークレット**</span><span class="sxs-lookup"><span data-stu-id="aaa26-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="aaa26-120">クライアント シークレットの説明を追加します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="aaa26-121">選択、**追加**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="aaa26-121">Select the **Add** button.</span></span>

* <span data-ttu-id="aaa26-122">**クライアント シークレット**、クライアント シークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="aaa26-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="aaa26-123">URI セグメント`/signin-microsoft`Microsoft 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="aaa26-124">既定のコールバック URI を変更するには、継承を使用して、Microsoft の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラス。</span><span class="sxs-lookup"><span data-stu-id="aaa26-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="aaa26-125">Microsoft のクライアント ID とクライアント シークレットを格納します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="aaa26-126">安全に保存するには、次のコマンドを実行`ClientId`と`ClientSecret`を使用して[Secret Manager](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="aaa26-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="aaa26-127">Microsoft のような機密性の高い設定リンク`ClientId`と`ClientSecret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="aaa26-128">このサンプルの目的で、名前トークン`Authentication:Microsoft:ClientId`と`Authentication:Microsoft:ClientSecret`します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="aaa26-129">Microsoft アカウント認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="aaa26-130">Microsoft アカウント サービスを追加`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="aaa26-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="aaa26-131">参照してください、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスの詳細については、Microsoft アカウント認証でサポートされる構成オプション。</span><span class="sxs-lookup"><span data-stu-id="aaa26-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="aaa26-132">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="aaa26-133">Microsoft アカウントでサインイン</span><span class="sxs-lookup"><span data-stu-id="aaa26-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="aaa26-134">実行 をクリック**ログイン**します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-134">Run the and click **Log in**.</span></span> <span data-ttu-id="aaa26-135">Microsoft アカウントでサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="aaa26-136">Microsoft をクリックすると、認証のように、Microsoft にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="aaa26-137">(サインインされていない) 場合、Microsoft アカウントでサインインしたら、アプリの情報にアクセスできるようにするよう求められます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="aaa26-138">タップ**はい**とメールを設定する web サイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="aaa26-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="aaa26-139">Microsoft の資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="aaa26-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="aaa26-140">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="aaa26-140">Troubleshooting</span></span>

* <span data-ttu-id="aaa26-141">Microsoft アカウント プロバイダーでは、サインイン エラー ページにリダイレクトするの場合、エラーのタイトルと説明クエリ文字列パラメーターの直後に注意してください、 `#` (ハッシュタグ) uri。</span><span class="sxs-lookup"><span data-stu-id="aaa26-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="aaa26-142">エラー メッセージを Microsoft 認証に問題を示すために見えますが、最も一般的な原因は、アプリケーション Uri と一致しない、**リダイレクト Uri**の指定、 **Web**プラットフォーム.</span><span class="sxs-lookup"><span data-stu-id="aaa26-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="aaa26-143">ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="aaa26-144">このサンプルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="aaa26-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="aaa26-145">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="aaa26-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="aaa26-146">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="aaa26-147">次の手順</span><span class="sxs-lookup"><span data-stu-id="aaa26-147">Next steps</span></span>

* <span data-ttu-id="aaa26-148">この記事では、Microsoft を認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="aaa26-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="aaa26-149">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="aaa26-150">Azure web アプリに web サイトを発行するとシークレットを作成、新しいクライアント、Microsoft 開発者ポータルで。</span><span class="sxs-lookup"><span data-stu-id="aaa26-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="aaa26-151">設定、`Authentication:Microsoft:ClientId`と`Authentication:Microsoft:ClientSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="aaa26-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="aaa26-152">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="aaa26-152">The configuration system is set up to read keys from environment variables.</span></span>