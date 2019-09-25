---
title: ASP.NET Core での SMS で 2 要素認証
author: rick-anderson
description: ASP.NET Core アプリで 2 要素認証 (2 fa) を設定する方法について説明します。
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 68219579be9b7a7b25da6e348054e1ff2015cf5f
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248388"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="2ca16-103">ASP.NET Core での SMS で 2 要素認証</span><span class="sxs-lookup"><span data-stu-id="2ca16-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="2ca16-104">によって[Rick Anderson](https://twitter.com/RickAndMSFT)と[Swiss 開発者](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="2ca16-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="2ca16-105">時間ベース ワンタイム パスワード アルゴリズム (TOTP) を使用して 2 要素認証 (2 fa) authenticator アプリは、業界の 2 fa のアプローチをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="2ca16-106">2 fa を SMS 2 fa を TOTP を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2ca16-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="2ca16-107">詳細については、次を参照してください。 [TOTP 認証アプリが ASP.NET Core での QR コードを有効にする生成](xref:security/authentication/identity-enable-qrcodes)ASP.NET Core 2.0 以降。</span><span class="sxs-lookup"><span data-stu-id="2ca16-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="2ca16-108">このチュートリアルでは、SMS を使用して 2 要素認証 (2 fa) を設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="2ca16-109">手順が示されて[twilio](https://www.twilio.com/)と[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)、他の SMS プロバイダーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="2ca16-110">完了したことをお勧めします。[アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)このチュートリアルを開始する前にします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="2ca16-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="2ca16-112">[ダウンロードする方法](xref:index#how-to-download-a-sample)します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="2ca16-113">新しい ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2ca16-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="2ca16-114">という名前の新しい ASP.NET Core web アプリ作成`Web2FA`個々 のユーザー アカウントを使用します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="2ca16-115">「」の<xref:security/enforcing-ssl>手順に従って、HTTPS を設定し、要求します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="2ca16-116">SMS アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-116">Create an SMS account</span></span>

<span data-ttu-id="2ca16-117">例については、SMS アカウントの作成[twilio](https://www.twilio.com/)または[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="2ca16-118">認証資格情報を記録します (twilio: accountSid および authToken の場合は、ASPSMS の場合:ユーザーキーとパスワード) を入力します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="2ca16-119">SMS プロバイダーの資格情報を見極める</span><span class="sxs-lookup"><span data-stu-id="2ca16-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="2ca16-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="2ca16-120">**Twilio:**</span></span>

<span data-ttu-id="2ca16-121">Twilio アカウントの [ダッシュボード] タブで、**アカウント SID**と**認証トークン**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="2ca16-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="2ca16-122">**ASPSMS:**</span></span>

<span data-ttu-id="2ca16-123">アカウントの設定から**ユーザーキー**に移動し、**パスワード**と共にコピーします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="2ca16-124">キー内 secret manager ツールを使用してこれらの値を後で保存`SMSAccountIdentification`と`SMSAccountPassword`します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="2ca16-125">SenderID を指定する/発信元</span><span class="sxs-lookup"><span data-stu-id="2ca16-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="2ca16-126">**Twilio**[数値] タブで、Twilio**電話番号**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="2ca16-127">**ASPSMS:** [発信元のロック解除] メニュー内で、1つ以上の発信者をロック解除するか、英数字の発信者を選択します (すべてのネットワークでサポートされていません)。</span><span class="sxs-lookup"><span data-stu-id="2ca16-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="2ca16-128">キー内のシークレット マネージャー ツールを使用してこの値を後で保存`SMSAccountFrom`します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="2ca16-129">SMS サービスの資格情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="2ca16-130">使用して、[オプション パターン](xref:fundamentals/configuration/options)ユーザー アカウントとキーの設定にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="2ca16-131">セキュリティで保護された SMS キーを取得するためのクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="2ca16-132">このサンプルで、`SMSoptions`でクラスを作成、 *Services/SMSoptions.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="2ca16-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="2ca16-133">設定、 `SMSAccountIdentification`、`SMSAccountPassword`と`SMSAccountFrom`で、 [secret manager ツール](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="2ca16-134">例:</span><span class="sxs-lookup"><span data-stu-id="2ca16-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="2ca16-135">SMS プロバイダーの NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="2ca16-136">パッケージ マネージャー コンソール (PMC) を実行します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="2ca16-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="2ca16-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="2ca16-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="2ca16-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="2ca16-139">内のコードを追加、 *Services/MessageServices.cs* SMS が有効にするファイル。</span><span class="sxs-lookup"><span data-stu-id="2ca16-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="2ca16-140">Twilio または ASPSMS セクションのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="2ca16-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="2ca16-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="2ca16-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="2ca16-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="2ca16-143">使用するスタートアップを構成します。 `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="2ca16-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="2ca16-144">追加`SMSoptions`でサービス コンテナーに、`ConfigureServices`メソッドで、 *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2ca16-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="2ca16-145">2 要素認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-145">Enable two-factor authentication</span></span>

<span data-ttu-id="2ca16-146">*Views/Manage/Index. cshtml* Razor ビューファイルを開き、コメント文字を削除します (マークアップがコメントアウトされることはありません)。</span><span class="sxs-lookup"><span data-stu-id="2ca16-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="2ca16-147">2 要素認証をログインします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="2ca16-148">アプリを実行し、新しいユーザーの登録</span><span class="sxs-lookup"><span data-stu-id="2ca16-148">Run the app and register a new user</span></span>

![Web アプリケーションの登録が Microsoft Edge でビューを開く](2fa/_static/login2fa1.png)

* <span data-ttu-id="2ca16-150">アクティブ化するユーザー名をタップして、`Index`管理コント ローラー アクション メソッド。</span><span class="sxs-lookup"><span data-stu-id="2ca16-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="2ca16-151">電話番号の順にタップ**追加**リンク。</span><span class="sxs-lookup"><span data-stu-id="2ca16-151">Then tap the phone number **Add** link.</span></span>

![ビューの管理 -"の追加 リンクをタップします。](2fa/_static/login2fa2.png)

* <span data-ttu-id="2ca16-153">確認コードを受信してタップ、する電話番号を追加**確認コードを送信**します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![ページの 電話番号を追加します。](2fa/_static/login2fa3.png)

* <span data-ttu-id="2ca16-155">確認コードを含むテキスト メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="2ca16-156">入力し、タップ**送信**</span><span class="sxs-lookup"><span data-stu-id="2ca16-156">Enter it and tap **Submit**</span></span>

![ページの 電話番号を確認します。](2fa/_static/login2fa4.png)

<span data-ttu-id="2ca16-158">テキスト メッセージが表示されない場合は、twilio ログ ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ca16-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="2ca16-159">電話番号が正常に追加された、管理ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="2ca16-159">The Manage view shows your phone number was added successfully.</span></span>

![ビュー - 電話番号が正常に追加の管理します。](2fa/_static/login2fa5.png)

* <span data-ttu-id="2ca16-161">タップ**を有効にする**2 要素認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-161">Tap **Enable** to enable two-factor authentication.</span></span>

![ビューの管理 - 2 要素認証を有効にします。](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="2ca16-163">2 要素認証のテスト</span><span class="sxs-lookup"><span data-stu-id="2ca16-163">Test two-factor authentication</span></span>

* <span data-ttu-id="2ca16-164">ログオフします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-164">Log off.</span></span>

* <span data-ttu-id="2ca16-165">ログイン。</span><span class="sxs-lookup"><span data-stu-id="2ca16-165">Log in.</span></span>

* <span data-ttu-id="2ca16-166">認証の 2 番目の要素を提供する必要があるために、ユーザー アカウントは、2 要素認証を有効になります。</span><span class="sxs-lookup"><span data-stu-id="2ca16-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="2ca16-167">このチュートリアルでは、電話番号の検証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="2ca16-168">組み込みのテンプレートでは、2 番目の要素として電子メールを設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="2ca16-169">QR コードなど、認証の他の 2 番目の要素を設定することができます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="2ca16-170">タップ**送信**します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-170">Tap **Submit**.</span></span>

![ビューの確認コードを送信します。](2fa/_static/login2fa7.png)

* <span data-ttu-id="2ca16-172">SMS メッセージを取得するコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="2ca16-173">クリックすると、**このブラウザーを記憶する** チェック ボックスの除外 2 fa を使用して、同じデバイスとブラウザーを使用するときにログオンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="2ca16-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="2ca16-174">2 fa を有効にし、**このブラウザーを記憶する**は 2 fa の強力な保護実現デバイスへのアクセスがあるない限り、アカウントにアクセスしようとした悪意のあるユーザーから。</span><span class="sxs-lookup"><span data-stu-id="2ca16-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="2ca16-175">これは、定期的に使用するプライベートあらゆるデバイスで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="2ca16-176">設定して**このブラウザーを記憶する**2 fa の追加のセキュリティを定期的に使用しないデバイスから取得した、独自のデバイスで 2 fa を経由する必要があるの利便性を取得します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![ビューを確認します。](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="2ca16-178">ブルート フォース攻撃から保護するためのアカウントのロックアウト</span><span class="sxs-lookup"><span data-stu-id="2ca16-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="2ca16-179">2 fa では、アカウントのロックアウトをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="2ca16-180">ユーザーがローカル アカウントまたはソーシャル アカウントでサインインすると、2 fa に失敗した場合は各が格納されます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="2ca16-181">失敗したアクセス試行の最大回数に達した場合、ユーザーはロックアウトされます (既定:5分間のロックアウトは5分間のアクセス試行に失敗しました)。</span><span class="sxs-lookup"><span data-stu-id="2ca16-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="2ca16-182">成功した認証は失敗したアクセス試行数がリセットされ、時計をリセットします。</span><span class="sxs-lookup"><span data-stu-id="2ca16-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="2ca16-183">失敗したアクセス試行の最大値とロックアウト時刻で設定できる[MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts)と[DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)します。</span><span class="sxs-lookup"><span data-stu-id="2ca16-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="2ca16-184">次で 10 アクセス試行の失敗後 10 分間のアカウントのロックアウトが構成されます。</span><span class="sxs-lookup"><span data-stu-id="2ca16-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="2ca16-185">確認します[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync)設定`lockoutOnFailure`に`true`:</span><span class="sxs-lookup"><span data-stu-id="2ca16-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
