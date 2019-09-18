---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082483"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core での Google 外部ログインのセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

[レガシ Google + api は2019年3月7日からシャットダウンされまし](https://developers.google.com/+/api-shutdown)た。 Google + サインインと開発者は、新しい Google サインインシステムに移行する必要があります。 Google 認証用の ASP.NET Core 2.1 および2.2 パッケージは、変更内容に合わせて更新されます。 ASP.NET Core の詳細と一時的な軽減策については、 [GitHub の問題](https://github.com/aspnet/AspNetCore/issues/6486)を参照してください。 このチュートリアルは、新しいセットアッププロセスで更新されました。

このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 2.2 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API コンソールプロジェクトとクライアント ID を作成する

* [「Google サインインを web アプリに統合](https://developers.google.com/identity/sign-in/web/devconsole-project)する」に移動し、 **[プロジェクトの構成]** を選択します。
* **[OAuth クライアントの構成]** ダイアログで、 **[Web サーバー]** を選択します。
* [承認された**リダイレクト uri**のテキスト入力] ボックスで、リダイレクト uri を設定します。 たとえば、`https://localhost:5001/signin-google`
* **クライアント ID**と**クライアントシークレット**を保存します。
* サイトをデプロイするときに、新しいパブリック url を**Google コンソール**から登録します。

## <a name="store-google-clientid-and-clientsecret"></a>ストア Google ClientID と ClientSecret

Google `Client ID`や`Client Secret` [Secret Manager](xref:security/app-secrets)などの機密性の高い設定を格納します。 このチュートリアルでは、トークン`Authentication:Google:ClientId`にとと`Authentication:Google:ClientSecret`いう名前を指定します。

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Api[コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。

## <a name="configure-google-authentication"></a>Google 認証を構成する

Google サービスをに`Startup.ConfigureServices`追加します。

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google のサインイン

* アプリを実行し、 **[ログイン]** をクリックします。 Google でサインインするためのオプションが表示されます。
* **[Google]** ボタンをクリックします。これにより、認証のために google にリダイレクトされます。
* Google の資格情報を入力すると、web サイトにリダイレクトされます。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Google 認証<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>でサポートされる構成オプションの詳細については、「API リファレンス」を参照してください。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="change-the-default-callback-uri"></a>既定のコールバック URI を変更する

URI セグメント`/signin-google`Google の認証プロバイダーの既定のコールバックとして設定されます。 既定のコールバック URI を変更するには、継承を使用して、Google の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラス。

## <a name="troubleshooting"></a>トラブルシューティング

* サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。
* で`services.AddIdentity` *を呼び出すことによって id が構成されていない場合、ArgumentException で結果を認証しようとしています。 `ConfigureServices`' SignInScheme ' オプションを指定*する必要があります。 このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。
* 取得する場合は、初期移行を適用することで、サイト データベースが作成されていない*要求の処理中にデータベース操作が失敗しました*エラー。 **[移行の適用]** を選択してデータベースを作成し、ページを更新してエラーを解消します。

## <a name="next-steps"></a>次の手順

* この記事では、Google で認証する方法を示しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。
* アプリを Azure に発行したら、Google API `ClientSecret`コンソールでをリセットします。
* 設定、`Authentication:Google:ClientId`と`Authentication:Google:ClientSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。
