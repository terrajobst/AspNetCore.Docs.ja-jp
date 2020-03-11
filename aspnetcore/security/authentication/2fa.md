---
title: ASP.NET Core での SMS で 2 要素認証
author: rick-anderson
description: ASP.NET Core アプリで 2 要素認証 (2 fa) を設定する方法について説明します。
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 424d21e446de02b39daa7685205a00931361b326
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652724"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="7d5fa-103">ASP.NET Core での SMS で 2 要素認証</span><span class="sxs-lookup"><span data-stu-id="7d5fa-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="7d5fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)および[スイス-開発者](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="7d5fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="7d5fa-105">時間ベース ワンタイム パスワード アルゴリズム (TOTP) を使用して 2 要素認証 (2 fa) authenticator アプリは、業界の 2 fa のアプローチをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="7d5fa-106">2 fa を SMS 2 fa を TOTP を使用しています。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="7d5fa-107">詳細については、ASP.NET Core 2.0 以降の[ASP.NET Core での TOTP authenticator アプリの QR コード生成の有効化](xref:security/authentication/identity-enable-qrcodes)に関する説明を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="7d5fa-108">このチュートリアルでは、SMS を使用して 2 要素認証 (2 fa) を設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="7d5fa-109">手順については、 [twilio](https://www.twilio.com/)と[aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)を参照してください。ただし、他の sms プロバイダーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="7d5fa-110">このチュートリアルを開始する前に[、アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)を完了することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="7d5fa-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="7d5fa-112">[ダウンロード方法](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="7d5fa-113">新しい ASP.NET Core プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="7d5fa-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="7d5fa-114">個々のユーザーアカウントを使用して `Web2FA` という名前の新しい ASP.NET Core web アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="7d5fa-115"><xref:security/enforcing-ssl> の指示に従って、HTTPS を設定して要求します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="7d5fa-116">SMS アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-116">Create an SMS account</span></span>

<span data-ttu-id="7d5fa-117">たとえば、 [twilio](https://www.twilio.com/)や[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)などの sms アカウントを作成します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="7d5fa-118">認証資格情報を記録 (twilio: accountSid および authToken、ASPSMS の: 別子-Userkey とパスワード)。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="7d5fa-119">SMS プロバイダーの資格情報を見極める</span><span class="sxs-lookup"><span data-stu-id="7d5fa-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="7d5fa-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="7d5fa-120">**Twilio:**</span></span>

<span data-ttu-id="7d5fa-121">Twilio アカウントの [ダッシュボード] タブで、**アカウント SID**と**認証トークン**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="7d5fa-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="7d5fa-122">**ASPSMS:**</span></span>

<span data-ttu-id="7d5fa-123">アカウントの設定から**ユーザーキー**に移動し、**パスワード**と共にコピーします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="7d5fa-124">これらの値は、後で `SMSAccountIdentification` と `SMSAccountPassword`のキー内の secret manager ツールを使用してに格納します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="7d5fa-125">SenderID を指定する/発信元</span><span class="sxs-lookup"><span data-stu-id="7d5fa-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="7d5fa-126">**Twilio:** [数値] タブで、Twilio**電話番号**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="7d5fa-127">**Aspsms:** [発信元のロック解除] メニュー内で、1つ以上の発信者をロック解除するか、英数字の発信者を選択します (すべてのネットワークでサポートされていません)。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="7d5fa-128">この値は、後でキー `SMSAccountFrom`内の secret manager ツールで格納します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="7d5fa-129">SMS サービスの資格情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="7d5fa-130">[オプションパターン](xref:fundamentals/configuration/options)を使用して、ユーザーアカウントとキーの設定にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="7d5fa-131">セキュリティで保護された SMS キーを取得するためのクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="7d5fa-132">このサンプルでは、*サービス/SMSoptions .cs*ファイルに `SMSoptions` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="7d5fa-133">`SMSAccountIdentification`、`SMSAccountPassword` と `SMSAccountFrom` を、 [secret manager ツール](xref:security/app-secrets)を使用して設定します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7d5fa-134">例 :</span><span class="sxs-lookup"><span data-stu-id="7d5fa-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="7d5fa-135">SMS プロバイダーの NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="7d5fa-136">パッケージ マネージャー コンソール (PMC) を実行します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="7d5fa-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="7d5fa-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="7d5fa-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="7d5fa-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="7d5fa-139">*サービス/MessageServices .cs*ファイルにコードを追加して、SMS を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="7d5fa-140">Twilio または ASPSMS セクションのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="7d5fa-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="7d5fa-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="7d5fa-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="7d5fa-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="7d5fa-143">起動時に `SMSoptions` を使用するように構成する</span><span class="sxs-lookup"><span data-stu-id="7d5fa-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="7d5fa-144">*Startup.cs*の `ConfigureServices` メソッドで、サービスコンテナーに `SMSoptions` を追加します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="7d5fa-145">2 要素認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-145">Enable two-factor authentication</span></span>

<span data-ttu-id="7d5fa-146">*Views/Manage/Index. cshtml* Razor ビューファイルを開き、コメント文字を削除します (マークアップがコメントアウトされることはありません)。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="7d5fa-147">2 要素認証をログインします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="7d5fa-148">アプリを実行し、新しいユーザーの登録</span><span class="sxs-lookup"><span data-stu-id="7d5fa-148">Run the app and register a new user</span></span>

![Web アプリケーションの登録が Microsoft Edge でビューを開く](2fa/_static/login2fa1.png)

* <span data-ttu-id="7d5fa-150">[コントローラーの管理] で `Index` アクションメソッドをアクティブにするユーザー名をタップします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="7d5fa-151">次に、[電話番号の**追加**] リンクをタップします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-151">Then tap the phone number **Add** link.</span></span>

![ビューの管理 -"の追加 リンクをタップします。](2fa/_static/login2fa2.png)

* <span data-ttu-id="7d5fa-153">確認コードを受信する電話番号を追加し、 **[確認コードの送信]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![ページの 電話番号を追加します。](2fa/_static/login2fa3.png)

* <span data-ttu-id="7d5fa-155">確認コードを含むテキスト メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="7d5fa-156">入力し、 **[送信]** をタップします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-156">Enter it and tap **Submit**</span></span>

![ページの 電話番号を確認します。](2fa/_static/login2fa4.png)

<span data-ttu-id="7d5fa-158">テキスト メッセージが表示されない場合は、twilio ログ ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="7d5fa-159">電話番号が正常に追加された、管理ビューを示しています。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-159">The Manage view shows your phone number was added successfully.</span></span>

![ビュー - 電話番号が正常に追加の管理します。](2fa/_static/login2fa5.png)

* <span data-ttu-id="7d5fa-161">**[有効]** をタップして、2要素認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-161">Tap **Enable** to enable two-factor authentication.</span></span>

![ビューの管理 - 2 要素認証を有効にします。](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="7d5fa-163">2 要素認証のテスト</span><span class="sxs-lookup"><span data-stu-id="7d5fa-163">Test two-factor authentication</span></span>

* <span data-ttu-id="7d5fa-164">ログオフします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-164">Log off.</span></span>

* <span data-ttu-id="7d5fa-165">ログインします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-165">Log in.</span></span>

* <span data-ttu-id="7d5fa-166">認証の 2 番目の要素を提供する必要があるために、ユーザー アカウントは、2 要素認証を有効になります。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="7d5fa-167">このチュートリアルでは、電話番号の検証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="7d5fa-168">組み込みのテンプレートでは、2 番目の要素として電子メールを設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="7d5fa-169">QR コードなど、認証の他の 2 番目の要素を設定することができます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="7d5fa-170">**Submit (送信**)」をタップします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-170">Tap **Submit**.</span></span>

![ビューの確認コードを送信します。](2fa/_static/login2fa7.png)

* <span data-ttu-id="7d5fa-172">SMS メッセージを取得するコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="7d5fa-173">[**このブラウザーを記憶**する] チェックボックスをオンにすると、同じデバイスとブラウザーを使用しているときに2fa を使用してログオンする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="7d5fa-174">2FA を有効にして [**このブラウザーを記憶**する] をオンにすると、悪意のあるユーザーが自分のデバイスにアクセスできなくても、アカウントへのアクセスを試みる悪意のあるユーザーからの強力な2fa 保護が提供されます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="7d5fa-175">これは、定期的に使用するプライベートあらゆるデバイスで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="7d5fa-176">[**このブラウザーを記憶**する] を設定すると、定期的に使用しないデバイスから2fa のセキュリティが強化され、自分のデバイスで2fa を通過する必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![ビューを確認します。](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="7d5fa-178">ブルート フォース攻撃から保護するためのアカウントのロックアウト</span><span class="sxs-lookup"><span data-stu-id="7d5fa-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="7d5fa-179">2 fa では、アカウントのロックアウトをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="7d5fa-180">ユーザーがローカル アカウントまたはソーシャル アカウントでサインインすると、2 fa に失敗した場合は各が格納されます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="7d5fa-181">ユーザーがロックアウトされた場合は、最大の失敗したアクセス試行に達すると、(既定値: 5 アクセス試行の失敗後に 5 分間ロックアウト)。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="7d5fa-182">成功した認証は失敗したアクセス試行数がリセットされ、時計をリセットします。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="7d5fa-183">失敗したアクセス試行の最大回数とロックアウト時間は、 [Maxfailed Accessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts)と[Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)で設定できます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="7d5fa-184">次で 10 アクセス試行の失敗後 10 分間のアカウントのロックアウトが構成されます。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="7d5fa-185">[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync)によって `lockoutOnFailure` が `true`に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7d5fa-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
