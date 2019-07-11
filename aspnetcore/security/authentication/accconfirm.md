---
title: アカウントの確認と ASP.NET Core でのパスワードの回復
author: rick-anderson
description: 電子メールの確認とパスワードのリセットと ASP.NET Core アプリを構築する方法について説明します。
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 802ba446af04df6a35ac73187ad693b8ec80c654
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814847"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="bf125-103">アカウントの確認と ASP.NET Core でのパスワードの回復</span><span class="sxs-lookup"><span data-stu-id="bf125-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="bf125-104">によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)、および[Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="bf125-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="bf125-105">このチュートリアルでは、電子メールの確認とパスワードのリセットと ASP.NET Core アプリをビルドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bf125-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="bf125-106">このチュートリアルは**いない**先頭トピック。</span><span class="sxs-lookup"><span data-stu-id="bf125-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="bf125-107">理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf125-107">You should be familiar with:</span></span>

* [<span data-ttu-id="bf125-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf125-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="bf125-109">認証</span><span class="sxs-lookup"><span data-stu-id="bf125-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="bf125-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="bf125-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="bf125-111">参照してください[この PDF ファイル](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)ASP.NET Core 1.1 バージョン。</span><span class="sxs-lookup"><span data-stu-id="bf125-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="bf125-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bf125-112">Prerequisites</span></span>

[<span data-ttu-id="bf125-113">.NET core 3.0 SDK またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="bf125-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="bf125-114">作成し、認証を使用した web アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="bf125-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="bf125-115">認証を使用した web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf125-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="bf125-116">アプリを実行し、選択、**登録**リンク、およびユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="bf125-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="bf125-117">登録されると、リダイレクトされるの`/Identity/Account/RegisterConfirmation`シミュレート確認の電子メールへのリンクを含むページ。</span><span class="sxs-lookup"><span data-stu-id="bf125-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="bf125-118">選択、`Click here to confirm your account`リンク。</span><span class="sxs-lookup"><span data-stu-id="bf125-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="bf125-119">選択、**ログイン**リンクと同じ資格情報でサインインします。</span><span class="sxs-lookup"><span data-stu-id="bf125-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="bf125-120">選択、`Hello YourEmail@provider.com!`リンクをリダイレクトする、`/Identity/Account/Manage/PersonalData`ページ。</span><span class="sxs-lookup"><span data-stu-id="bf125-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="bf125-121">選択、**個人データ**、左側のタブを選び**削除**します。</span><span class="sxs-lookup"><span data-stu-id="bf125-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="bf125-122">電子メール プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-122">Configure an email provider</span></span>

<span data-ttu-id="bf125-123">このチュートリアルで[SendGrid](https://sendgrid.com)電子メールを送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="bf125-124">SendGrid アカウントとキーが電子メールを送信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf125-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="bf125-125">その他の電子メール プロバイダーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-125">You can use other email providers.</span></span> <span data-ttu-id="bf125-126">SendGrid または別の電子メール サービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bf125-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="bf125-127">SMTP では、セキュリティで保護し、設定を正しく困難です。</span><span class="sxs-lookup"><span data-stu-id="bf125-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="bf125-128">電子メールをセキュリティで保護されたキーを取得するためのクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="bf125-129">このサンプルでは、次のように作成します*Services/AuthMessageSenderOptions.cs*:。</span><span class="sxs-lookup"><span data-stu-id="bf125-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="bf125-130">SendGrid のユーザーの機密情報を構成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="bf125-131">設定、`SendGridUser`と`SendGridKey`で、 [secret manager ツール](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="bf125-132">例:</span><span class="sxs-lookup"><span data-stu-id="bf125-132">For example:</span></span>

```console
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="bf125-133">Secret Manager、Windows 上のキー/値のペアが格納、 *secrets.json*ファイル、`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="bf125-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="bf125-134">内容、 *secrets.json*ファイルは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="bf125-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="bf125-135">次のマークアップに示す、 *secrets.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="bf125-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="bf125-136">`SendGridKey`値が削除されました。</span><span class="sxs-lookup"><span data-stu-id="bf125-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="bf125-137">詳細については、次を参照してください。、[オプション パターン](xref:fundamentals/configuration/options)と[構成](xref:fundamentals/configuration/index)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="bf125-138">SendGrid をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bf125-138">Install SendGrid</span></span>

<span data-ttu-id="bf125-139">このチュートリアルは、使用して電子メール通知を追加する方法を示します[SendGrid](https://sendgrid.com/)が SMTP およびその他のメカニズムを使用して電子メールを送信できます。</span><span class="sxs-lookup"><span data-stu-id="bf125-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="bf125-140">インストール、 `SendGrid` NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="bf125-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf125-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf125-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf125-142">パッケージ マネージャー コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="bf125-142">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bf125-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bf125-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bf125-144">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="bf125-144">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="bf125-145">参照してください[SendGrid を無料で開始する](https://sendgrid.com/free/)無料の SendGrid アカウントを登録します。</span><span class="sxs-lookup"><span data-stu-id="bf125-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="bf125-146">IEmailSender を実装します。</span><span class="sxs-lookup"><span data-stu-id="bf125-146">Implement IEmailSender</span></span>

<span data-ttu-id="bf125-147">実装`IEmailSender`、作成*Services/EmailSender.cs*次のようなコードで。</span><span class="sxs-lookup"><span data-stu-id="bf125-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="bf125-148">電子メールをサポートするために起動時を構成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-148">Configure startup to support email</span></span>

<span data-ttu-id="bf125-149">次のコードを追加、`ConfigureServices`メソッドで、 *Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="bf125-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="bf125-150">追加`EmailSender`一時的なサービスとして。</span><span class="sxs-lookup"><span data-stu-id="bf125-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="bf125-151">登録、`AuthMessageSenderOptions`構成インスタンス。</span><span class="sxs-lookup"><span data-stu-id="bf125-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="bf125-152">登録、確認の電子メール、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="bf125-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="bf125-153">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="bf125-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="bf125-154">アプリを実行し、新しいユーザーの登録</span><span class="sxs-lookup"><span data-stu-id="bf125-154">Run the app and register a new user</span></span>
* <span data-ttu-id="bf125-155">アカウント確認用のリンクは、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf125-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="bf125-156">参照してください[デバッグ電子メール](#debug)電子メールが届かない場合。</span><span class="sxs-lookup"><span data-stu-id="bf125-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="bf125-157">電子メールを確認するリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bf125-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="bf125-158">電子メール アドレスとパスワードでサインインします。</span><span class="sxs-lookup"><span data-stu-id="bf125-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="bf125-159">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="bf125-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="bf125-160">テストのパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="bf125-160">Test password reset</span></span>

* <span data-ttu-id="bf125-161">サインインしている場合は、選択**ログアウト**します。</span><span class="sxs-lookup"><span data-stu-id="bf125-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="bf125-162">選択、**ログイン**リンクし、選択、**パスワードを忘れた場合でしょうか。** リンク。</span><span class="sxs-lookup"><span data-stu-id="bf125-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="bf125-163">アカウントを登録するために使用する電子メール アドレスを入力します。</span><span class="sxs-lookup"><span data-stu-id="bf125-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="bf125-164">パスワードをリセットするリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="bf125-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="bf125-165">電子メールを確認し、パスワードをリセットするリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bf125-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="bf125-166">パスワードが正常にリセットされると後、はの電子メール アドレスと新しいパスワードでサインインすることができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="bf125-167">電子メールとアクティビティのタイムアウトを変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-167">Change email and activity timeout</span></span>

<span data-ttu-id="bf125-168">既定のアイドル タイムアウトは、14 日間です。</span><span class="sxs-lookup"><span data-stu-id="bf125-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="bf125-169">次のコードでは、アイドル タイムアウトを 5 日に設定します。</span><span class="sxs-lookup"><span data-stu-id="bf125-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="bf125-170">すべてのデータの保護トークン lifespans を変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="bf125-171">次のコードは、すべてのデータの保護トークンのタイムアウト期間を 3 時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="bf125-172">組み込みの Id のユーザー トークン (を参照してください[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) が、 [1 日のタイムアウト](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="bf125-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="bf125-173">電子メールのトークン有効期間を変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-173">Change the email token lifespan</span></span>

<span data-ttu-id="bf125-174">既定のトークン有効期間[Id のユーザー トークン](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)は[1 日](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="bf125-175">このセクションでは、電子メールのトークン有効期間を変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bf125-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="bf125-176">追加のカスタム[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)と<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="bf125-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="bf125-177">サービス コンテナーには、カスタム プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="bf125-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="bf125-178">確認の電子メールを再送信します。</span><span class="sxs-lookup"><span data-stu-id="bf125-178">Resend email confirmation</span></span>

<span data-ttu-id="bf125-179">参照してください[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5410)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="bf125-180">電子メールをデバッグします。</span><span class="sxs-lookup"><span data-stu-id="bf125-180">Debug email</span></span>

<span data-ttu-id="bf125-181">電子メールの作業が発生したことはできません: 場合</span><span class="sxs-lookup"><span data-stu-id="bf125-181">If you can't get email working:</span></span>

* <span data-ttu-id="bf125-182">ブレークポイントを設定`EmailSender.Execute`を確認する`SendGridClient.SendEmailAsync`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bf125-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="bf125-183">作成、[電子メールを送信するためのコンソール アプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)に同様のコードを使用して`EmailSender.Execute`します。</span><span class="sxs-lookup"><span data-stu-id="bf125-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="bf125-184">レビュー、[電子メール アクティビティ](https://sendgrid.com/docs/User_Guide/email_activity.html)ページ。</span><span class="sxs-lookup"><span data-stu-id="bf125-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="bf125-185">迷惑メール フォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf125-185">Check your spam folder.</span></span>
* <span data-ttu-id="bf125-186">別のメール プロバイダー (Microsoft、Yahoo、Gmail など) に別の電子メール エイリアスをお試しください。</span><span class="sxs-lookup"><span data-stu-id="bf125-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="bf125-187">別のメール アカウントへの送信を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="bf125-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="bf125-188">**セキュリティのベスト プラクティス**は**いない**テストと開発における運用シークレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="bf125-189">アプリを Azure に発行する場合は、Web アプリを Azure portal でアプリケーション設定と SendGrid シークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="bf125-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="bf125-190">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="bf125-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="bf125-191">ソーシャル、ローカルのログイン アカウントを組み合わせる</span><span class="sxs-lookup"><span data-stu-id="bf125-191">Combine social and local login accounts</span></span>

<span data-ttu-id="bf125-192">このセクションを完了するには、まず、外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf125-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="bf125-193">参照してください[Facebook、Google、および外部プロバイダー認証](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="bf125-194">電子メールのリンクをクリックして、ローカルおよびソーシャル アカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="bf125-195">次の順序で"RickAndMSFT@gmail.com"が最初に、ローカルのログインとして作成ただし、最初に、ソーシャル ログインとしてアカウントを作成し、ローカル ログインを追加します。</span><span class="sxs-lookup"><span data-stu-id="bf125-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション:RickAndMSFT@gmail.com認証されたユーザー](accconfirm/_static/rick.png)

<span data-ttu-id="bf125-197">をクリックして、**管理**リンク。</span><span class="sxs-lookup"><span data-stu-id="bf125-197">Click on the **Manage** link.</span></span> <span data-ttu-id="bf125-198">このアカウントに関連付けられた 0 外部 (ソーシャル ログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="bf125-198">Note the 0 external (social logins) associated with this account.</span></span>

![ビューを管理します。](accconfirm/_static/manage.png)

<span data-ttu-id="bf125-200">別のログイン サービスへのリンクをクリックし、アプリの要求をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="bf125-201">次の図では、Facebook は、外部認証プロバイダーは。</span><span class="sxs-lookup"><span data-stu-id="bf125-201">In the following image, Facebook is the external authentication provider:</span></span>

![Facebook を一覧表示する、外部ログイン ビューを管理します。](accconfirm/_static/fb.png)

<span data-ttu-id="bf125-203">2 つのアカウントが統合されました。</span><span class="sxs-lookup"><span data-stu-id="bf125-203">The two accounts have been combined.</span></span> <span data-ttu-id="bf125-204">いずれかのアカウントでサインインすることは。</span><span class="sxs-lookup"><span data-stu-id="bf125-204">You are able to sign in with either account.</span></span> <span data-ttu-id="bf125-205">ユーザーがソーシャル ログインの認証サービスがダウンしているか、自分のソーシャル アカウントにアクセスを紛失した可能性が高い場合に、ローカル アカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="bf125-206">サイトがユーザー アカウントの確認を有効にします。</span><span class="sxs-lookup"><span data-stu-id="bf125-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="bf125-207">ユーザーとサイトのアカウント確認を有効にする既存のすべてのユーザーをロックアウトします。</span><span class="sxs-lookup"><span data-stu-id="bf125-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="bf125-208">自分のアカウントが確認されないために、既存のユーザーはロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="bf125-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="bf125-209">既存のユーザーのロックアウトを回避するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="bf125-210">確認されているすべての既存ユーザーをマークするデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="bf125-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="bf125-211">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf125-211">Confirm existing users.</span></span> <span data-ttu-id="bf125-212">確認リンクを含むメールなどのバッチの送信をします。</span><span class="sxs-lookup"><span data-stu-id="bf125-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="bf125-213">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bf125-213">Prerequisites</span></span>

[<span data-ttu-id="bf125-214">.NET core 2.2 SDK またはそれ以降</span><span class="sxs-lookup"><span data-stu-id="bf125-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="bf125-215">Web アプリを作成し、Identity のスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="bf125-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="bf125-216">認証を使用した web アプリを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bf125-216">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="bf125-217">新規ユーザー登録をテストします。</span><span class="sxs-lookup"><span data-stu-id="bf125-217">Test new user registration</span></span>

<span data-ttu-id="bf125-218">アプリを実行し、選択、**登録**リンク、およびユーザーを登録します。</span><span class="sxs-lookup"><span data-stu-id="bf125-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="bf125-219">この時点では、電子メールの検証のみ、 [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性。</span><span class="sxs-lookup"><span data-stu-id="bf125-219">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="bf125-220">登録を送信した後は、アプリにログインします。</span><span class="sxs-lookup"><span data-stu-id="bf125-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="bf125-221">チュートリアルの後半では、自分の電子メールが検証されるまでに新しいユーザーがサインインできないように、コードが更新されます。</span><span class="sxs-lookup"><span data-stu-id="bf125-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="bf125-222">テーブルに注意してください`EmailConfirmed`フィールドは`False`します。</span><span class="sxs-lookup"><span data-stu-id="bf125-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="bf125-223">アプリが送信された確認メールを送信するとき、次の手順でこの電子メールをもう一度使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="bf125-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="bf125-224">クリックし、行を右クリックして**削除**します。</span><span class="sxs-lookup"><span data-stu-id="bf125-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="bf125-225">電子メール エイリアスを削除する簡単で、次の手順。</span><span class="sxs-lookup"><span data-stu-id="bf125-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="bf125-226">確認の電子メールが必要です。</span><span class="sxs-lookup"><span data-stu-id="bf125-226">Require email confirmation</span></span>

<span data-ttu-id="bf125-227">新規ユーザー登録の電子メール アドレスを確認することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bf125-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="bf125-228">電子メールの他のユーザーが偽装はいないしていることを確認することを確認できます (つまりに登録していない他のユーザーの電子メールで)。</span><span class="sxs-lookup"><span data-stu-id="bf125-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="bf125-229">ディスカッション フォーラムでは、行われていればし、しないようにする"yli@example.comとして登録する"から"nolivetto@contoso.com"。</span><span class="sxs-lookup"><span data-stu-id="bf125-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="bf125-230">電子メールの確認なし"nolivetto@contoso.com"アプリから不要な電子メールを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="bf125-231">ユーザーが誤ってとして登録されているとします"ylo@example.com""yli"のスペル ミスを認識していなかったとします。</span><span class="sxs-lookup"><span data-stu-id="bf125-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="bf125-232">アプリは、正しいメール アドレスがあるないために、パスワードの回復を使用できるでしょう。</span><span class="sxs-lookup"><span data-stu-id="bf125-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="bf125-233">確認の電子メールは、ボットからの限られた保護を提供します。</span><span class="sxs-lookup"><span data-stu-id="bf125-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="bf125-234">確認の電子メールは、多くの電子メール アカウントを使用して悪意のあるユーザーから保護を提供しません。</span><span class="sxs-lookup"><span data-stu-id="bf125-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="bf125-235">新しいユーザーが確認された電子メールを受ける前に、web サイトにデータを送信するを防ぐために一般的にします。</span><span class="sxs-lookup"><span data-stu-id="bf125-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="bf125-236">Update`Startup.ConfigureServices`確認された電子メールを要求します。</span><span class="sxs-lookup"><span data-stu-id="bf125-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="bf125-237">`config.SignIn.RequireConfirmedEmail = true;` 登録済みのユーザーで自分の電子メールを確認するまでのログ記録はできません。</span><span class="sxs-lookup"><span data-stu-id="bf125-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="bf125-238">電子メール プロバイダーを構成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-238">Configure email provider</span></span>

<span data-ttu-id="bf125-239">このチュートリアルで[SendGrid](https://sendgrid.com)電子メールを送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="bf125-240">SendGrid アカウントとキーが電子メールを送信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf125-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="bf125-241">その他の電子メール プロバイダーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-241">You can use other email providers.</span></span> <span data-ttu-id="bf125-242">ASP.NET Core の 2.x を含む`System.Net.Mail`、アプリから電子メールを送信できます。</span><span class="sxs-lookup"><span data-stu-id="bf125-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="bf125-243">SendGrid または別の電子メール サービスを使用して電子メールを送信することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bf125-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="bf125-244">SMTP では、セキュリティで保護し、設定を正しく困難です。</span><span class="sxs-lookup"><span data-stu-id="bf125-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="bf125-245">電子メールをセキュリティで保護されたキーを取得するためのクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="bf125-246">このサンプルでは、次のように作成します*Services/AuthMessageSenderOptions.cs*:。</span><span class="sxs-lookup"><span data-stu-id="bf125-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="bf125-247">SendGrid のユーザーの機密情報を構成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="bf125-248">設定、`SendGridUser`と`SendGridKey`で、 [secret manager ツール](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="bf125-249">例:</span><span class="sxs-lookup"><span data-stu-id="bf125-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="bf125-250">Secret Manager、Windows 上のキー/値のペアが格納、 *secrets.json*ファイル、`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="bf125-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="bf125-251">内容、 *secrets.json*ファイルは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="bf125-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="bf125-252">次のマークアップに示す、 *secrets.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="bf125-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="bf125-253">`SendGridKey`値が削除されました。</span><span class="sxs-lookup"><span data-stu-id="bf125-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="bf125-254">詳細については、次を参照してください。、[オプション パターン](xref:fundamentals/configuration/options)と[構成](xref:fundamentals/configuration/index)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="bf125-255">SendGrid をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bf125-255">Install SendGrid</span></span>

<span data-ttu-id="bf125-256">このチュートリアルは、使用して電子メール通知を追加する方法を示します[SendGrid](https://sendgrid.com/)が SMTP およびその他のメカニズムを使用して電子メールを送信できます。</span><span class="sxs-lookup"><span data-stu-id="bf125-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="bf125-257">インストール、 `SendGrid` NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="bf125-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf125-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf125-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf125-259">パッケージ マネージャー コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="bf125-259">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bf125-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bf125-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bf125-261">コンソールで、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="bf125-261">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="bf125-262">参照してください[SendGrid を無料で開始する](https://sendgrid.com/free/)無料の SendGrid アカウントを登録します。</span><span class="sxs-lookup"><span data-stu-id="bf125-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="bf125-263">IEmailSender を実装します。</span><span class="sxs-lookup"><span data-stu-id="bf125-263">Implement IEmailSender</span></span>

<span data-ttu-id="bf125-264">実装`IEmailSender`、作成*Services/EmailSender.cs*次のようなコードで。</span><span class="sxs-lookup"><span data-stu-id="bf125-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="bf125-265">電子メールをサポートするために起動時を構成します。</span><span class="sxs-lookup"><span data-stu-id="bf125-265">Configure startup to support email</span></span>

<span data-ttu-id="bf125-266">次のコードを追加、`ConfigureServices`メソッドで、 *Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="bf125-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="bf125-267">追加`EmailSender`一時的なサービスとして。</span><span class="sxs-lookup"><span data-stu-id="bf125-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="bf125-268">登録、`AuthMessageSenderOptions`構成インスタンス。</span><span class="sxs-lookup"><span data-stu-id="bf125-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="bf125-269">アカウントの確認とパスワードの回復を有効にします。</span><span class="sxs-lookup"><span data-stu-id="bf125-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="bf125-270">テンプレートには、アカウントの確認とパスワードの回復用コードがあります。</span><span class="sxs-lookup"><span data-stu-id="bf125-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="bf125-271">検索、`OnPostAsync`メソッド*Areas/Identity/Pages/Account/Register.cshtml.cs*します。</span><span class="sxs-lookup"><span data-stu-id="bf125-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="bf125-272">新しく登録されたユーザーが、次の行をコメント アウトして自動的にサインインするようにします。</span><span class="sxs-lookup"><span data-stu-id="bf125-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="bf125-273">メソッド全体を強調表示されている変更された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bf125-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="bf125-274">登録、確認の電子メール、およびパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="bf125-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="bf125-275">Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。</span><span class="sxs-lookup"><span data-stu-id="bf125-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="bf125-276">アプリを実行し、新しいユーザーの登録</span><span class="sxs-lookup"><span data-stu-id="bf125-276">Run the app and register a new user</span></span>
* <span data-ttu-id="bf125-277">アカウント確認用のリンクは、電子メールを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf125-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="bf125-278">参照してください[デバッグ電子メール](#debug)電子メールが届かない場合。</span><span class="sxs-lookup"><span data-stu-id="bf125-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="bf125-279">電子メールを確認するリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bf125-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="bf125-280">電子メール アドレスとパスワードでサインインします。</span><span class="sxs-lookup"><span data-stu-id="bf125-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="bf125-281">サインアウトします。</span><span class="sxs-lookup"><span data-stu-id="bf125-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="bf125-282">ビューの管理 ページ</span><span class="sxs-lookup"><span data-stu-id="bf125-282">View the manage page</span></span>

<span data-ttu-id="bf125-283">ブラウザーで、ユーザー名を選択:![ユーザー名を備えたブラウザー ウィンドウ](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="bf125-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="bf125-284">管理ページが表示されますが、**プロファイル**タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="bf125-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="bf125-285">**電子メール**電子メールを示すチェック ボックスが確認されているかを示します。</span><span class="sxs-lookup"><span data-stu-id="bf125-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="bf125-286">テストのパスワードのリセット</span><span class="sxs-lookup"><span data-stu-id="bf125-286">Test password reset</span></span>

* <span data-ttu-id="bf125-287">サインインしている場合は、選択**ログアウト**します。</span><span class="sxs-lookup"><span data-stu-id="bf125-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="bf125-288">選択、**ログイン**リンクし、選択、**パスワードを忘れた場合でしょうか。** リンク。</span><span class="sxs-lookup"><span data-stu-id="bf125-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="bf125-289">アカウントを登録するために使用する電子メール アドレスを入力します。</span><span class="sxs-lookup"><span data-stu-id="bf125-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="bf125-290">パスワードをリセットするリンクを含む電子メールが送信されます。</span><span class="sxs-lookup"><span data-stu-id="bf125-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="bf125-291">電子メールを確認し、パスワードをリセットするリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bf125-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="bf125-292">パスワードが正常にリセットされると後、はの電子メール アドレスと新しいパスワードでサインインすることができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="bf125-293">電子メールとアクティビティのタイムアウトを変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-293">Change email and activity timeout</span></span>

<span data-ttu-id="bf125-294">既定のアイドル タイムアウトは、14 日間です。</span><span class="sxs-lookup"><span data-stu-id="bf125-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="bf125-295">次のコードでは、アイドル タイムアウトを 5 日に設定します。</span><span class="sxs-lookup"><span data-stu-id="bf125-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="bf125-296">すべてのデータの保護トークン lifespans を変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="bf125-297">次のコードは、すべてのデータの保護トークンのタイムアウト期間を 3 時間に変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="bf125-298">組み込みの Id のユーザー トークン (を参照してください[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) が、 [1 日のタイムアウト](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="bf125-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="bf125-299">電子メールのトークン有効期間を変更します。</span><span class="sxs-lookup"><span data-stu-id="bf125-299">Change the email token lifespan</span></span>

<span data-ttu-id="bf125-300">既定のトークン有効期間[Id のユーザー トークン](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)は[1 日](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="bf125-301">このセクションでは、電子メールのトークン有効期間を変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bf125-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="bf125-302">追加のカスタム[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)と<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="bf125-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="bf125-303">サービス コンテナーには、カスタム プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="bf125-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="bf125-304">確認の電子メールを再送信します。</span><span class="sxs-lookup"><span data-stu-id="bf125-304">Resend email confirmation</span></span>

<span data-ttu-id="bf125-305">参照してください[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5410)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="bf125-306">電子メールをデバッグします。</span><span class="sxs-lookup"><span data-stu-id="bf125-306">Debug email</span></span>

<span data-ttu-id="bf125-307">電子メールの作業が発生したことはできません: 場合</span><span class="sxs-lookup"><span data-stu-id="bf125-307">If you can't get email working:</span></span>

* <span data-ttu-id="bf125-308">ブレークポイントを設定`EmailSender.Execute`を確認する`SendGridClient.SendEmailAsync`が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="bf125-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="bf125-309">作成、[電子メールを送信するためのコンソール アプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)に同様のコードを使用して`EmailSender.Execute`します。</span><span class="sxs-lookup"><span data-stu-id="bf125-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="bf125-310">レビュー、[電子メール アクティビティ](https://sendgrid.com/docs/User_Guide/email_activity.html)ページ。</span><span class="sxs-lookup"><span data-stu-id="bf125-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="bf125-311">迷惑メール フォルダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf125-311">Check your spam folder.</span></span>
* <span data-ttu-id="bf125-312">別のメール プロバイダー (Microsoft、Yahoo、Gmail など) に別の電子メール エイリアスをお試しください。</span><span class="sxs-lookup"><span data-stu-id="bf125-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="bf125-313">別のメール アカウントへの送信を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="bf125-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="bf125-314">**セキュリティのベスト プラクティス**は**いない**テストと開発における運用シークレットを使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="bf125-315">アプリを Azure に発行する場合は、Web アプリを Azure portal でアプリケーション設定と SendGrid シークレットを設定できます。</span><span class="sxs-lookup"><span data-stu-id="bf125-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="bf125-316">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="bf125-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="bf125-317">ソーシャル、ローカルのログイン アカウントを組み合わせる</span><span class="sxs-lookup"><span data-stu-id="bf125-317">Combine social and local login accounts</span></span>

<span data-ttu-id="bf125-318">このセクションを完了するには、まず、外部認証プロバイダーを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf125-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="bf125-319">参照してください[Facebook、Google、および外部プロバイダー認証](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="bf125-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="bf125-320">電子メールのリンクをクリックして、ローカルおよびソーシャル アカウントを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="bf125-321">次の順序で"RickAndMSFT@gmail.com"が最初に、ローカルのログインとして作成ただし、最初に、ソーシャル ログインとしてアカウントを作成し、ローカル ログインを追加します。</span><span class="sxs-lookup"><span data-stu-id="bf125-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web アプリケーション:RickAndMSFT@gmail.com認証されたユーザー](accconfirm/_static/rick.png)

<span data-ttu-id="bf125-323">をクリックして、**管理**リンク。</span><span class="sxs-lookup"><span data-stu-id="bf125-323">Click on the **Manage** link.</span></span> <span data-ttu-id="bf125-324">このアカウントに関連付けられた 0 外部 (ソーシャル ログイン) に注意してください。</span><span class="sxs-lookup"><span data-stu-id="bf125-324">Note the 0 external (social logins) associated with this account.</span></span>

![ビューを管理します。](accconfirm/_static/manage.png)

<span data-ttu-id="bf125-326">別のログイン サービスへのリンクをクリックし、アプリの要求をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="bf125-327">次の図では、Facebook は、外部認証プロバイダーは。</span><span class="sxs-lookup"><span data-stu-id="bf125-327">In the following image, Facebook is the external authentication provider:</span></span>

![Facebook を一覧表示する、外部ログイン ビューを管理します。](accconfirm/_static/fb.png)

<span data-ttu-id="bf125-329">2 つのアカウントが統合されました。</span><span class="sxs-lookup"><span data-stu-id="bf125-329">The two accounts have been combined.</span></span> <span data-ttu-id="bf125-330">いずれかのアカウントでサインインすることは。</span><span class="sxs-lookup"><span data-stu-id="bf125-330">You are able to sign in with either account.</span></span> <span data-ttu-id="bf125-331">ユーザーがソーシャル ログインの認証サービスがダウンしているか、自分のソーシャル アカウントにアクセスを紛失した可能性が高い場合に、ローカル アカウントを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="bf125-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="bf125-332">サイトがユーザー アカウントの確認を有効にします。</span><span class="sxs-lookup"><span data-stu-id="bf125-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="bf125-333">ユーザーとサイトのアカウント確認を有効にする既存のすべてのユーザーをロックアウトします。</span><span class="sxs-lookup"><span data-stu-id="bf125-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="bf125-334">自分のアカウントが確認されないために、既存のユーザーはロックアウトされます。</span><span class="sxs-lookup"><span data-stu-id="bf125-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="bf125-335">既存のユーザーのロックアウトを回避するには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="bf125-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="bf125-336">確認されているすべての既存ユーザーをマークするデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="bf125-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="bf125-337">既存のユーザーを確認します。</span><span class="sxs-lookup"><span data-stu-id="bf125-337">Confirm existing users.</span></span> <span data-ttu-id="bf125-338">確認リンクを含むメールなどのバッチの送信をします。</span><span class="sxs-lookup"><span data-stu-id="bf125-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
