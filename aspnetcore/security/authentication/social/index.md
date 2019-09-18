---
title: Facebook、Google、ASP.NET Core での外部プロバイダーの認証
author: rick-anderson
description: このチュートリアルでは、OAuth 2.0 と外部の認証プロバイダーを使用して ASP.NET Core 2.x アプリを構築する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082502"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="53672-103">Facebook、Google、ASP.NET Core での外部プロバイダーの認証</span><span class="sxs-lookup"><span data-stu-id="53672-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="53672-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="53672-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="53672-105">このチュートリアルでは、ユーザーが OAuth 2.0 と外部の認証プロバイダーの資格情報を使用してサインインできる、ASP.NET Core 2.2 アプリケーションを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="53672-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="53672-106">ここでは、[Facebook](xref:security/authentication/facebook-logins)、[Twitter](xref:security/authentication/twitter-logins)、[Google](xref:security/authentication/google-logins)、および [Microsoft](xref:security/authentication/microsoft-logins) の各プロバイダーを対象に説明します。</span><span class="sxs-lookup"><span data-stu-id="53672-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="53672-107">他のプロバイダーは、[AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers)、[AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) などのサードパーティ パッケージで利用できます。</span><span class="sxs-lookup"><span data-stu-id="53672-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Facebook、Twitter、Google+、Windows のソーシャル メディア アイコン](index/_static/social.png)

