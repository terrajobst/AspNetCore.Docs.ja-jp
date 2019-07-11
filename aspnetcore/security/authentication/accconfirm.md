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
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>アカウントの確認と ASP.NET Core でのパスワードの回復

によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)、および[Joe Audette](https://twitter.com/joeaudette)

このチュートリアルでは、電子メールの確認とパスワードのリセットと ASP.NET Core アプリをビルドする方法を示します。 このチュートリアルは**いない**先頭トピック。 理解しておく必要があります。

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [認証](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

参照してください[この PDF ファイル](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)ASP.NET Core 1.1 バージョン。

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a>必須コンポーネント

[.NET core 3.0 SDK またはそれ以降](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>作成し、認証を使用した web アプリのテスト

認証を使用した web アプリを作成するには、次のコマンドを実行します。

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

アプリを実行し、選択、**登録**リンク、およびユーザーを登録します。 登録されると、リダイレクトされるの`/Identity/Account/RegisterConfirmation`シミュレート確認の電子メールへのリンクを含むページ。

* 選択、`Click here to confirm your account`リンク。
* 選択、**ログイン**リンクと同じ資格情報でサインインします。
* 選択、`Hello YourEmail@provider.com!`リンクをリダイレクトする、`/Identity/Account/Manage/PersonalData`ページ。
* 選択、**個人データ**、左側のタブを選び**削除**します。

### <a name="configure-an-email-provider"></a>電子メール プロバイダーを構成します。

このチュートリアルで[SendGrid](https://sendgrid.com)電子メールを送信するために使用します。 SendGrid アカウントとキーが電子メールを送信する必要があります。 その他の電子メール プロバイダーを使用することができます。 SendGrid または別の電子メール サービスを使用して電子メールを送信することをお勧めします。 SMTP では、セキュリティで保護し、設定を正しく困難です。

電子メールをセキュリティで保護されたキーを取得するためのクラスを作成します。 このサンプルでは、次のように作成します*Services/AuthMessageSenderOptions.cs*:。

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid のユーザーの機密情報を構成します。

設定、`SendGridUser`と`SendGridKey`で、 [secret manager ツール](xref:security/app-secrets)します。 例:

```console
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Secret Manager、Windows 上のキー/値のペアが格納、 *secrets.json*ファイル、`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`ディレクトリ。

内容、 *secrets.json*ファイルは暗号化されません。 次のマークアップに示す、 *secrets.json*ファイル。 `SendGridKey`値が削除されました。

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

詳細については、次を参照してください。、[オプション パターン](xref:fundamentals/configuration/options)と[構成](xref:fundamentals/configuration/index)します。

### <a name="install-sendgrid"></a>SendGrid をインストールします。

このチュートリアルは、使用して電子メール通知を追加する方法を示します[SendGrid](https://sendgrid.com/)が SMTP およびその他のメカニズムを使用して電子メールを送信できます。

インストール、 `SendGrid` NuGet パッケージ。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージ マネージャー コンソールで、次のコマンドを入力します。

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コンソールで、次のコマンドを入力します。

```cli
dotnet add package SendGrid
```

---

参照してください[SendGrid を無料で開始する](https://sendgrid.com/free/)無料の SendGrid アカウントを登録します。

### <a name="implement-iemailsender"></a>IEmailSender を実装します。

実装`IEmailSender`、作成*Services/EmailSender.cs*次のようなコードで。

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>電子メールをサポートするために起動時を構成します。

次のコードを追加、`ConfigureServices`メソッドで、 *Startup.cs*ファイル。

* 追加`EmailSender`一時的なサービスとして。
* 登録、`AuthMessageSenderOptions`構成インスタンス。

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a>登録、確認の電子メール、およびパスワードのリセット

Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。

* アプリを実行し、新しいユーザーの登録
* アカウント確認用のリンクは、電子メールを確認します。 参照してください[デバッグ電子メール](#debug)電子メールが届かない場合。
* 電子メールを確認するリンクをクリックします。
* 電子メール アドレスとパスワードでサインインします。
* サインアウトします。

### <a name="test-password-reset"></a>テストのパスワードのリセット

* サインインしている場合は、選択**ログアウト**します。
* 選択、**ログイン**リンクし、選択、**パスワードを忘れた場合でしょうか。** リンク。
* アカウントを登録するために使用する電子メール アドレスを入力します。
* パスワードをリセットするリンクを含む電子メールが送信されます。 電子メールを確認し、パスワードをリセットするリンクをクリックします。 パスワードが正常にリセットされると後、はの電子メール アドレスと新しいパスワードでサインインすることができます。

## <a name="change-email-and-activity-timeout"></a>電子メールとアクティビティのタイムアウトを変更します。

既定のアイドル タイムアウトは、14 日間です。 次のコードでは、アイドル タイムアウトを 5 日に設定します。

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>すべてのデータの保護トークン lifespans を変更します。

次のコードは、すべてのデータの保護トークンのタイムアウト期間を 3 時間に変更します。

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

組み込みの Id のユーザー トークン (を参照してください[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) が、 [1 日のタイムアウト](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。

### <a name="change-the-email-token-lifespan"></a>電子メールのトークン有効期間を変更します。

既定のトークン有効期間[Id のユーザー トークン](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)は[1 日](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)します。 このセクションでは、電子メールのトークン有効期間を変更する方法を示します。

追加のカスタム[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)と<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

サービス コンテナーには、カスタム プロバイダーを追加します。

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>確認の電子メールを再送信します。

参照してください[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5410)します。

<a name="debug"></a>

### <a name="debug-email"></a>電子メールをデバッグします。

電子メールの作業が発生したことはできません: 場合

* ブレークポイントを設定`EmailSender.Execute`を確認する`SendGridClient.SendEmailAsync`が呼び出されます。
* 作成、[電子メールを送信するためのコンソール アプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)に同様のコードを使用して`EmailSender.Execute`します。
* レビュー、[電子メール アクティビティ](https://sendgrid.com/docs/User_Guide/email_activity.html)ページ。
* 迷惑メール フォルダーを確認します。
* 別のメール プロバイダー (Microsoft、Yahoo、Gmail など) に別の電子メール エイリアスをお試しください。
* 別のメール アカウントへの送信を再試行してください。

**セキュリティのベスト プラクティス**は**いない**テストと開発における運用シークレットを使用します。 アプリを Azure に発行する場合は、Web アプリを Azure portal でアプリケーション設定と SendGrid シークレットを設定します。 構成システムは、環境変数からキーの読み取りを設定します。

## <a name="combine-social-and-local-login-accounts"></a>ソーシャル、ローカルのログイン アカウントを組み合わせる

このセクションを完了するには、まず、外部認証プロバイダーを有効にする必要があります。 参照してください[Facebook、Google、および外部プロバイダー認証](xref:security/authentication/social/index)します。

電子メールのリンクをクリックして、ローカルおよびソーシャル アカウントを組み合わせることができます。 次の順序で"RickAndMSFT@gmail.com"が最初に、ローカルのログインとして作成ただし、最初に、ソーシャル ログインとしてアカウントを作成し、ローカル ログインを追加します。

![Web アプリケーション:RickAndMSFT@gmail.com認証されたユーザー](accconfirm/_static/rick.png)

をクリックして、**管理**リンク。 このアカウントに関連付けられた 0 外部 (ソーシャル ログイン) に注意してください。

![ビューを管理します。](accconfirm/_static/manage.png)

別のログイン サービスへのリンクをクリックし、アプリの要求をそのまま使用します。 次の図では、Facebook は、外部認証プロバイダーは。

![Facebook を一覧表示する、外部ログイン ビューを管理します。](accconfirm/_static/fb.png)

2 つのアカウントが統合されました。 いずれかのアカウントでサインインすることは。 ユーザーがソーシャル ログインの認証サービスがダウンしているか、自分のソーシャル アカウントにアクセスを紛失した可能性が高い場合に、ローカル アカウントを追加することができます。

## <a name="enable-account-confirmation-after-a-site-has-users"></a>サイトがユーザー アカウントの確認を有効にします。

ユーザーとサイトのアカウント確認を有効にする既存のすべてのユーザーをロックアウトします。 自分のアカウントが確認されないために、既存のユーザーはロックアウトされます。 既存のユーザーのロックアウトを回避するには、次の方法のいずれかを使用します。

* 確認されているすべての既存ユーザーをマークするデータベースを更新します。
* 既存のユーザーを確認します。 確認リンクを含むメールなどのバッチの送信をします。

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a>必須コンポーネント

[.NET core 2.2 SDK またはそれ以降](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>Web アプリを作成し、Identity のスキャフォールディング

認証を使用した web アプリを作成するには、次のコマンドを実行します。

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

## <a name="test-new-user-registration"></a>新規ユーザー登録をテストします。

アプリを実行し、選択、**登録**リンク、およびユーザーを登録します。 この時点では、電子メールの検証のみ、 [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性。 登録を送信した後は、アプリにログインします。 チュートリアルの後半では、自分の電子メールが検証されるまでに新しいユーザーがサインインできないように、コードが更新されます。

[!INCLUDE[](~/includes/view-identity-db.md)]

テーブルに注意してください`EmailConfirmed`フィールドは`False`します。

アプリが送信された確認メールを送信するとき、次の手順でこの電子メールをもう一度使用する場合があります。 クリックし、行を右クリックして**削除**します。 電子メール エイリアスを削除する簡単で、次の手順。

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>確認の電子メールが必要です。

新規ユーザー登録の電子メール アドレスを確認することをお勧めします。 電子メールの他のユーザーが偽装はいないしていることを確認することを確認できます (つまりに登録していない他のユーザーの電子メールで)。 ディスカッション フォーラムでは、行われていればし、しないようにする"yli@example.comとして登録する"から"nolivetto@contoso.com"。 電子メールの確認なし"nolivetto@contoso.com"アプリから不要な電子メールを受け取ることができます。 ユーザーが誤ってとして登録されているとします"ylo@example.com""yli"のスペル ミスを認識していなかったとします。 アプリは、正しいメール アドレスがあるないために、パスワードの回復を使用できるでしょう。 確認の電子メールは、ボットからの限られた保護を提供します。 確認の電子メールは、多くの電子メール アカウントを使用して悪意のあるユーザーから保護を提供しません。

新しいユーザーが確認された電子メールを受ける前に、web サイトにデータを送信するを防ぐために一般的にします。

Update`Startup.ConfigureServices`確認された電子メールを要求します。

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` 登録済みのユーザーで自分の電子メールを確認するまでのログ記録はできません。

### <a name="configure-email-provider"></a>電子メール プロバイダーを構成します。

このチュートリアルで[SendGrid](https://sendgrid.com)電子メールを送信するために使用します。 SendGrid アカウントとキーが電子メールを送信する必要があります。 その他の電子メール プロバイダーを使用することができます。 ASP.NET Core の 2.x を含む`System.Net.Mail`、アプリから電子メールを送信できます。 SendGrid または別の電子メール サービスを使用して電子メールを送信することをお勧めします。 SMTP では、セキュリティで保護し、設定を正しく困難です。

電子メールをセキュリティで保護されたキーを取得するためのクラスを作成します。 このサンプルでは、次のように作成します*Services/AuthMessageSenderOptions.cs*:。

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid のユーザーの機密情報を構成します。

設定、`SendGridUser`と`SendGridKey`で、 [secret manager ツール](xref:security/app-secrets)します。 例:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Secret Manager、Windows 上のキー/値のペアが格納、 *secrets.json*ファイル、`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`ディレクトリ。

内容、 *secrets.json*ファイルは暗号化されません。 次のマークアップに示す、 *secrets.json*ファイル。 `SendGridKey`値が削除されました。

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

詳細については、次を参照してください。、[オプション パターン](xref:fundamentals/configuration/options)と[構成](xref:fundamentals/configuration/index)します。

### <a name="install-sendgrid"></a>SendGrid をインストールします。

このチュートリアルは、使用して電子メール通知を追加する方法を示します[SendGrid](https://sendgrid.com/)が SMTP およびその他のメカニズムを使用して電子メールを送信できます。

インストール、 `SendGrid` NuGet パッケージ。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

パッケージ マネージャー コンソールで、次のコマンドを入力します。

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

コンソールで、次のコマンドを入力します。

```cli
dotnet add package SendGrid
```

---

参照してください[SendGrid を無料で開始する](https://sendgrid.com/free/)無料の SendGrid アカウントを登録します。

### <a name="implement-iemailsender"></a>IEmailSender を実装します。

実装`IEmailSender`、作成*Services/EmailSender.cs*次のようなコードで。

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>電子メールをサポートするために起動時を構成します。

次のコードを追加、`ConfigureServices`メソッドで、 *Startup.cs*ファイル。

* 追加`EmailSender`一時的なサービスとして。
* 登録、`AuthMessageSenderOptions`構成インスタンス。

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>アカウントの確認とパスワードの回復を有効にします。

テンプレートには、アカウントの確認とパスワードの回復用コードがあります。 検索、`OnPostAsync`メソッド*Areas/Identity/Pages/Account/Register.cshtml.cs*します。

新しく登録されたユーザーが、次の行をコメント アウトして自動的にサインインするようにします。

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

メソッド全体を強調表示されている変更された行が表示されます。

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>登録、確認の電子メール、およびパスワードのリセット

Web アプリを実行し、アカウントの確認とパスワードの回復フローをテストします。

* アプリを実行し、新しいユーザーの登録
* アカウント確認用のリンクは、電子メールを確認します。 参照してください[デバッグ電子メール](#debug)電子メールが届かない場合。
* 電子メールを確認するリンクをクリックします。
* 電子メール アドレスとパスワードでサインインします。
* サインアウトします。

### <a name="view-the-manage-page"></a>ビューの管理 ページ

ブラウザーで、ユーザー名を選択:![ユーザー名を備えたブラウザー ウィンドウ](accconfirm/_static/un.png)

管理ページが表示されますが、**プロファイル**タブを選択します。 **電子メール**電子メールを示すチェック ボックスが確認されているかを示します。

### <a name="test-password-reset"></a>テストのパスワードのリセット

* サインインしている場合は、選択**ログアウト**します。
* 選択、**ログイン**リンクし、選択、**パスワードを忘れた場合でしょうか。** リンク。
* アカウントを登録するために使用する電子メール アドレスを入力します。
* パスワードをリセットするリンクを含む電子メールが送信されます。 電子メールを確認し、パスワードをリセットするリンクをクリックします。 パスワードが正常にリセットされると後、はの電子メール アドレスと新しいパスワードでサインインすることができます。

## <a name="change-email-and-activity-timeout"></a>電子メールとアクティビティのタイムアウトを変更します。

既定のアイドル タイムアウトは、14 日間です。 次のコードでは、アイドル タイムアウトを 5 日に設定します。

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>すべてのデータの保護トークン lifespans を変更します。

次のコードは、すべてのデータの保護トークンのタイムアウト期間を 3 時間に変更します。

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

組み込みの Id のユーザー トークン (を参照してください[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) が、 [1 日のタイムアウト](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。

### <a name="change-the-email-token-lifespan"></a>電子メールのトークン有効期間を変更します。

既定のトークン有効期間[Id のユーザー トークン](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)は[1 日](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)します。 このセクションでは、電子メールのトークン有効期間を変更する方法を示します。

追加のカスタム[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)と<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

サービス コンテナーには、カスタム プロバイダーを追加します。

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>確認の電子メールを再送信します。

参照してください[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/5410)します。

<a name="debug"></a>

### <a name="debug-email"></a>電子メールをデバッグします。

電子メールの作業が発生したことはできません: 場合

* ブレークポイントを設定`EmailSender.Execute`を確認する`SendGridClient.SendEmailAsync`が呼び出されます。
* 作成、[電子メールを送信するためのコンソール アプリ](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)に同様のコードを使用して`EmailSender.Execute`します。
* レビュー、[電子メール アクティビティ](https://sendgrid.com/docs/User_Guide/email_activity.html)ページ。
* 迷惑メール フォルダーを確認します。
* 別のメール プロバイダー (Microsoft、Yahoo、Gmail など) に別の電子メール エイリアスをお試しください。
* 別のメール アカウントへの送信を再試行してください。

**セキュリティのベスト プラクティス**は**いない**テストと開発における運用シークレットを使用します。 アプリを Azure に発行する場合は、Web アプリを Azure portal でアプリケーション設定と SendGrid シークレットを設定できます。 構成システムは、環境変数からキーの読み取りを設定します。

## <a name="combine-social-and-local-login-accounts"></a>ソーシャル、ローカルのログイン アカウントを組み合わせる

このセクションを完了するには、まず、外部認証プロバイダーを有効にする必要があります。 参照してください[Facebook、Google、および外部プロバイダー認証](xref:security/authentication/social/index)します。

電子メールのリンクをクリックして、ローカルおよびソーシャル アカウントを組み合わせることができます。 次の順序で"RickAndMSFT@gmail.com"が最初に、ローカルのログインとして作成ただし、最初に、ソーシャル ログインとしてアカウントを作成し、ローカル ログインを追加します。

![Web アプリケーション:RickAndMSFT@gmail.com認証されたユーザー](accconfirm/_static/rick.png)

をクリックして、**管理**リンク。 このアカウントに関連付けられた 0 外部 (ソーシャル ログイン) に注意してください。

![ビューを管理します。](accconfirm/_static/manage.png)

別のログイン サービスへのリンクをクリックし、アプリの要求をそのまま使用します。 次の図では、Facebook は、外部認証プロバイダーは。

![Facebook を一覧表示する、外部ログイン ビューを管理します。](accconfirm/_static/fb.png)

2 つのアカウントが統合されました。 いずれかのアカウントでサインインすることは。 ユーザーがソーシャル ログインの認証サービスがダウンしているか、自分のソーシャル アカウントにアクセスを紛失した可能性が高い場合に、ローカル アカウントを追加することができます。

## <a name="enable-account-confirmation-after-a-site-has-users"></a>サイトがユーザー アカウントの確認を有効にします。

ユーザーとサイトのアカウント確認を有効にする既存のすべてのユーザーをロックアウトします。 自分のアカウントが確認されないために、既存のユーザーはロックアウトされます。 既存のユーザーのロックアウトを回避するには、次の方法のいずれかを使用します。

* 確認されているすべての既存ユーザーをマークするデータベースを更新します。
* 既存のユーザーを確認します。 確認リンクを含むメールなどのバッチの送信をします。

::: moniker-end
