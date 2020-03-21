---
title: ASP.NET Core での Facebook 外部ログインのセットアップ
author: rick-anderson
description: Facebook アカウントのユーザー認証を既存の ASP.NET Core アプリに統合する方法を示すコード例を紹介したチュートリアルです。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: bb26a27f026e744c7d4925aa2281bf0625fff8a2
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989781"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core での Facebook 外部ログインのセットアップ

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このチュートリアルでは、[前のページ](xref:security/authentication/social/index)で作成したサンプル ASP.NET Core 3.0 プロジェクトを使用して、ユーザーが Facebook アカウントでサインインできるようにする方法を示します。 まず、[正式な手順](https://developers.facebook.com)に従って FACEBOOK アプリ ID を作成します。

## <a name="create-the-app-in-facebook"></a>Facebook で、アプリを作成します。

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet パッケージをプロジェクトに追加します。

* [Facebook 開発者アプリ](https://developers.facebook.com/apps/)のページに移動し、サインインします。 まだ Facebook アカウントを持っていない場合は、ログインページの **[facebook へのサインアップ]** リンクを使用して作成します。  Facebook アカウントを作成したら、手順に従って Facebook 開発者として登録します。

* **[マイアプリ**] メニューで、 **[アプリの作成]** を選択して新しいアプリ ID を作成します。

   ![Microsoft Edge で開発者ポータルの Facebook を開く](index/_static/FBMyApps.png)

* フォームに入力し、 **[アプリ ID の作成]** ボタンをタップします。

  ![アプリ ID の新しいフォームを作成します。](index/_static/FBNewAppId.png)

* 新しいアプリカードで、 **[製品の追加]** を選択します。  **Facebook ログイン**カードで、 **[セットアップ] をクリックし**ます。 

  ![製品のセットアップ ページ](index/_static/FBProductSetup.png)

* **クイックスタート**ウィザードが起動し、最初のページとして **[プラットフォーム] を選択**します。 ここでウィザードをバイパスするには、左下のメニューで [ **FaceBook のログイン** **設定**] リンクをクリックします。

  ![スキップのクイック スタート](index/_static/FBSkipQuickStart.png)

* **[クライアントの OAuth 設定]** ページが表示されます。

  ![クライアントの OAuth の設定 ページ](index/_static/FBOAuthSetup.png)

* */Signin-facebook*を使用して、 **[有効な OAuth リダイレクト uri]** フィールドに追加された開発 URI を入力します (例: `https://localhost:44320/signin-facebook`)。 このチュートリアルの後半で構成する Facebook 認証は、OAuth フローを実装するために */signin-facebook* route で要求を自動的に処理します。

> [!NOTE]
> URI */signin-facebook*は、facebook 認証プロバイダーの既定のコールバックとして設定されます。 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)クラスの [継承された[remoteauthenticationoptions]](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)プロパティを使用して Facebook 認証ミドルウェアを構成するときに、既定のコールバック URI を変更できます。

* **[Save Changes]** をクリックします。

* 左側のナビゲーションで、 **[設定]** 、 **[基本]** リンク の順にクリックし > ます。

  このページで、`App ID` と `App Secret`をメモしておきます。 次のセクションでは、両方に、ASP.NET Core アプリケーションを追加します。

* サイトをデプロイするときに、 **Facebook ログイン**のセットアップページを再表示し、新しいパブリック URI を登録する必要があります。

## <a name="store-the-facebook-app-id-and-secret"></a>Facebook アプリ ID とシークレットを保存する

Facebook アプリ ID やシークレット値などの機微な設定を[Secret Manager](xref:security/app-secrets)に保存します。 このサンプルでは、次の手順を使用します。

1. 「[シークレットストレージを有効にする](xref:security/app-secrets#enable-secret-storage)」の手順に従って、シークレットストレージのプロジェクトを初期化します。
1. 秘密キー `Authentication:Facebook:AppId` と `Authentication:Facebook:AppSecret`を使用して、ローカルシークレットストアに機密設定を格納します。

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Facebook 認証を構成します。

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

Facebook 認証でサポートされる構成オプションの詳細については、 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API リファレンスを参照してください。 構成オプションを使用できます。

* ユーザーに関するさまざまな情報を要求します。
* ログイン エクスペリエンスをカスタマイズするクエリ文字列引数を追加します。

## <a name="sign-in-with-facebook"></a>Facebook のサインイン

アプリケーションを実行し、 **[ログイン]** をクリックします。 Facebook でサインインするオプションが表示されます。

![Web アプリケーション: ユーザーが認証されていません。](index/_static/DoneFacebook.png)

**Facebook**をクリックすると、認証のために facebook にリダイレクトされます。

![Facebook 認証ページ](index/_static/FBLogin.png)

Facebook の認証は、既定では、パブリック プロファイルと電子メール アドレスを要求します。

![Facebook 認証ページの同意画面](index/_static/FBLoginDone.png)

Facebook の資格情報を入力すると、電子メールを設定するサイトにリダイレクトされます。

Facebook の資格情報を使用してログインしました。

![Web アプリケーション: 認証されたユーザー](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>トラブルシューティング

* **ASP.NET Core 2.x のみ:** `ConfigureServices`で `services.AddIdentity` を呼び出すことによって Id が構成されていない場合、認証を試みると ArgumentException が返され*ます。 ' SignInScheme ' オプションを指定する必要があり*ます。 このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。
* 初期移行を適用してサイトデータベースが作成されていない場合は、*要求エラーの処理中にデータベース操作が失敗*します。 **[移行の適用]** をタップしてデータベースを作成し、更新してエラーを続行します。

## <a name="next-steps"></a>次のステップ:

* この記事では、Facebook を認証する方法を示しました。 同様のアプローチに従って、[前のページ](xref:security/authentication/social/index)に一覧表示されている他のプロバイダーとの認証を行うことができます。

* Web サイトを Azure web アプリに発行したら、Facebook 開発者ポータルで `AppSecret` をリセットする必要があります。

* Azure portal で、`Authentication:Facebook:AppId` と `Authentication:Facebook:AppSecret` をアプリケーション設定として設定します。 構成システムは、環境変数からキーの読み取りを設定します。
