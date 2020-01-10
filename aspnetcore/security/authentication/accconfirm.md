---
title: ASP.NET Core でのアカウントの確認とパスワードの回復
author: rick-anderson
description: 電子メールの確認とパスワードのリセットを使用して ASP.NET Core アプリを構築する方法について説明します。
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 49d3d214fd64edc5b17df2df929ddc3c2af47ede
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829271"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="dd59b-103">ASP.NET Core でのアカウントの確認とパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="dd59b-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="dd59b-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)、 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="dd59b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="dd59b-105">このチュートリアルでは、電子メールの確認とパスワードのリセットを使用して ASP.NET Core アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="dd59b-106">このチュートリアルは最初のトピックでは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="dd59b-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="dd59b-107">理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-107">You should be familiar with:</span></span>

* [<span data-ttu-id="dd59b-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd59b-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="dd59b-109">認証</span><span class="sxs-lookup"><span data-stu-id="dd59b-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="dd59b-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="dd59b-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="dd59b-111">ASP.NET Core 1.1 バージョンについては、[この PDF ファイル](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="dd59b-112">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="dd59b-112">Prerequisites</span></span>

[<span data-ttu-id="dd59b-113">.NET Core 3.0 SDK 以降</span><span class="sxs-lookup"><span data-stu-id="dd59b-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="dd59b-114">認証を使用した web アプリの作成とテスト</span><span class="sxs-lookup"><span data-stu-id="dd59b-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="dd59b-115">認証を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="dd59b-116">アプリを実行し、 **[登録]** リンクを選択して、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="dd59b-117">登録されると、[to `/Identity/Account/RegisterConfirmation`] ページにリダイレクトされます。このページには、電子メールの確認をシミュレートするためのリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="dd59b-118">[`Click here to confirm your account`] リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="dd59b-119">**ログイン**リンクを選択し、同じ資格情報でサインインします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="dd59b-120">[`Hello YourEmail@provider.com!`] リンクを選択すると、[`/Identity/Account/Manage/PersonalData`] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="dd59b-121">左側の **[Personal data]** タブを選択し、 **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="dd59b-122">電子メールプロバイダーを構成する</span><span class="sxs-lookup"><span data-stu-id="dd59b-122">Configure an email provider</span></span>

<span data-ttu-id="dd59b-123">このチュートリアルでは、 [Sendgrid](https://sendgrid.com)を使用して電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="dd59b-124">電子メールを送信するには、SendGrid アカウントとキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="dd59b-125">他の電子メールプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-125">You can use other email providers.</span></span> <span data-ttu-id="dd59b-126">SendGrid または別の電子メールサービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="dd59b-127">SMTP のセキュリティを保護し、正しく設定することは困難です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="dd59b-128">セキュリティで保護された電子メールキーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="dd59b-129">このサンプルでは、*サービス/認証の Enderoptions を作成します。*</span><span class="sxs-lookup"><span data-stu-id="dd59b-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="dd59b-130">SendGrid ユーザーシークレットの構成</span><span class="sxs-lookup"><span data-stu-id="dd59b-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="dd59b-131">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、`SendGridUser` と `SendGridKey` を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="dd59b-132">例:</span><span class="sxs-lookup"><span data-stu-id="dd59b-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="dd59b-133">Windows では、シークレットマネージャーは、キーと値のペアを `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` ディレクトリにある*シークレットの json*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="dd59b-134">*シークレットの json*ファイルの内容は暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="dd59b-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="dd59b-135">次のマークアップは、*シークレットの json*ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="dd59b-136">`SendGridKey` 値が削除されました。</span><span class="sxs-lookup"><span data-stu-id="dd59b-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="dd59b-137">詳細については、「パターンと[構成](xref:fundamentals/configuration/index)の[オプション](xref:fundamentals/configuration/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="dd59b-138">SendGrid のインストール</span><span class="sxs-lookup"><span data-stu-id="dd59b-138">Install SendGrid</span></span>

<span data-ttu-id="dd59b-139">このチュートリアルでは、 [Sendgrid](https://sendgrid.com/)を使用して電子メール通知を追加する方法について説明しますが、SMTP などのメカニズムを使用して電子メールを送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="dd59b-140">`SendGrid` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd59b-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd59b-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd59b-142">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="dd59b-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="dd59b-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="dd59b-144">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="dd59b-145">無料の sendgrid アカウントを登録するには、「 [SendGrid を無料で開始](https://sendgrid.com/free/)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="dd59b-146">IEmailSender を実装する</span><span class="sxs-lookup"><span data-stu-id="dd59b-146">Implement IEmailSender</span></span>

<span data-ttu-id="dd59b-147">`IEmailSender`を実装するには、次のようなコードを使用して*サービス/EmailSender .cs*を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="dd59b-148">電子メールをサポートするスタートアップの構成</span><span class="sxs-lookup"><span data-stu-id="dd59b-148">Configure startup to support email</span></span>

<span data-ttu-id="dd59b-149">*Startup.cs*ファイルの `ConfigureServices` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="dd59b-150">`EmailSender` を一時的なサービスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="dd59b-151">`AuthMessageSenderOptions` 構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="dd59b-152">登録、電子メールの確認、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="dd59b-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="dd59b-153">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="dd59b-154">アプリを実行し、新しいユーザーの登録</span><span class="sxs-lookup"><span data-stu-id="dd59b-154">Run the app and register a new user</span></span>
* <span data-ttu-id="dd59b-155">アカウントの確認リンクについては、電子メールを確認してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="dd59b-156">電子メールを受信しない場合は、「[デバッグ電子メール](#debug)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="dd59b-157">リンクをクリックして、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="dd59b-158">電子メールとパスワードを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="dd59b-159">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="dd59b-160">パスワードリセットのテスト</span><span class="sxs-lookup"><span data-stu-id="dd59b-160">Test password reset</span></span>

* <span data-ttu-id="dd59b-161">サインインしている場合は、 **[ログアウト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="dd59b-162">**[ログイン]** リンクを選択し、 **[パスワードを忘れ]** た場合 リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="dd59b-163">アカウントの登録に使用した電子メールを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="dd59b-164">パスワードをリセットするためのリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="dd59b-165">メールを確認し、リンクをクリックしてパスワードをリセットします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="dd59b-166">パスワードが正常にリセットされたら、電子メールと新しいパスワードでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="dd59b-167">電子メールとアクティビティのタイムアウトを変更する</span><span class="sxs-lookup"><span data-stu-id="dd59b-167">Change email and activity timeout</span></span>

<span data-ttu-id="dd59b-168">既定の非アクティブタイムアウトは14日です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="dd59b-169">次のコードは、非アクティブタイムアウトを5日間に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="dd59b-170">すべてのデータ保護トークンの lifespans を変更する</span><span class="sxs-lookup"><span data-stu-id="dd59b-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="dd59b-171">次のコードでは、すべてのデータ保護トークンのタイムアウト期間を3時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="dd59b-172">組み込みの Id ユーザートークン (「 [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions .cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 」を参照) には、 [1 日のタイムアウト](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)があります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="dd59b-173">電子メールトークンの有効期間を変更する</span><span class="sxs-lookup"><span data-stu-id="dd59b-173">Change the email token lifespan</span></span>

<span data-ttu-id="dd59b-174">[Id ユーザートークン](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)の既定のトークン有効期間は[1 日](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="dd59b-175">このセクションでは、電子メールトークンの有効期間を変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="dd59b-176">カスタムの[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)と <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>を追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="dd59b-177">カスタムプロバイダーをサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="dd59b-178">電子メールの再送信の確認</span><span class="sxs-lookup"><span data-stu-id="dd59b-178">Resend email confirmation</span></span>

<span data-ttu-id="dd59b-179">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5410)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="dd59b-180">デバッグ用電子メール</span><span class="sxs-lookup"><span data-stu-id="dd59b-180">Debug email</span></span>

<span data-ttu-id="dd59b-181">電子メールが機能しない場合:</span><span class="sxs-lookup"><span data-stu-id="dd59b-181">If you can't get email working:</span></span>

* <span data-ttu-id="dd59b-182">`EmailSender.Execute` にブレークポイントを設定し `SendGridClient.SendEmailAsync` が呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="dd59b-183">同様のコードを使用して `EmailSender.Execute`に[電子メールを送信するコンソールアプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="dd59b-184">[[電子メール活動](https://sendgrid.com/docs/User_Guide/email_activity.html)] ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="dd59b-185">迷惑メールフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-185">Check your spam folder.</span></span>
* <span data-ttu-id="dd59b-186">別の電子メールプロバイダー (Microsoft、Yahoo、Gmail など) で別の電子メールエイリアスを試してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="dd59b-187">別の電子メールアカウントに送信してみてください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="dd59b-188">**セキュリティのベストプラクティス**として、テストと開発では運用シークレットを使用し**ないこと**をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="dd59b-189">アプリを Azure に発行する場合は、Azure Web アプリポータルで SendGrid シークレットをアプリケーション設定として設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="dd59b-190">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="dd59b-191">ソーシャルおよびローカルログインアカウントの結合</span><span class="sxs-lookup"><span data-stu-id="dd59b-191">Combine social and local login accounts</span></span>

<span data-ttu-id="dd59b-192">このセクションを完了するには、まず外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="dd59b-193">「 [Facebook、Google、および外部プロバイダーの認証](xref:security/authentication/social/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="dd59b-194">電子メールリンクをクリックして、ローカルアカウントとソーシャルアカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="dd59b-195">次のシーケンスでは、"RickAndMSFT@gmail.com" がローカルログインとして最初に作成されます。ただし、まず、アカウントをソーシャルログインとして作成してから、ローカルログインを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション: RickAndMSFT@gmail.com ユーザー認証済み](accconfirm/_static/rick.png)

<span data-ttu-id="dd59b-197">**[管理]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-197">Click on the **Manage** link.</span></span> <span data-ttu-id="dd59b-198">このアカウントに関連付けられている0外部 (ソーシャルログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-198">Note the 0 external (social logins) associated with this account.</span></span>

![ビューの管理](accconfirm/_static/manage.png)

<span data-ttu-id="dd59b-200">別のログインサービスへのリンクをクリックし、アプリの要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="dd59b-201">次の図では、Facebook が外部認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="dd59b-201">In the following image, Facebook is the external authentication provider:</span></span>

![外部ログインビューを管理する Facebook の一覧表示](accconfirm/_static/fb.png)

<span data-ttu-id="dd59b-203">2つのアカウントが結合されています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-203">The two accounts have been combined.</span></span> <span data-ttu-id="dd59b-204">いずれかのアカウントでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-204">You are able to sign in with either account.</span></span> <span data-ttu-id="dd59b-205">ソーシャルログイン認証サービスがダウンした場合、またはソーシャルアカウントへのアクセスが失われた可能性がある場合は、ユーザーにローカルアカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="dd59b-206">サイトにユーザーがいる場合のアカウントの確認を有効にする</span><span class="sxs-lookup"><span data-stu-id="dd59b-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="dd59b-207">ユーザーがサイトでアカウントの確認を有効にすると、すべての既存のユーザーがロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="dd59b-208">アカウントが確認されていないため、既存のユーザーがロックアウトされています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="dd59b-209">既存のユーザーロックアウトを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="dd59b-210">データベースを更新して、すべての既存のユーザーを確認済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="dd59b-211">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-211">Confirm existing users.</span></span> <span data-ttu-id="dd59b-212">たとえば、確認リンクを含む電子メールをバッチ送信します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="dd59b-213">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="dd59b-213">Prerequisites</span></span>

[<span data-ttu-id="dd59b-214">.NET Core 2.2 SDK 以降</span><span class="sxs-lookup"><span data-stu-id="dd59b-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="dd59b-215">Web アプリとスキャフォールディング Id を作成する</span><span class="sxs-lookup"><span data-stu-id="dd59b-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="dd59b-216">認証を使用して web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-216">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="dd59b-217">新しいユーザー登録をテストする</span><span class="sxs-lookup"><span data-stu-id="dd59b-217">Test new user registration</span></span>

<span data-ttu-id="dd59b-218">アプリを実行し、 **[登録]** リンクを選択して、ユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="dd59b-219">この時点で、電子メールの検証は、 [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性を使用した場合のみです。</span><span class="sxs-lookup"><span data-stu-id="dd59b-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="dd59b-220">登録を送信すると、アプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="dd59b-221">このチュートリアルの後半では、電子メールが検証されるまで新しいユーザーがサインインできないように、コードが更新されています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="dd59b-222">テーブルの `EmailConfirmed` フィールドが `False`であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="dd59b-223">アプリが確認の電子メールを送信するときに、次の手順でもう一度このメールを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="dd59b-224">行を右クリックし、 **[削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="dd59b-225">電子メールエイリアスを削除すると、次の手順が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="dd59b-226">電子メールの確認が必要</span><span class="sxs-lookup"><span data-stu-id="dd59b-226">Require email confirmation</span></span>

<span data-ttu-id="dd59b-227">新しいユーザー登録の電子メールを確認することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="dd59b-228">電子メールの確認では、他のユーザーの権限を借用していないこと (つまり、他のユーザーの電子メールに登録されていないこと) を確認できます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="dd59b-229">ディスカッションフォーラムがあり、"yli@example.com" が "nolivetto@contoso.com" として登録されないようにしたいとします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="dd59b-230">電子メールを確認しないと、"nolivetto@contoso.com" はアプリから不要な電子メールを受信する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="dd59b-231">ユーザーが誤って "ylo@example.com" として登録され、"yli" のスペルミスに気付かないとします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="dd59b-232">アプリには正しい電子メールがないため、パスワードの回復を使用できません。</span><span class="sxs-lookup"><span data-stu-id="dd59b-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="dd59b-233">電子メールの確認では、ボットからの保護が制限されています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="dd59b-234">電子メールの確認では、多くの電子メールアカウントを持つ悪意のあるユーザーからの保護は提供されません。</span><span class="sxs-lookup"><span data-stu-id="dd59b-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="dd59b-235">通常は、確認済みの電子メールを送信する前に、新しいユーザーが web サイトにデータを投稿できないようにします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="dd59b-236">確認済みの電子メールを要求するように `Startup.ConfigureServices` を更新します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="dd59b-237">`config.SignIn.RequireConfirmedEmail = true;` は、登録ユーザーが電子メールを確認するまでログインできないようにします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="dd59b-238">電子メールプロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="dd59b-238">Configure email provider</span></span>

<span data-ttu-id="dd59b-239">このチュートリアルでは、 [Sendgrid](https://sendgrid.com)を使用して電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="dd59b-240">電子メールを送信するには、SendGrid アカウントとキーが必要です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="dd59b-241">他の電子メールプロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-241">You can use other email providers.</span></span> <span data-ttu-id="dd59b-242">ASP.NET Core 2.x には `System.Net.Mail`が含まれています。これにより、アプリから電子メールを送信できます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="dd59b-243">SendGrid または別の電子メールサービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="dd59b-244">SMTP のセキュリティを保護し、正しく設定することは困難です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="dd59b-245">セキュリティで保護された電子メールキーを取得するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="dd59b-246">このサンプルでは、*サービス/認証の Enderoptions を作成します。*</span><span class="sxs-lookup"><span data-stu-id="dd59b-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="dd59b-247">SendGrid ユーザーシークレットの構成</span><span class="sxs-lookup"><span data-stu-id="dd59b-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="dd59b-248">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、`SendGridUser` と `SendGridKey` を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="dd59b-249">例:</span><span class="sxs-lookup"><span data-stu-id="dd59b-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="dd59b-250">Windows では、シークレットマネージャーは、キーと値のペアを `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` ディレクトリにある*シークレットの json*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="dd59b-251">*シークレットの json*ファイルの内容は暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="dd59b-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="dd59b-252">次のマークアップは、*シークレットの json*ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="dd59b-253">`SendGridKey` 値が削除されました。</span><span class="sxs-lookup"><span data-stu-id="dd59b-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="dd59b-254">詳細については、「パターンと[構成](xref:fundamentals/configuration/index)の[オプション](xref:fundamentals/configuration/options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="dd59b-255">SendGrid のインストール</span><span class="sxs-lookup"><span data-stu-id="dd59b-255">Install SendGrid</span></span>

<span data-ttu-id="dd59b-256">このチュートリアルでは、 [Sendgrid](https://sendgrid.com/)を使用して電子メール通知を追加する方法について説明しますが、SMTP などのメカニズムを使用して電子メールを送信することもできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="dd59b-257">`SendGrid` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dd59b-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd59b-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd59b-259">パッケージマネージャーコンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="dd59b-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="dd59b-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="dd59b-261">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="dd59b-262">無料の sendgrid アカウントを登録するには、「 [SendGrid を無料で開始](https://sendgrid.com/free/)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="dd59b-263">IEmailSender を実装する</span><span class="sxs-lookup"><span data-stu-id="dd59b-263">Implement IEmailSender</span></span>

<span data-ttu-id="dd59b-264">`IEmailSender`を実装するには、次のようなコードを使用して*サービス/EmailSender .cs*を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="dd59b-265">電子メールをサポートするスタートアップの構成</span><span class="sxs-lookup"><span data-stu-id="dd59b-265">Configure startup to support email</span></span>

<span data-ttu-id="dd59b-266">*Startup.cs*ファイルの `ConfigureServices` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="dd59b-267">`EmailSender` を一時的なサービスとして追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="dd59b-268">`AuthMessageSenderOptions` 構成インスタンスを登録します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="dd59b-269">アカウントの確認とパスワードの回復を有効にする</span><span class="sxs-lookup"><span data-stu-id="dd59b-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="dd59b-270">このテンプレートには、アカウントの確認とパスワードの回復のためのコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="dd59b-271">[区分]、[ *id*]、[ページ]、[アカウント]、[登録] の順に `OnPostAsync` 方法を見つけます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="dd59b-272">新しく登録されたユーザーが自動的にサインインしないようにするには、次の行をコメントアウトします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="dd59b-273">完全なメソッドは、変更された行が強調表示された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="dd59b-274">登録、電子メールの確認、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="dd59b-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="dd59b-275">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="dd59b-276">アプリを実行し、新しいユーザーの登録</span><span class="sxs-lookup"><span data-stu-id="dd59b-276">Run the app and register a new user</span></span>
* <span data-ttu-id="dd59b-277">アカウントの確認リンクについては、電子メールを確認してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="dd59b-278">電子メールを受信しない場合は、「[デバッグ電子メール](#debug)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="dd59b-279">リンクをクリックして、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="dd59b-280">電子メールとパスワードを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="dd59b-281">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="dd59b-282">[管理] ページを表示する</span><span class="sxs-lookup"><span data-stu-id="dd59b-282">View the manage page</span></span>

<span data-ttu-id="dd59b-283">ブラウザーでユーザー名を選択します。ユーザー名を ![ブラウザーウィンドウ](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="dd59b-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="dd59b-284">管理 ページが、**プロファイル** タブが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="dd59b-285">電子メールには、電子メールが確認されたことを示すチェックボックス**が表示さ**れます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="dd59b-286">パスワードリセットのテスト</span><span class="sxs-lookup"><span data-stu-id="dd59b-286">Test password reset</span></span>

* <span data-ttu-id="dd59b-287">サインインしている場合は、 **[ログアウト]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="dd59b-288">**[ログイン]** リンクを選択し、 **[パスワードを忘れ]** た場合 リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="dd59b-289">アカウントの登録に使用した電子メールを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="dd59b-290">パスワードをリセットするためのリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="dd59b-291">メールを確認し、リンクをクリックしてパスワードをリセットします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="dd59b-292">パスワードが正常にリセットされたら、電子メールと新しいパスワードでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="dd59b-293">電子メールとアクティビティのタイムアウトを変更する</span><span class="sxs-lookup"><span data-stu-id="dd59b-293">Change email and activity timeout</span></span>

<span data-ttu-id="dd59b-294">既定の非アクティブタイムアウトは14日です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="dd59b-295">次のコードは、非アクティブタイムアウトを5日間に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="dd59b-296">すべてのデータ保護トークンの lifespans を変更する</span><span class="sxs-lookup"><span data-stu-id="dd59b-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="dd59b-297">次のコードでは、すべてのデータ保護トークンのタイムアウト期間を3時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="dd59b-298">組み込みの Id ユーザートークン (「 [AspNetCore/src/Identity/Extensions. Core/src/TokenOptions .cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 」を参照) には、 [1 日のタイムアウト](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)があります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="dd59b-299">電子メールトークンの有効期間を変更する</span><span class="sxs-lookup"><span data-stu-id="dd59b-299">Change the email token lifespan</span></span>

<span data-ttu-id="dd59b-300">[Id ユーザートークン](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)の既定のトークン有効期間は[1 日](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)です。</span><span class="sxs-lookup"><span data-stu-id="dd59b-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="dd59b-301">このセクションでは、電子メールトークンの有効期間を変更する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="dd59b-302">カスタムの[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)と <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>を追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="dd59b-303">カスタムプロバイダーをサービスコンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="dd59b-304">電子メールの再送信の確認</span><span class="sxs-lookup"><span data-stu-id="dd59b-304">Resend email confirmation</span></span>

<span data-ttu-id="dd59b-305">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5410)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="dd59b-306">デバッグ用電子メール</span><span class="sxs-lookup"><span data-stu-id="dd59b-306">Debug email</span></span>

<span data-ttu-id="dd59b-307">電子メールが機能しない場合:</span><span class="sxs-lookup"><span data-stu-id="dd59b-307">If you can't get email working:</span></span>

* <span data-ttu-id="dd59b-308">`EmailSender.Execute` にブレークポイントを設定し `SendGridClient.SendEmailAsync` が呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="dd59b-309">同様のコードを使用して `EmailSender.Execute`に[電子メールを送信するコンソールアプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="dd59b-310">[[電子メール活動](https://sendgrid.com/docs/User_Guide/email_activity.html)] ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="dd59b-311">迷惑メールフォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-311">Check your spam folder.</span></span>
* <span data-ttu-id="dd59b-312">別の電子メールプロバイダー (Microsoft、Yahoo、Gmail など) で別の電子メールエイリアスを試してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="dd59b-313">別の電子メールアカウントに送信してみてください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="dd59b-314">**セキュリティのベストプラクティス**として、テストと開発では運用シークレットを使用し**ないこと**をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="dd59b-315">アプリを Azure に発行する場合は、Azure Web アプリポータルで SendGrid シークレットをアプリケーション設定として設定できます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="dd59b-316">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="dd59b-317">ソーシャルおよびローカルログインアカウントの結合</span><span class="sxs-lookup"><span data-stu-id="dd59b-317">Combine social and local login accounts</span></span>

<span data-ttu-id="dd59b-318">このセクションを完了するには、まず外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd59b-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="dd59b-319">「 [Facebook、Google、および外部プロバイダーの認証](xref:security/authentication/social/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="dd59b-320">電子メールリンクをクリックして、ローカルアカウントとソーシャルアカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="dd59b-321">次のシーケンスでは、"RickAndMSFT@gmail.com" がローカルログインとして最初に作成されます。ただし、まず、アカウントをソーシャルログインとして作成してから、ローカルログインを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション: RickAndMSFT@gmail.com ユーザー認証済み](accconfirm/_static/rick.png)

<span data-ttu-id="dd59b-323">**[管理]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-323">Click on the **Manage** link.</span></span> <span data-ttu-id="dd59b-324">このアカウントに関連付けられている0外部 (ソーシャルログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="dd59b-324">Note the 0 external (social logins) associated with this account.</span></span>

![ビューの管理](accconfirm/_static/manage.png)

<span data-ttu-id="dd59b-326">別のログインサービスへのリンクをクリックし、アプリの要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="dd59b-327">次の図では、Facebook が外部認証プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="dd59b-327">In the following image, Facebook is the external authentication provider:</span></span>

![外部ログインビューを管理する Facebook の一覧表示](accconfirm/_static/fb.png)

<span data-ttu-id="dd59b-329">2つのアカウントが結合されています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-329">The two accounts have been combined.</span></span> <span data-ttu-id="dd59b-330">いずれかのアカウントでサインインできます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-330">You are able to sign in with either account.</span></span> <span data-ttu-id="dd59b-331">ソーシャルログイン認証サービスがダウンした場合、またはソーシャルアカウントへのアクセスが失われた可能性がある場合は、ユーザーにローカルアカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="dd59b-332">サイトにユーザーがいる場合のアカウントの確認を有効にする</span><span class="sxs-lookup"><span data-stu-id="dd59b-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="dd59b-333">ユーザーがサイトでアカウントの確認を有効にすると、すべての既存のユーザーがロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="dd59b-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="dd59b-334">アカウントが確認されていないため、既存のユーザーがロックアウトされています。</span><span class="sxs-lookup"><span data-stu-id="dd59b-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="dd59b-335">既存のユーザーロックアウトを回避するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="dd59b-336">データベースを更新して、すべての既存のユーザーを確認済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="dd59b-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="dd59b-337">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-337">Confirm existing users.</span></span> <span data-ttu-id="dd59b-338">たとえば、確認リンクを含む電子メールをバッチ送信します。</span><span class="sxs-lookup"><span data-stu-id="dd59b-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
