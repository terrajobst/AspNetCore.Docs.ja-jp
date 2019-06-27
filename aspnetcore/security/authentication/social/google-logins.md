---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: b0edac411e73cd2eec7c4e212b99971577f59cfb
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316449"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core での Google 外部ログインのセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[従来の Google + Api が、2019 年 3 月 7 日の時点でシャット ダウンされた](https://developers.google.com/+/api-shutdown)します。 Google + にサインインして、開発者は、システムで新しい Google サインインを移動する必要があります。 Google 認証用の ASP.NET Core 2.1、2.2 パッケージは、変化に対応する更新があります。 詳細と ASP.NET Core 用の一時的な軽減策は、次を参照してください。[この GitHub の問題](https://github.com/aspnet/AspNetCore/issues/6486)します。 このチュートリアルは、新しいセットアップ プロセスで更新されました。

このチュートリアルでは、ASP.NET Core 2.2 プロジェクトが作成を使用して、Google アカウントでサインインするユーザーを有効にする方法、[前のページ](xref:security/authentication/social/index)します。

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API コンソール プロジェクトとクライアント ID を作成します。

* 移動します[統合で Google サインインを web アプリに](https://developers.google.com/identity/sign-in/web/devconsole-project)選択**A プロジェクトの構成**します。
* **OAuth クライアントを構成する**ダイアログ ボックスで、 **Web server**します。
* **承認済みのリダイレクト Uri**テキスト入力ボックス、リダイレクト URI を設定します。 たとえば、`https://localhost:5001/signin-google`
* 保存、**クライアント ID**と**クライアント シークレット**します。
* 新しいパブリック url の登録、サイトをデプロイするとき、 **Google コンソール**します。

## <a name="store-google-clientid-and-clientsecret"></a>ストア Google ClientID と ClientSecret

Google などの機密設定を保存`Client ID`と`Client Secret`で、 [Secret Manager](xref:security/app-secrets)します。 このチュートリアルの目的で、名前トークン`Authentication:Google:ClientId`と`Authentication:Google:ClientSecret`:

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

API の資格情報と使用量を管理することができます、 [API コンソール](https://console.developers.google.com/apis/dashboard)します。

## <a name="configure-google-authentication"></a>Google 認証を構成します。

Google サービスを追加`Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google のサインイン

* アプリを実行し、をクリックして**ログイン**します。 Google でサインインするためのオプションが表示されます。
* をクリックして、 **Google**ボタンで、認証の Google にリダイレクトします。
* Google の資格情報を入力した後は、web サイトにリダイレクトされます。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

参照してください、 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> Google 認証でサポートされる構成オプションの詳細について、API リファレンス。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="change-the-default-callback-uri"></a>既定のコールバック URI を変更します。

URI セグメント`/signin-google`Google の認証プロバイダーの既定のコールバックとして設定されます。 既定のコールバック URI を変更するには、継承を使用して、Google の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラス。

## <a name="troubleshooting"></a>トラブルシューティング

* サインインでは機能しません、エラー通知が届かない場合は、問題をデバッグしやすくする開発モードに切り替えます。
* ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`で結果を認証しようとすると、 *ArgumentException:'SignInScheme' オプションを指定する必要があります*します。 このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。
* 取得する場合は、初期移行を適用することで、サイト データベースが作成されていない*要求の処理中にデータベース操作が失敗しました*エラー。 選択**適用移行**データベースを作成し、エラーを続行するページを更新します。

## <a name="next-steps"></a>次の手順

* この記事では、Google で認証する方法を示しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。
* アプリを Azure に発行すると、リセット、 `ClientSecret` Google API コンソールでします。
* 設定、`Authentication:Google:ClientId`と`Authentication:Google:ClientSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。
