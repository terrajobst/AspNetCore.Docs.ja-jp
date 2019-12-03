---
title: ASP.NET Core での Facebook 外部ログインセットアップ
author: rick-anderson
description: Facebook アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を示すコード例を紹介したチュートリアルです。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717119"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core での Facebook 外部ログインセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが Facebook アカウントでサインインできるようにする方法を示します。 まず、[正式な手順](https://developers.facebook.com)に従って FACEBOOK アプリ ID を作成します。

## <a name="create-the-app-in-facebook"></a>Facebook でアプリを作成する

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet パッケージをプロジェクトに追加します。

* [Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。 まだ Facebook アカウントを持っていない場合は、ログインページの **[facebook へのサインアップ]** リンクを使用して作成します。  Facebook アカウントを作成したら、手順に従って Facebook 開発者として登録します。

* **[マイアプリ**] メニューで、 **[アプリの作成]** を選択して新しいアプリ ID を作成します。

   ![開発者向け Facebook ポータルを Microsoft Edge で開く](index/_static/FBMyApps.png)

* フォームに入力し、 **[アプリ ID の作成]** ボタンをタップします。

  ![新しいアプリ ID フォームを作成する](index/_static/FBNewAppId.png)

* 新しいアプリカードで、 **[製品の追加]** を選択します。  **Facebook ログイン**カードで、 **[セットアップ] をクリックし**ます。 

  ![製品のセットアップページ](index/_static/FBProductSetup.png)

* **クイックスタート**ウィザードが起動し、最初のページとして **[プラットフォーム] を選択**します。 ここでウィザードをバイパスするには、左下のメニューで [ **FaceBook のログイン** **設定**] リンクをクリックします。

  ![スキップクイックスタート](index/_static/FBSkipQuickStart.png)

* **[クライアントの OAuth 設定]** ページが表示されます。

  ![[クライアント OAuth 設定] ページ](index/_static/FBOAuthSetup.png)

* */Signin-facebook*を使用して、 **[有効な OAuth リダイレクト uri]** フィールドに追加された開発 URI を入力します (例: `https://localhost:44320/signin-facebook`)。 このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook* route で要求を自動的に処理します。

> [!NOTE]
> URI */signin-facebook*は、facebook 認証プロバイダーの既定のコールバックとして設定されます。 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Facebook 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

* **[変更の保存]** をクリックします。

* 左側のナビゲーションで、 **[設定]** 、 **[基本]** リンク の順にクリックし > ます。

  このページで、`App ID` と `App Secret`をメモしておきます。 次のセクションでは、両方を ASP.NET Core アプリケーションに追加します。

* サイトをデプロイするときに、 **Facebook ログイン**のセットアップページを再表示し、新しいパブリック URI を登録する必要があります。

## <a name="store-facebook-app-id-and-app-secret"></a>Facebook アプリ ID とアプリシークレットを格納する

[Secret Manager](xref:security/app-secrets)を使用して、Facebook `App ID` や `App Secret` などの機密性の高い設定をアプリケーション構成にリンクします。 このチュートリアルでは、トークンに `Authentication:Facebook:AppId` と `Authentication:Facebook:AppSecret`という名前を指定します。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

次のコマンドを実行して、シークレットマネージャーを使用して `App ID` と `App Secret` を安全に格納します。

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Facebook 認証の構成

*Startup.cs*ファイルの `ConfigureServices` メソッドに Facebook サービスを追加します。

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook 認証でサポートされる構成オプションの詳細については、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。 構成オプションは、次の場合に使用できます。

* ユーザーに関するさまざまな情報を要求します。
* クエリ文字列引数を追加して、ログインエクスペリエンスをカスタマイズします。

## <a name="sign-in-with-facebook"></a>Facebook でサインインする

アプリケーションを実行し、 **[ログイン]** をクリックします。 Facebook でサインインするためのオプションが表示されます。

![Web アプリケーション: ユーザーが認証されていません](index/_static/DoneFacebook.png)

**Facebook**をクリックすると、認証のために facebook にリダイレクトされます。

![Facebook の認証ページ](index/_static/FBLogin.png)

Facebook 認証は、既定でパブリックプロファイルと電子メールアドレスを要求します。

![Facebook 認証ページの同意画面](index/_static/FBLoginDone.png)

Facebook の資格情報を入力すると、電子メールを設定できるサイトにリダイレクトされます。

Facebook の資格情報を使用してログインしました。

![Web アプリケーション: ユーザー認証済み](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NET Core 2.x のみ:** `ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。 このチュートリアルで使用するプロジェクトテンプレートによって、この処理が確実に行われます。
* 初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。 **[移行の適用]** をタップしてデータベースを作成し、更新してエラーを続行します。

## <a name="next-steps"></a>次のステップ:

* この記事では、Facebook で認証する方法について説明しました。 同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。

* Web サイトを Azure web アプリに発行したら、Facebook 開発者ポータルで `AppSecret` をリセットする必要があります。

* Azure portal で、`Authentication:Facebook:AppId` と `Authentication:Facebook:AppSecret` をアプリケーション設定として設定します。 構成システムは、環境変数からキーを読み取るように設定されています。
