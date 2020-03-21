---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
uid: security/authentication/google-logins
ms.openlocfilehash: a114d23c25201c9fe31ad0397efaf99fe98a312a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989768"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core での Google 外部ログインのセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが自分の Google アカウントでサインインできるようにする方法について説明します。

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API コンソールプロジェクトとクライアント ID を作成する

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)をインストールします。
* [「Google サインインを web アプリに統合](https://developers.google.com/identity/sign-in/web/devconsole-project)する」に移動し、 **[プロジェクトの構成]** を選択します。
* **[OAuth クライアントの構成]** ダイアログで、 **[Web サーバー]** を選択します。
* [承認された**リダイレクト uri**のテキスト入力] ボックスで、リダイレクト uri を設定します。 たとえば、`https://localhost:44312/signin-google` のように指定します。
* **クライアント ID**と**クライアントシークレット**を保存します。
* サイトをデプロイするときに、新しいパブリック url を**Google コンソール**から登録します。

## <a name="store-the-google-client-id-and-secret"></a>Google クライアント ID とシークレットを保存する

Google クライアント ID やシークレット値などの機微な設定を[Secret Manager](xref:security/app-secrets)に保存します。 このサンプルでは、次の手順を使用します。

1. 「[シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。
1. 秘密キー `Authentication:Google:ClientId` と `Authentication:Google:ClientSecret`を使用して、ローカルシークレットストアに機密設定を格納します。

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Api[コンソール](https://console.developers.google.com/apis/dashboard)で api の資格情報と使用状況を管理できます。

## <a name="configure-google-authentication"></a>Google 認証を構成する

`Startup.ConfigureServices`に Google サービスを追加します。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google でサインイン

* アプリを実行し、 **[ログイン]** をクリックします。 Google でサインインするためのオプションが表示されます。
* **[Google]** ボタンをクリックします。これにより、認証のために google にリダイレクトされます。
* Google の資格情報を入力すると、web サイトにリダイレクトされます。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Google 認証でサポートされる構成オプションの詳細については、<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="change-the-default-callback-uri"></a>既定のコールバック URI を変更する

`/signin-google` URI セグメントは、Google 認証プロバイダーの既定のコールバックとして設定されます。 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Google 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

## <a name="troubleshooting"></a>トラブルシューティング

* サインインが機能せず、エラーが発生しない場合は、開発モードに切り替えて、問題を簡単にデバッグできるようにします。
* `ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合は、 *ArgumentException: ' SignInScheme ' オプションを指定する必要があり*ます。 このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。
* 初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。 **[移行の適用]** を選択してデータベースを作成し、ページを更新してエラーを解消します。

## <a name="next-steps"></a>次のステップ:

* この記事では、Google で認証する方法を示しました。 同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。
* アプリを Azure に発行したら、Google API コンソールで `ClientSecret` をリセットします。
* Azure portal で、`Authentication:Google:ClientId` と `Authentication:Google:ClientSecret` をアプリケーション設定として設定します。 構成システムは、環境変数からキーの読み取りを設定します。
