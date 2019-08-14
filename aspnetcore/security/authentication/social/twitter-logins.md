---
title: ASP.NET Core を使用した Twitter の外部サインインセットアップ
author: rick-anderson
description: このチュートリアルでは、Twitter アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 6b6fa3e50f602a92fec9112ac3ba43583de33a70
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994278"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core を使用した Twitter の外部サインインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 2.2 プロジェクトを使用して、ユーザーが[Twitter アカウントを](https://dev.twitter.com/web/sign-in/desktop-browser)使用してサインインできるようにする方法を示します。

## <a name="create-the-app-in-twitter"></a>Twitter でアプリを作成する

* 移動します[ https://apps.twitter.com/ ](https://apps.twitter.com/)してサインインします。 まだ Twitter アカウントをお持ちでない場合は、[ **[今すぐサインアップ](https://twitter.com/signup)** ] リンクを使用して作成します。

* **[新しいアプリの作成]** をタップし、アプリケーション**名**、**説明**、およびパブリック**web サイト**URI を入力します (これは、ドメイン名を登録するまで一時的な場合があります)。

* **[有効な OAuth リダイレクト uri]** フィールドに追加された`https://webapp128.azurewebsites.net/signin-twitter` `/signin-twitter`開発 URI を入力します (例:)。 このサンプルで後で構成される Twitter 認証スキームは、OAuth `/signin-twitter`フローを実装するために、ルートで要求を自動的に処理します。

  > [!NOTE]
  > URI セグメント`/signin-twitter`は、Twitter 認証プロバイダーの既定のコールバックとして設定されます。 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して、Twitter 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

* フォームの残りの部分を入力し、 **[Create Your Twitter application]** をタップします。 新しいアプリケーションの詳細が表示されます。

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Twitter コンシューマー API キーとシークレットを格納する

次のコマンドを実行して`ClientId` 、 `ClientSecret` [シークレットマネージャー](xref:security/app-secrets)を安全に格納して使用します。

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

[シークレットマネージャー](xref:security/app-secrets)を使用し`Consumer Key`て`Consumer Secret` 、Twitter やなどの機密性の高い設定をアプリケーション構成にリンクします。 このサンプルでは、トークン`Authentication:Twitter:ConsumerKey`にとと`Authentication:Twitter:ConsumerSecret`いう名前を指定します。

これらのトークンは、新しい Twitter アプリケーションを作成した後の **[キーとアクセストークン]** タブにあります。

## <a name="configure-twitter-authentication"></a>Twitter 認証の構成

`ConfigureServices` *Startup.cs*ファイルのメソッドに Twitter サービスを追加します。

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

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

* **ASP.NET Core 2.x のみ:** で`services.AddIdentity` *を呼び出すことによって id が構成されていない場合、認証を試みると ArgumentException が発生します。 `ConfigureServices`' SignInScheme ' オプションを指定*する必要があります。 このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。
* 最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。 タップ**適用移行**データベースを作成し、エラーを引き続き更新します。

## <a name="next-steps"></a>次の手順

* この記事では、Twitter で認証する方法について説明しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。

* Web サイトを Azure web アプリに発行したら、Twitter 開発者ポータル`ConsumerSecret`でをリセットする必要があります。

* 設定、`Authentication:Twitter:ConsumerKey`と`Authentication:Twitter:ConsumerSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。
