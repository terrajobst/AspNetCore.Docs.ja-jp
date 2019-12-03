---
title: ASP.NET Core での Facebook 外部ログインセットアップ
author: rick-anderson
description: Facebook アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を示すコード例を紹介したチュートリアルです。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717119"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="0d63c-103">ASP.NET Core での Facebook 外部ログインセットアップ</span><span class="sxs-lookup"><span data-stu-id="0d63c-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="0d63c-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0d63c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0d63c-105">このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが Facebook アカウントでサインインできるようにする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="0d63c-106">まず、[正式な手順](https://developers.facebook.com)に従って FACEBOOK アプリ ID を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="0d63c-107">Facebook でアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="0d63c-107">Create the app in Facebook</span></span>

* <span data-ttu-id="0d63c-108">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="0d63c-109">[Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="0d63c-110">まだ Facebook アカウントを持っていない場合は、ログインページの **[facebook へのサインアップ]** リンクを使用して作成します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="0d63c-111">Facebook アカウントを作成したら、手順に従って Facebook 開発者として登録します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="0d63c-112">**[マイアプリ**] メニューで、 **[アプリの作成]** を選択して新しいアプリ ID を作成します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![開発者向け Facebook ポータルを Microsoft Edge で開く](index/_static/FBMyApps.png)

* <span data-ttu-id="0d63c-114">フォームに入力し、 **[アプリ ID の作成]** ボタンをタップします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![新しいアプリ ID フォームを作成する](index/_static/FBNewAppId.png)

* <span data-ttu-id="0d63c-116">新しいアプリカードで、 **[製品の追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="0d63c-117">**Facebook ログイン**カードで、 **[セットアップ] をクリックし**ます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![製品のセットアップページ](index/_static/FBProductSetup.png)

* <span data-ttu-id="0d63c-119">**クイックスタート**ウィザードが起動し、最初のページとして **[プラットフォーム] を選択**します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="0d63c-120">ここでウィザードをバイパスするには、左下のメニューで [ **FaceBook のログイン** **設定**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![スキップクイックスタート](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="0d63c-122">**[クライアントの OAuth 設定]** ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![[クライアント OAuth 設定] ページ](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="0d63c-124">*/Signin-facebook*を使用して、 **[有効な OAuth リダイレクト uri]** フィールドに追加された開発 URI を入力します (例: `https://localhost:44320/signin-facebook`)。</span><span class="sxs-lookup"><span data-stu-id="0d63c-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="0d63c-125">このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook* route で要求を自動的に処理します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="0d63c-126">URI */signin-facebook*は、facebook 認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="0d63c-127">[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Facebook 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="0d63c-128">**[変更の保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="0d63c-129">左側のナビゲーションで、 **[設定]** 、 **[基本]** リンク の順にクリックし > ます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="0d63c-130">このページで、`App ID` と `App Secret`をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="0d63c-131">次のセクションでは、両方を ASP.NET Core アプリケーションに追加します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="0d63c-132">サイトをデプロイするときに、 **Facebook ログイン**のセットアップページを再表示し、新しいパブリック URI を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d63c-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-facebook-app-id-and-app-secret"></a><span data-ttu-id="0d63c-133">Facebook アプリ ID とアプリシークレットを格納する</span><span class="sxs-lookup"><span data-stu-id="0d63c-133">Store Facebook App ID and App Secret</span></span>

<span data-ttu-id="0d63c-134">[Secret Manager](xref:security/app-secrets)を使用して、Facebook `App ID` や `App Secret` などの機密性の高い設定をアプリケーション構成にリンクします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-134">Link sensitive settings like Facebook `App ID` and `App Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="0d63c-135">このチュートリアルでは、トークンに `Authentication:Facebook:AppId` と `Authentication:Facebook:AppSecret`という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-135">For the purposes of this tutorial, name the tokens `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="0d63c-136">次のコマンドを実行して、シークレットマネージャーを使用して `App ID` と `App Secret` を安全に格納します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-136">Execute the following commands to securely store `App ID` and `App Secret` using Secret Manager:</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a><span data-ttu-id="0d63c-137">Facebook 認証の構成</span><span class="sxs-lookup"><span data-stu-id="0d63c-137">Configure Facebook Authentication</span></span>

<span data-ttu-id="0d63c-138">*Startup.cs*ファイルの `ConfigureServices` メソッドに Facebook サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-138">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="0d63c-139">Facebook 認証でサポートされる構成オプションの詳細については、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0d63c-139">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="0d63c-140">構成オプションは、次の場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-140">Configuration options can be used to:</span></span>

* <span data-ttu-id="0d63c-141">ユーザーに関するさまざまな情報を要求します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-141">Request different information about the user.</span></span>
* <span data-ttu-id="0d63c-142">クエリ文字列引数を追加して、ログインエクスペリエンスをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-142">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="0d63c-143">Facebook でサインインする</span><span class="sxs-lookup"><span data-stu-id="0d63c-143">Sign in with Facebook</span></span>

<span data-ttu-id="0d63c-144">アプリケーションを実行し、 **[ログイン]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0d63c-144">Run your application and click **Log in**.</span></span> <span data-ttu-id="0d63c-145">Facebook でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-145">You see an option to sign in with Facebook.</span></span>

![Web アプリケーション: ユーザーが認証されていません](index/_static/DoneFacebook.png)

<span data-ttu-id="0d63c-147">**Facebook**をクリックすると、認証のために facebook にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-147">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook の認証ページ](index/_static/FBLogin.png)

<span data-ttu-id="0d63c-149">Facebook 認証は、既定でパブリックプロファイルと電子メールアドレスを要求します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-149">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook 認証ページの同意画面](index/_static/FBLoginDone.png)

<span data-ttu-id="0d63c-151">Facebook の資格情報を入力すると、電子メールを設定できるサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-151">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="0d63c-152">Facebook の資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="0d63c-152">You are now logged in using your Facebook credentials:</span></span>

![Web アプリケーション: ユーザー認証済み](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="0d63c-154">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0d63c-154">Troubleshooting</span></span>

* <span data-ttu-id="0d63c-155">**ASP.NET Core 2.x のみ:** `ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-155">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="0d63c-156">このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-156">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="0d63c-157">初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-157">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="0d63c-158">**[移行の適用]** をタップしてデータベースを作成し、更新してエラーを続行します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-158">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0d63c-159">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="0d63c-159">Next steps</span></span>

* <span data-ttu-id="0d63c-160">この記事では、Facebook で認証する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="0d63c-160">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="0d63c-161">同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="0d63c-161">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="0d63c-162">Web サイトを Azure web アプリに発行したら、Facebook 開発者ポータルで `AppSecret` をリセットする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0d63c-162">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="0d63c-163">Azure portal で、`Authentication:Facebook:AppId` と `Authentication:Facebook:AppSecret` をアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="0d63c-163">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="0d63c-164">構成システムは、環境変数からキーを読み取るように設定されています。</span><span class="sxs-lookup"><span data-stu-id="0d63c-164">The configuration system is set up to read keys from environment variables.</span></span>
