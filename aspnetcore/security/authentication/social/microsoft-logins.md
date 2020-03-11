---
title: ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ
author: rick-anderson
description: このサンプルでは、Microsoft アカウントユーザー認証を既存の ASP.NET Core アプリに統合する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652070"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core を使用した Microsoft アカウントの外部ログインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このサンプルでは、[前のページ](xref:security/authentication/social/index)で作成した ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが Microsoft アカウントでサインインできるようにする方法を示します。

## <a name="create-the-app-in-microsoft-developer-portal"></a>Microsoft 開発者ポータルでアプリを作成する

* プロジェクトに、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet パッケージを追加します。
* [Azure portal アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)ページに移動し、Microsoft アカウントを作成またはサインインします。

Microsoft アカウントがない場合は、 **[作成]** を選択します。 サインインすると、 **[アプリの登録]** ページにリダイレクトされます。

* **[新規登録]** を選択します
* **[名前]** を入力します。
* **サポートされているアカウントの種類**のオプションを選択します。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* **[リダイレクト URI]** に、`/signin-microsoft` 追加された開発 URL を入力します。 たとえば、「 `https://localhost:5001/signin-microsoft` 」のように入力します。 このサンプルの後半で構成されている Microsoft 認証スキームは、OAuth フローを実装するために `/signin-microsoft` ルートで要求を自動的に処理します。
* **[登録]** を選択します

### <a name="create-client-secret"></a>クライアント シークレットを作成する

* 左側のウィンドウで、 **[証明書とシークレット]** を選択します。
* **[クライアントシークレット]** で、 **[新しいクライアントシークレット]** を選択します。

  * クライアントシークレットの説明を追加します。
  * **[追加]** ボタンを選びます。

* **[クライアントシークレット]** で、クライアントシークレットの値をコピーします。

> [!NOTE]
> URI セグメント `/signin-microsoft` は、Microsoft 認証プロバイダーの既定のコールバックとして設定されます。 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Microsoft 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Microsoft クライアント ID とクライアントシークレットを保存する

次のコマンドを実行して、[シークレットマネージャー](xref:security/app-secrets)を使用して `ClientId` と `ClientSecret` を安全に格納します。

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

[シークレットマネージャー](xref:security/app-secrets)を使用して、Microsoft `ClientId` や `ClientSecret` などの機密性の高い設定をアプリケーション構成にリンクします。 このサンプルでは、トークンに `Authentication:Microsoft:ClientId` と `Authentication:Microsoft:ClientSecret`という名前を指定します。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft アカウント認証を構成する

Microsoft アカウントサービスを `Startup.ConfigureServices`に追加します。

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Microsoft アカウント認証でサポートされる構成オプションの詳細については、 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API リファレンスを参照してください。 これは、ユーザーに関するさまざまな情報を要求を使用できます。

## <a name="sign-in-with-microsoft-account"></a>Microsoft アカウントでサインインアカウント

アプリを実行し、 **[ログイン]** をクリックします。 Microsoft でサインインするためのオプションが表示されます。 [Microsoft] をクリックすると、認証のために Microsoft にリダイレクトされます。 Microsoft アカウントでサインインすると、アプリが情報にアクセスできるようにするように求めるメッセージが表示されます。

**[はい]** をタップすると、電子メールを設定できる web サイトにリダイレクトされます。

これで、Microsoft 資格情報を使用してログインしました。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* Microsoft アカウントプロバイダーからサインインエラーページにリダイレクトされた場合は、Uri 内の `#` (ハッシュタグ) のすぐ後に、エラーのタイトルと説明のクエリ文字列パラメーターを確認してください。

  エラーメッセージは Microsoft 認証に問題があることを示していますが、最も一般的な原因は、アプリケーション Uri が**Web**プラットフォームに指定されている**リダイレクト uri**と一致していないことです。
* `ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。 このサンプルで使用するプロジェクトテンプレートにより、この処理が確実に行われます。
* 初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。 **[移行の適用]** をタップしてデータベースを作成し、更新してエラーを続行します。

## <a name="next-steps"></a>次のステップ:

* この記事では、Microsoft で認証する方法について説明しました。 同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。

* Web サイトを Azure web アプリに発行したら、Microsoft 開発者ポータルで新しいクライアントシークレットを作成します。

* Azure portal で、`Authentication:Microsoft:ClientId` と `Authentication:Microsoft:ClientSecret` をアプリケーション設定として設定します。 構成システムは、環境変数からキーの読み取りを設定します。