<span data-ttu-id="53672-109">既存の資格情報でユーザーがサインインできるようになると:</span><span class="sxs-lookup"><span data-stu-id="53672-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="53672-110">ユーザーにとって便利です。</span><span class="sxs-lookup"><span data-stu-id="53672-110">Is convenient for the users.</span></span>
* <span data-ttu-id="53672-111">サインイン プロセスの複雑な管理の多くが、サード パーティに移ります。</span><span class="sxs-lookup"><span data-stu-id="53672-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="53672-112">ソーシャル ログインによってトラフィックとユーザーの変換を促進する方法の例については、[Facebook](https://www.facebook.com/unsupportedbrowser) と [Twitter](https://dev.twitter.com/resources/case-studies) によるケース スタディを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53672-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="53672-113">新しい .NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="53672-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="53672-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="53672-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="53672-115">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="53672-115">Create a new project.</span></span>
* <span data-ttu-id="53672-116">**[ASP.NET Core Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="53672-117">**[プロジェクト名]** を指定して、 **[場所]** を確認または変更します。</span><span class="sxs-lookup"><span data-stu-id="53672-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="53672-118">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-118">Select **Create**.</span></span>
* <span data-ttu-id="53672-119">ドロップダウンから **[ASP.NET Core 2.2]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="53672-120">テンプレートの一覧で **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="53672-121">**[認証]** の下で、 **[変更]** を選択して認証を **[個人のユーザー アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="53672-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="53672-122">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-122">Select **OK**.</span></span>
* <span data-ttu-id="53672-123">**[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="53672-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="53672-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="53672-125">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="53672-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="53672-126">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="53672-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="53672-127">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="53672-127">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="53672-128">`dotnet new` コマンドでは、*WebApp1* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="53672-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="53672-129">`-uld` では、SQLite ではなく LocalDB を使用します。</span><span class="sxs-lookup"><span data-stu-id="53672-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="53672-130">`-uld` を省略して SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="53672-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="53672-131">`-au Individual` によって、個々の認証に対するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="53672-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="53672-132">`code` コマンドでは、Visual Studio Code の新しいインスタンス内に *WebApp1* フォルダーが開かれます。</span><span class="sxs-lookup"><span data-stu-id="53672-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="53672-133">"**ビルドとデバッグに必要な資産が 'WebApp1' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="53672-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="53672-134">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="53672-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="53672-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="53672-136">**[ファイル]** 、 **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="53672-137">サイドバーで **[.NET Core]**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="53672-138">**[Web アプリケーション]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-138">Select the **Web Application** template.</span></span> <span data-ttu-id="53672-139">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-139">Select **Next**.</span></span>
* <span data-ttu-id="53672-140">**[ターゲット フレームワーク]** ドロップダウンを **[.NET Core 2.2]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="53672-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="53672-141">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-141">Select **Next**.</span></span>
* <span data-ttu-id="53672-142">**[プロジェクト名]** を指定します。</span><span class="sxs-lookup"><span data-stu-id="53672-142">Provide a **Project Name**.</span></span> <span data-ttu-id="53672-143">**[場所]** を確認または変更します。</span><span class="sxs-lookup"><span data-stu-id="53672-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="53672-144">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="53672-145">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="53672-145">Apply migrations</span></span>

* <span data-ttu-id="53672-146">アプリを実行し、 **[登録]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="53672-147">新しいアカウントの電子メール アドレスとパスワードを入力し、 **[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="53672-148">指示に従って移行を適用します。</span><span class="sxs-lookup"><span data-stu-id="53672-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="53672-149">SecretManager を使用して、ログイン プロバイダーから割り当てられたトークンを格納する</span><span class="sxs-lookup"><span data-stu-id="53672-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="53672-150">ソーシャル ログイン プロバイダーは、登録プロセス中に**アプリケーション ID** トークンと**アプリケーション シークレット** トークンを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="53672-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="53672-151">完全なトークン名はプロバイダーにより異なります。</span><span class="sxs-lookup"><span data-stu-id="53672-151">The exact token names vary by provider.</span></span> <span data-ttu-id="53672-152">これらのトークンは、アプリが API にアクセスするために使用する資格情報を示します。</span><span class="sxs-lookup"><span data-stu-id="53672-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="53672-153">トークンは、[Secret Manager](xref:security/app-secrets#secret-manager) のヘルプにより、アプリの構成にリンクすることが可能な "シークレット" になります。</span><span class="sxs-lookup"><span data-stu-id="53672-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="53672-154">Secret Manager は、*appsettings.json* などの構成ファイルのトークンに格納される、セキュリティがさらに向上した方法です。</span><span class="sxs-lookup"><span data-stu-id="53672-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="53672-155">Secret Manager は、開発目的のみのためのものです。</span><span class="sxs-lookup"><span data-stu-id="53672-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="53672-156">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)により、Azure テストと運用のシークレットを格納し、保護することが可能です。</span><span class="sxs-lookup"><span data-stu-id="53672-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="53672-157">「[Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets)」(ASP.NET Core での開発中にアプリのシークレットを安全に格納する) トピックの手順に従い、以下の各ログイン プロバイダーから割り当てられたトークンを格納できるようにします。</span><span class="sxs-lookup"><span data-stu-id="53672-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="53672-158">アプリケーションに必要なログイン プロバイダーをセットアップする</span><span class="sxs-lookup"><span data-stu-id="53672-158">Setup login providers required by your application</span></span>

<span data-ttu-id="53672-159">各プロバイダーを使用するようにアプリケーションを構成するには、以下のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53672-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="53672-160">[Facebook](xref:security/authentication/facebook-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="53672-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="53672-161">[Twitter](xref:security/authentication/twitter-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="53672-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="53672-162">[Google](xref:security/authentication/google-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="53672-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="53672-163">[Microsoft](xref:security/authentication/microsoft-logins) の手順</span><span class="sxs-lookup"><span data-stu-id="53672-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="53672-164">[その他のプロバイダー](xref:security/authentication/otherlogins)の手順</span><span class="sxs-lookup"><span data-stu-id="53672-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="53672-165">必要に応じてパスワードを設定する</span><span class="sxs-lookup"><span data-stu-id="53672-165">Optionally set password</span></span>

<span data-ttu-id="53672-166">外部ログイン プロバイダーに登録するときに、アプリケーションにパスワードは登録していません。</span><span class="sxs-lookup"><span data-stu-id="53672-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="53672-167">そのため、サイトのパスワードを作成し、記憶する作業は軽減されますが、外部ログイン プロバイダーに依存することにもなります。</span><span class="sxs-lookup"><span data-stu-id="53672-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="53672-168">外部ログイン プロバイダーを使用できない場合、Web サイトにサインインすることができません。</span><span class="sxs-lookup"><span data-stu-id="53672-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="53672-169">外部プロバイダーでのサインイン プロセス中に設定した電子メール アドレスを使用して、パスワードを作成し、サインインするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="53672-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="53672-170">右上にある **[Hello &lt; 電子メール エイリアス&gt;]** リンクを選択して**管理**ビューに移動します。</span><span class="sxs-lookup"><span data-stu-id="53672-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Web アプリケーションの管理ビュー](index/_static/pass1a.png)

* <span data-ttu-id="53672-172">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="53672-172">Select **Create**</span></span>

![パスワード ページを設定する](index/_static/pass2a.png)

* <span data-ttu-id="53672-174">有効なパスワードを設定します。そのパスワードと電子メール アドレスを使用してサインインできます。</span><span class="sxs-lookup"><span data-stu-id="53672-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="53672-175">次の手順</span><span class="sxs-lookup"><span data-stu-id="53672-175">Next steps</span></span>

* <span data-ttu-id="53672-176">このキーの順に押します。では、外部認証プロバイダーを紹介し、外部ログインを ASP.NET Core アプリケーションに追加するために必要な前提条件について説明しました。</span><span class="sxs-lookup"><span data-stu-id="53672-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="53672-177">アプリケーションに必要なプロバイダーのログインを構成するには、各プロバイダーのページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53672-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="53672-178">ユーザーとそのアクセス トークン許可および更新トークンに関する追加のデータを保持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="53672-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="53672-179">詳細については、<xref:security/authentication/social/additional-claims> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53672-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
