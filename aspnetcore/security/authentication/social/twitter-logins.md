---
title: ASP.NET Core での twitter の外部サインイン設定
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Twitter アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: d816ed27898639b0af6896a51ac035d5526c5d29
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814068"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core での twitter の外部サインイン設定

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルは、ユーザーを有効にする方法を示しています。 [、Twitter アカウントでサインイン](https://dev.twitter.com/web/sign-in/desktop-browser)で作成されたサンプルの ASP.NET Core 2.2 プロジェクトを使用して、[前のページ](xref:security/authentication/social/index)します。

## <a name="create-the-app-in-twitter"></a>Twitter でアプリケーションを作成します。

* 移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。 Twitter アカウントがない場合は、使用、 **[今すぐサインアップ](https://twitter.com/signup)** リンクを作成します。

* タップ**Create New App**アプリケーション記入と**名前**、**説明**とパブリック**web サイト**URI (このことは一時的なものまで、ドメイン名を登録)。

* 開発 URI を入力と`/signin-twitter`に追加されます、**有効な OAuth リダイレクト Uri**フィールド (例: `https://webapp128.azurewebsites.net/signin-twitter`)。 このサンプルでは後で構成されている Twitter 認証の構成はで、要求を自動的に処理`/signin-twitter`OAuth フローを実装するルート。

  > [!NOTE]
  > URI セグメント`/signin-twitter`Twitter の認証プロバイダーの既定のコールバックとして設定されます。 既定のコールバック URI を変更するには、継承を使用して、Twitter 認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラス。

* フォームの残りの部分を入力し、タップ**Twitter アプリケーションを作成**です。 新しいアプリケーションの詳細が表示されます。

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Twitter のコンシューマー API キーとシークレットを格納します。

安全に保存するには、次のコマンドを実行`ClientId`と`ClientSecret`を使用して[Secret Manager](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

Twitter などの機密設定をリンク`Consumer Key`と`Consumer Secret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。 このサンプルの目的で、名前トークン`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`します。

これらのトークンで確認できます、 **Keys and Access Tokens**新しい Twitter アプリケーションを作成した後タブ。

## <a name="configure-twitter-authentication"></a>Twitter 認証を構成します。

Twitter サービスを追加、`ConfigureServices`メソッド*Startup.cs*ファイル。

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

参照してください、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 認証でサポートされる構成オプションの詳細について、API リファレンス。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="sign-in-with-twitter"></a>Sign in with Twitter

アプリを実行し、選択**ログイン**します。 Twitter でサインインするためのオプションが表示されます。

クリックすると**Twitter**認証の Twitter にリダイレクトします。

Twitter の資格情報を入力した後、電子メールを設定する web サイトにリダイレクトされます。

これで、Twitter の資格情報を使用してをログインしています。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NET Core 2.x のみ。** ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。 このサンプルで使用するプロジェクト テンプレートによりこれが行われるようになります。
* 最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。 タップ**適用移行**データベースを作成し、エラーを引き続き更新します。

## <a name="next-steps"></a>次の手順

* この記事では、Twitter に認証する方法を示しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。

* リセットする必要があります、web サイトを Azure web アプリを発行すると、 `ConsumerSecret` Twitter の開発者ポータルでします。

* 設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。