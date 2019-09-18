---
title: ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ
author: rick-anderson
description: このサンプルでは、Microsoft アカウントユーザー認証を既存の ASP.NET Core アプリに統合する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082338"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 2.2 プロジェクトを使用して、ユーザーが Microsoft アカウントでサインインできるようにする方法を示します。

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 開発者ポータルでアプリを作成する

* [Azure portal アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。

Microsoft アカウントがない場合は、 **[作成]** を選択します。 サインインすると、 **[アプリの登録]** ページにリダイレクトされます。

* **新しい登録**の選択
* **名前**を入力してください。
* **サポートされているアカウントの種類**のオプションを選択します。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* **[リダイレクト URI]** に、追加した`/signin-microsoft`開発 URL を入力します。 たとえば、`https://localhost:44389/signin-microsoft` のようにします。 このサンプルの後半で構成されている Microsoft 認証スキームは`/signin-microsoft` 、OAuth フローを実装するために、ルートで要求を自動的に処理します。
* **登録**の選択

### <a name="create-client-secret"></a>クライアントシークレットの作成

* 左側のウィンドウで、 **[証明書 & シークレット]** を選択します。
* **[クライアントシークレット]** で、 **[新しいクライアントシークレット]** を選択します。

  * クライアントシークレットの説明を追加します。
  * **[追加]** ボタンを選択します。

* **[クライアントシークレット]** で、クライアントシークレットの値をコピーします。

> [!NOTE]
> URI セグメント`/signin-microsoft`は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Microsoft 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Microsoft クライアント ID とクライアントシークレットを保存する

次のコマンドを実行して`ClientId` 、 `ClientSecret` [シークレットマネージャー](xref:security/app-secrets)を安全に格納して使用します。

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

[シークレットマネージャー](xref:security/app-secrets)を使用し`ClientId`て`ClientSecret` 、Microsoft やなどの機密性の高い設定をアプリケーション構成にリンクします。 このサンプルでは、トークン`Authentication:Microsoft:ClientId`にとと`Authentication:Microsoft:ClientSecret`いう名前を指定します。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft アカウント認証を構成する

Microsoft アカウントサービスをに`Startup.ConfigureServices`追加します。

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Microsoft アカウント認証でサポートされる構成オプションの詳細については、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="sign-in-with-microsoft-account"></a>Microsoft アカウントでサインインアカウント

を実行し、 **[ログイン]** をクリックします。 Microsoft でサインインするためのオプションが表示されます。 [Microsoft] をクリックすると、認証のために Microsoft にリダイレクトされます。 Microsoft アカウントでサインインした後 (まだサインインしていない場合)、アプリに情報へのアクセスを許可するように求められます。

**[はい]** をタップすると、電子メールを設定できる web サイトにリダイレクトされます。

これで、Microsoft 資格情報を使用してログインしました。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* Microsoft アカウントプロバイダーによってサインインエラーページが表示された場合は、Uri の (ハッシュタグ) の`#`すぐ後にあるエラータイトルと説明のクエリ文字列パラメーターを確認してください。

  エラーメッセージは Microsoft 認証に問題があることを示していますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定されている**リダイレクト uri**と一致していないことです。
* で`services.AddIdentity` *を呼び出すことによって id が構成されていない場合、認証を試みると ArgumentException が発生します。 `ConfigureServices`' SignInScheme ' オプションを指定*する必要があります。 このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。
* 最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。 タップ**適用移行**データベースを作成し、エラーを引き続き更新します。

## <a name="next-steps"></a>次の手順

* この記事では、Microsoft で認証する方法について説明しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。

* Web サイトを Azure web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアントシークレットを作成します。

* 設定、`Authentication:Microsoft:ClientId`と`Authentication:Microsoft:ClientSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。