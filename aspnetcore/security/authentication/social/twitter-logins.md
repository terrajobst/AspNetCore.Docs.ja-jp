---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5d0695160d90d0c5d31b8e35bc6c4cc984829333
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944214"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core を使用した Twitter の外部サインインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。

## <a name="create-the-app-in-twitter"></a>Twitter でアプリを作成する

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet パッケージをプロジェクトに追加します。

* 移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。 まだ Twitter アカウントをお持ちでない場合は、[ **[今すぐサインアップ](https://twitter.com/signup)** ] リンクを使用して作成します。

* **[アプリの作成]** を選択します **アプリ名**、**アプリケーションの説明**、およびパブリック**web サイト**の URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。

* **[コールバック url]** フィールドに `/signin-twitter` 追加された開発 URI を入力します (例: `https://webapp128.azurewebsites.net/signin-twitter`)。 このサンプルの後半で構成されている Twitter 認証スキームは、OAuth フローを実装するために `/signin-twitter` ルートで要求を自動的に処理します。

  > [!NOTE]
  > `/signin-twitter` URI セグメントは、Twitter 認証プロバイダーの既定のコールバックとして設定されます。 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

* フォームの残りの部分を入力し、 **[作成]** を選択します。 新しいアプリケーションの詳細が表示されます。

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Twitter コンシューマー API キーとシークレットを格納する

次のコマンドを実行して、[シークレットマネージャー](xref:security/app-secrets)を使用して `ClientId` と `ClientSecret` を安全に格納します。

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

[Secret Manager](xref:security/app-secrets)を使用して、Twitter `Consumer Key` や `Consumer Secret` などの機密性の高い設定をアプリケーション構成にリンクします。 このサンプルでは、トークンに `Authentication:Twitter:ConsumerKey` と `Authentication:Twitter:ConsumerSecret`という名前を指定します。

これらのトークンは、新しい Twitter アプリケーションを作成した後の **[キーとアクセストークン]** タブにあります。

## <a name="configure-twitter-authentication"></a>Twitter 認証の構成

*Startup.cs*ファイルの `ConfigureServices` メソッドに Twitter サービスを追加します。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Twitter 認証でサポートされる構成オプションの詳細については、 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="sign-in-with-twitter"></a>Twitter でのサインイン

アプリを実行し、 **[ログイン]** を選択します。 Twitter でサインインするためのオプションが表示されます。

**Twitter**をクリックすると、認証のために twitter にリダイレクトされるようになります。

Twitter の資格情報を入力すると、電子メールを設定できる web サイトにリダイレクトされます。

これで、Twitter の資格情報を使用してログインします。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NET Core 2.x のみ:** 呼び出すことによって構成されていない場合の Identity`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException: 'SignInScheme' オプションを指定する必要があります*します。 このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。
* 最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。 タップ**適用移行**データベースを作成し、エラーを引き続き更新します。

## <a name="next-steps"></a>次のステップ:

* この記事では、Twitter で認証する方法について説明しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。

* Web サイトを Azure web アプリに発行したら、Twitter 開発者ポータルで `ConsumerSecret` をリセットする必要があります。

* 設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。
