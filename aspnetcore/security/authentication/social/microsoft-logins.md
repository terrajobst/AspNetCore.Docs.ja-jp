---
title: ASP.NET Core での Microsoft アカウントの外部ログインのセットアップ
author: rick-anderson
description: このサンプルでは、既存の ASP.NET Core アプリケーションを Microsoft アカウント ユーザーの認証の統合を示します。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815576"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core での Microsoft アカウントの外部ログインのセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルは、ASP.NET Core 2.2 プロジェクトが作成を使用して、Microsoft アカウントでサインインするユーザーを有効にする方法を示します、[前のページ](xref:security/authentication/social/index)します。

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 開発者ポータルで、アプリを作成します。

* 移動し、 [Azure portal - アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページで、作成するか、Microsoft アカウントにサインインします。

Microsoft アカウントを持っていない場合は、選択**作成**です。 サインインした後にリダイレクトされたら、**アプリの登録**ページ。

* 選択**新規登録**
* 入力、**名前**します。
* オプションを選択**勘定科目の種類がサポートされている**します。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* **リダイレクト URI**で、開発の URL を入力`/signin-microsoft`追加されます。 たとえば、`https://localhost:44389/signin-microsoft` のようにします。 このサンプルでは後で構成されている Microsoft の認証構成はで、要求を自動的に処理`/signin-microsoft`OAuth フローを実装するルート。
* 選択**登録**

### <a name="create-client-secret"></a>クライアント シークレットを作成します。

* 左側のウィンドウで次のように選択します。**証明書およびシークレット**します。
* **クライアント シークレット**、**新しいクライアント シークレット**

  * クライアント シークレットの説明を追加します。
  * 選択、**追加**ボタンをクリックします。

* **クライアント シークレット**、クライアント シークレットの値をコピーします。

> [!NOTE]
> URI セグメント`/signin-microsoft`Microsoft 認証プロバイダーの既定のコールバックとして設定されます。 既定のコールバック URI を変更するには、継承を使用して、Microsoft の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラス。

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Microsoft のクライアント ID とクライアント シークレットを格納します。

安全に保存するには、次のコマンドを実行`ClientId`と`ClientSecret`を使用して[Secret Manager](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Microsoft のような機密性の高い設定リンク`ClientId`と`ClientSecret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。 このサンプルの目的で、名前トークン`Authentication:Microsoft:ClientId`と`Authentication:Microsoft:ClientSecret`します。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft アカウント認証を構成します。

Microsoft アカウント サービスを追加`Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

参照してください、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスの詳細については、Microsoft アカウント認証でサポートされる構成オプション。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="sign-in-with-microsoft-account"></a>Microsoft アカウントでサインイン

実行 をクリック**ログイン**します。 Microsoft アカウントでサインインするためのオプションが表示されます。 Microsoft をクリックすると、認証のように、Microsoft にリダイレクトされます。 (サインインされていない) 場合、Microsoft アカウントでサインインしたら、アプリの情報にアクセスできるようにするよう求められます。

タップ**はい**とメールを設定する web サイトにリダイレクトされます。

Microsoft の資格情報を使用してログインしました。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* Microsoft アカウント プロバイダーでは、サインイン エラー ページにリダイレクトするの場合、エラーのタイトルと説明クエリ文字列パラメーターの直後に注意してください、 `#` (ハッシュタグ) uri。

  エラー メッセージを Microsoft 認証に問題を示すために見えますが、最も一般的な原因は、アプリケーション Uri と一致しない、**リダイレクト Uri**の指定、 **Web**プラットフォーム.
* ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。 このサンプルで使用するプロジェクト テンプレートによりこれが行われるようになります。
* 最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。 タップ**適用移行**データベースを作成し、エラーを引き続き更新します。

## <a name="next-steps"></a>次の手順

* この記事では、Microsoft を認証する方法を示しました。 記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。

* Azure web アプリに web サイトを発行するとシークレットを作成、新しいクライアント、Microsoft 開発者ポータルで。

* 設定、`Authentication:Microsoft:ClientId`と`Authentication:Microsoft:ClientSecret`として、Azure portal でアプリケーションの設定。 構成システムは、環境変数からキーの読み取りを設定します。