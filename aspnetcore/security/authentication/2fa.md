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
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>ASP.NET Core での SMS で 2 要素認証

By [Rick Anderson](https://twitter.com/RickAndMSFT)および[スイス-開発者](https://github.com/Swiss-Devs)

>[!WARNING]
> 時間ベース ワンタイム パスワード アルゴリズム (TOTP) を使用して 2 要素認証 (2 fa) authenticator アプリは、業界の 2 fa のアプローチをお勧めします。 2 fa を SMS 2 fa を TOTP を使用しています。 詳細については、ASP.NET Core 2.0 以降の[ASP.NET Core での TOTP authenticator アプリの QR コード生成の有効化](xref:security/authentication/identity-enable-qrcodes)に関する説明を参照してください。

このチュートリアルでは、SMS を使用して 2 要素認証 (2 fa) を設定する方法を示します。 手順については、 [twilio](https://www.twilio.com/)と[aspsms](https://www.aspsms.com/asp.net/identity/core/testcredits/)を参照してください。ただし、他の sms プロバイダーを使用することもできます。 このチュートリアルを開始する前に[、アカウントの確認とパスワードの回復](xref:security/authentication/accconfirm)を完了することをお勧めします。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)します。 [ダウンロード方法](xref:index#how-to-download-a-sample)。

## <a name="create-a-new-aspnet-core-project"></a>新しい ASP.NET Core プロジェクトを作成する

個々のユーザーアカウントを使用して `Web2FA` という名前の新しい ASP.NET Core web アプリを作成します。 <xref:security/enforcing-ssl> の指示に従って、HTTPS を設定して要求します。

### <a name="create-an-sms-account"></a>SMS アカウントを作成します。

たとえば、 [twilio](https://www.twilio.com/)や[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)などの sms アカウントを作成します。 認証資格情報を記録 (twilio: accountSid および authToken、ASPSMS の: 別子-Userkey とパスワード)。

#### <a name="figuring-out-sms-provider-credentials"></a>SMS プロバイダーの資格情報を見極める

**Twilio**

Twilio アカウントの [ダッシュボード] タブで、**アカウント SID**と**認証トークン**をコピーします。

**ASPSMS:**

アカウントの設定から**ユーザーキー**に移動し、**パスワード**と共にコピーします。

これらの値は、後で `SMSAccountIdentification` と `SMSAccountPassword`のキー内の secret manager ツールを使用してに格納します。

#### <a name="specifying-senderid--originator"></a>SenderID を指定する/発信元

**Twilio:** [数値] タブで、Twilio**電話番号**をコピーします。

**Aspsms:** [発信元のロック解除] メニュー内で、1つ以上の発信者をロック解除するか、英数字の発信者を選択します (すべてのネットワークでサポートされていません)。

この値は、後でキー `SMSAccountFrom`内の secret manager ツールで格納します。

### <a name="provide-credentials-for-the-sms-service"></a>SMS サービスの資格情報を提供します。

[オプションパターン](xref:fundamentals/configuration/options)を使用して、ユーザーアカウントとキーの設定にアクセスします。

* セキュリティで保護された SMS キーを取得するためのクラスを作成します。 このサンプルでは、*サービス/SMSoptions .cs*ファイルに `SMSoptions` クラスを作成します。

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

`SMSAccountIdentification`、`SMSAccountPassword` と `SMSAccountFrom` を、 [secret manager ツール](xref:security/app-secrets)を使用して設定します。 例 :

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* SMS プロバイダーの NuGet パッケージを追加します。 パッケージ マネージャー コンソール (PMC) を実行します。

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* *サービス/MessageServices .cs*ファイルにコードを追加して、SMS を有効にします。 Twilio または ASPSMS セクションのいずれかを使用します。

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>起動時に `SMSoptions` を使用するように構成する

*Startup.cs*の `ConfigureServices` メソッドで、サービスコンテナーに `SMSoptions` を追加します。

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>2 要素認証を有効にします。

*Views/Manage/Index. cshtml* Razor ビューファイルを開き、コメント文字を削除します (マークアップがコメントアウトされることはありません)。

## <a name="log-in-with-two-factor-authentication"></a>2 要素認証をログインします。

* アプリを実行し、新しいユーザーの登録

![Web アプリケーションの登録が Microsoft Edge でビューを開く](2fa/_static/login2fa1.png)

* [コントローラーの管理] で `Index` アクションメソッドをアクティブにするユーザー名をタップします。 次に、[電話番号の**追加**] リンクをタップします。

![ビューの管理 -"の追加 リンクをタップします。](2fa/_static/login2fa2.png)

* 確認コードを受信する電話番号を追加し、 **[確認コードの送信]** をタップします。

![ページの 電話番号を追加します。](2fa/_static/login2fa3.png)

* 確認コードを含むテキスト メッセージが表示されます。 入力し、 **[送信]** をタップします。

![ページの 電話番号を確認します。](2fa/_static/login2fa4.png)

テキスト メッセージが表示されない場合は、twilio ログ ページを参照してください。

* 電話番号が正常に追加された、管理ビューを示しています。

![ビュー - 電話番号が正常に追加の管理します。](2fa/_static/login2fa5.png)

* **[有効]** をタップして、2要素認証を有効にします。

![ビューの管理 - 2 要素認証を有効にします。](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>2 要素認証のテスト

* ログオフします。

* ログインします。

* 認証の 2 番目の要素を提供する必要があるために、ユーザー アカウントは、2 要素認証を有効になります。 このチュートリアルでは、電話番号の検証を有効にします。 組み込みのテンプレートでは、2 番目の要素として電子メールを設定することもできます。 QR コードなど、認証の他の 2 番目の要素を設定することができます。 **Submit (送信**)」をタップします。

![ビューの確認コードを送信します。](2fa/_static/login2fa7.png)

* SMS メッセージを取得するコードを入力します。

* [**このブラウザーを記憶**する] チェックボックスをオンにすると、同じデバイスとブラウザーを使用しているときに2fa を使用してログオンする必要がありません。 2FA を有効にして [**このブラウザーを記憶**する] をオンにすると、悪意のあるユーザーが自分のデバイスにアクセスできなくても、アカウントへのアクセスを試みる悪意のあるユーザーからの強力な2fa 保護が提供されます。 これは、定期的に使用するプライベートあらゆるデバイスで行うことができます。 [**このブラウザーを記憶**する] を設定すると、定期的に使用しないデバイスから2fa のセキュリティが強化され、自分のデバイスで2fa を通過する必要がなくなります。

![ビューを確認します。](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>ブルート フォース攻撃から保護するためのアカウントのロックアウト

2 fa では、アカウントのロックアウトをお勧めします。 ユーザーがローカル アカウントまたはソーシャル アカウントでサインインすると、2 fa に失敗した場合は各が格納されます。 ユーザーがロックアウトされた場合は、最大の失敗したアクセス試行に達すると、(既定値: 5 アクセス試行の失敗後に 5 分間ロックアウト)。 成功した認証は失敗したアクセス試行数がリセットされ、時計をリセットします。 失敗したアクセス試行の最大回数とロックアウト時間は、 [Maxfailed Accessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts)と[Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)で設定できます。 次で 10 アクセス試行の失敗後 10 分間のアカウントのロックアウトが構成されます。

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync)によって `lockoutOnFailure` が `true`に設定されていることを確認します。

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
