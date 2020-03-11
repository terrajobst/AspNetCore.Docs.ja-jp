---
title: Facebook、Google、ASP.NET Core での外部プロバイダーの認証
author: rick-anderson
description: このチュートリアルでは、OAuth 2.0 と外部の認証プロバイダーを使用して ASP.NET Core アプリを構築する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644816"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="fa3a2-103">Facebook、Google、ASP.NET Core での外部プロバイダーの認証</span><span class="sxs-lookup"><span data-stu-id="fa3a2-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="fa3a2-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fa3a2-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fa3a2-105">このチュートリアルでは、ユーザーが OAuth 2.0 と外部の認証プロバイダーの資格情報を使用してサインインできる、ASP.NET Core 3.0 アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="fa3a2-106">以下のセクションでは、[Facebook](xref:security/authentication/facebook-logins)、[Twitter](xref:security/authentication/twitter-logins)、[Google](xref:security/authentication/google-logins)、および [Microsoft](xref:security/authentication/microsoft-logins) の各プロバイダーを対象とします。また、この記事で作成するスタート プロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="fa3a2-107">他のプロバイダーは、[AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers)、[AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) などのサードパーティ パッケージで利用できます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="fa3a2-108">既存の資格情報でユーザーがサインインできるようになると:</span><span class="sxs-lookup"><span data-stu-id="fa3a2-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="fa3a2-109">ユーザーにとって便利です。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-109">Is convenient for the users.</span></span>
* <span data-ttu-id="fa3a2-110">サインイン プロセスの複雑な管理の多くが、サード パーティに移ります。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="fa3a2-111">ソーシャル ログインによってトラフィックとユーザーの変換を促進する方法の例については、[Facebook](https://www.facebook.com/unsupportedbrowser) と [Twitter](https://dev.twitter.com/resources/case-studies) によるケース スタディを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="fa3a2-112">新しい .NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="fa3a2-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa3a2-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa3a2-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fa3a2-114">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-114">Create a new project.</span></span>
* <span data-ttu-id="fa3a2-115">**[ASP.NET Core Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="fa3a2-116">**[プロジェクト名]** を指定して、 **[場所]** を確認または変更します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="fa3a2-117">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-117">Select **Create**.</span></span>
* <span data-ttu-id="fa3a2-118">ドロップダウン (**ASP.NET Core {X.y}** ) で ASP.NET Core の最新バージョンを選択し、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="fa3a2-119">**[認証]** の下で、 **[変更]** を選択して認証を **[個人のユーザー アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="fa3a2-120">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-120">Select **OK**.</span></span>
* <span data-ttu-id="fa3a2-121">**[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fa3a2-122">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fa3a2-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="fa3a2-123">ターミナルを開きます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-123">Open the terminal.</span></span>  <span data-ttu-id="fa3a2-124">Visual Studio Code の場合は、[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開くことができます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="fa3a2-125">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="fa3a2-126">Windows の場合、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="fa3a2-127">macOS および Linux の場合は、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="fa3a2-128">`dotnet new` コマンドでは、*WebApp1* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="fa3a2-129">`-au Individual` によって、個々の認証に対するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="fa3a2-130">`-uld` では、Windows 用の SQL Server Express の軽量バージョンである、LocalDB を使用します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="fa3a2-131">`-uld` を省略して SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="fa3a2-132">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *WebApp1* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="fa3a2-133">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="fa3a2-133">Apply migrations</span></span>

* <span data-ttu-id="fa3a2-134">アプリを実行し、 **[登録]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="fa3a2-135">新しいアカウントの電子メール アドレスとパスワードを入力し、 **[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="fa3a2-136">指示に従って移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="fa3a2-137">SecretManager を使用して、ログイン プロバイダーから割り当てられたトークンを格納する</span><span class="sxs-lookup"><span data-stu-id="fa3a2-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="fa3a2-138">ソーシャル ログイン プロバイダーは、登録プロセス中に**アプリケーション ID** トークンと**アプリケーション シークレット** トークンを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="fa3a2-139">完全なトークン名はプロバイダーにより異なります。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-139">The exact token names vary by provider.</span></span> <span data-ttu-id="fa3a2-140">これらのトークンは、アプリが API にアクセスするために使用する資格情報を示します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="fa3a2-141">トークンは、[Secret Manager](xref:security/app-secrets#secret-manager) のヘルプにより、アプリの構成にリンクすることが可能な "シークレット" になります。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="fa3a2-142">Secret Manager は、*appsettings.json* などの構成ファイルのトークンに格納される、セキュリティがさらに向上した方法です。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fa3a2-143">Secret Manager は、開発目的のみのためのものです。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="fa3a2-144">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)により、Azure テストと運用のシークレットを格納し、保護することが可能です。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="fa3a2-145">「[Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets)」(ASP.NET Core での開発中にアプリのシークレットを安全に格納する) トピックの手順に従い、以下の各ログイン プロバイダーから割り当てられたトークンを格納できるようにします。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="fa3a2-146">アプリケーションに必要なログイン プロバイダーをセットアップする</span><span class="sxs-lookup"><span data-stu-id="fa3a2-146">Setup login providers required by your application</span></span>

<span data-ttu-id="fa3a2-147">各プロバイダーを使用するようにアプリケーションを構成するには、以下のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="fa3a2-148">[Facebook](xref:security/authentication/facebook-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="fa3a2-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="fa3a2-149">[Twitter](xref:security/authentication/twitter-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="fa3a2-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="fa3a2-150">[Google](xref:security/authentication/google-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="fa3a2-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="fa3a2-151">[Microsoft](xref:security/authentication/microsoft-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="fa3a2-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="fa3a2-152">[その他のプロバイダー](xref:security/authentication/otherlogins)の手順</span><span class="sxs-lookup"><span data-stu-id="fa3a2-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="fa3a2-153">必要に応じてパスワードを設定する</span><span class="sxs-lookup"><span data-stu-id="fa3a2-153">Optionally set password</span></span>

<span data-ttu-id="fa3a2-154">外部ログイン プロバイダーに登録するときに、アプリケーションにパスワードは登録していません。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="fa3a2-155">そのため、サイトのパスワードを作成し、記憶する作業は軽減されますが、外部ログイン プロバイダーに依存することにもなります。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="fa3a2-156">外部ログイン プロバイダーを使用できない場合、Web サイトにサインインすることができません。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="fa3a2-157">外部プロバイダーでのサインイン プロセス中に設定した電子メール アドレスを使用して、パスワードを作成し、サインインするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="fa3a2-158">右上にある **[Hello &lt; 電子メール エイリアス&gt;]** リンクを選択して**管理**ビューに移動します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Web アプリケーションの管理ビュー](index/_static/pass1a.png)

* <span data-ttu-id="fa3a2-160">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-160">Select **Create**</span></span>

![パスワード ページを設定する](index/_static/pass2a.png)

* <span data-ttu-id="fa3a2-162">有効なパスワードを設定します。そのパスワードと電子メール アドレスを使用してサインインできます。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="fa3a2-163">次の手順</span><span class="sxs-lookup"><span data-stu-id="fa3a2-163">Next steps</span></span>

* <span data-ttu-id="fa3a2-164">ログイン ボタンをカスタマイズする方法については、[こちらの GitHub イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/10563)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="fa3a2-165">このキーの順に押します。では、外部認証プロバイダーを紹介し、外部ログインを ASP.NET Core アプリケーションに追加するために必要な前提条件について説明しました。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="fa3a2-166">アプリケーションに必要なプロバイダーのログインを構成するには、各プロバイダーのページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="fa3a2-167">ユーザーとそのアクセス トークン許可および更新トークンに関する追加のデータを保持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="fa3a2-168">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa3a2-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
