---
title: ASP.NET Core の Azure Active Directory B2C を使用したクラウド認証
author: camsoper
description: ASP.NET Core で Azure Active Directory B2C 認証をセットアップする方法について説明します。
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653618"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>ASP.NET Core の Azure Active Directory B2C を使用したクラウド認証

作成者: [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) は、web アプリとモバイルアプリのクラウド id 管理ソリューションです。 サービスは、クラウドとオンプレミスでホストされているアプリの認証を提供します。 認証の種類は、個々 のアカウントに、ソーシャル ネットワーク アカウントを含めるし、エンタープライズ アカウントをフェデレーションします。 また、Azure AD B2C では、最小構成で multi-factor authentication を提供できます。

> [!TIP]
> Azure Active Directory (Azure AD) と Azure AD B2C は個別の製品を提供します。 Azure AD テナントは、組織を表し、Azure AD B2C テナントは証明書利用者アプリケーションで使用される id のコレクションを表します。 詳細については、「 [Azure AD B2C: よく寄せられる質問 (faq)](/azure/active-directory-b2c/active-directory-b2c-faqs)」を参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory B2C テナントの作成
> * Azure AD B2C にアプリを登録する
> * Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成された ASP.NET Core web アプリを作成する
> * Azure AD B2C テナントの動作を制御するポリシーを構成する

## <a name="prerequisites"></a>前提条件

次に、このチュートリアルでは必須です。

* [Microsoft Azure サブスクリプション](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントを作成します。

[ドキュメントで説明されているように](/azure/active-directory-b2c/active-directory-b2c-get-started)Azure Active Directory B2C テナントを作成します。 入力を求められたら、テナントを Azure サブスクリプションに関連付ける省略可能なこのチュートリアルでは。

## <a name="register-the-app-in-azure-ad-b2c"></a>Azure AD B2C にアプリを登録する

新しく作成された Azure AD B2C テナントで、「 **web アプリを登録する**」セクションにある[ドキュメントの手順](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)に従って、アプリを登録します。 **[Web アプリクライアントシークレットの作成]** セクションで停止します。 このチュートリアルでは、クライアント シークレットが必要はありません。 

次の値を使用します。

| 設定                       | 値                     | 説明                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                      | *&lt;アプリケーション名&gt;*        | アプリをコンシューマーに説明するアプリの**名前**を入力します。                                                                                                                                 |
| **Web アプリ/Web API を含める** | はい                       |                                                                                                                                                                                                    |
| **暗黙的フローを許可する**       | はい                       |                                                                                                                                                                                                    |
| **応答 URL**                 | `https://localhost:44300/signin-oidc` | 応答 URL は、アプリが要求したトークンを Azure AD B2C が返すエンドポイントです。 Visual Studio には、使用する応答 URL が用意されています。 ここでは、`https://localhost:44300/signin-oidc` 入力してフォームを完成させます。 |
| **アプリケーション ID/URI**                | 空白               | このチュートリアルでは必要ありません。                                                                                                                                                                    |
| **ネイティブ クライアントを含める**     | いいえ                        |                                                                                                                                                                                                    |

> [!WARNING]
> Localhost 以外の応答 URL を設定する場合は、[[応答 url] の一覧で許可されている内容に関する制約](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)に注意してください。 

アプリが登録されると、テナント内のアプリの一覧が表示されます。 登録したばかりのアプリを選択します。 **[アプリケーション ID]** フィールドの右側にある**コピー**アイコンを選択して、クリップボードにコピーします。

現時点では、Azure AD B2C テナントで構成できるものはありませんが、ブラウザーウィンドウは開いたままにしておきます。 ASP.NET Core アプリが作成された後は、さらに多くの構成があります。

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Visual Studio での ASP.NET Core アプリの作成

認証に Azure AD B2C テナントを使用する Visual Studio Web アプリケーション テンプレートを構成できます。

Visual Studio で次の操作を行います。

1. 新しい ASP.NET Core Web アプリケーションを作成します。 
2. テンプレートの一覧から **[Web アプリケーション]** を選択します。
3. **[認証の変更]** ボタンを選択します。
    
    ![変更の認証 ボタン](./azure-ad-b2c/_static/changeauth.png)

4. **[認証の変更]** ダイアログで、 **[個別のユーザーアカウント]** を選択し、ドロップダウンで **[クラウド内の既存のユーザーストアに接続する]** を選択します。 
    
    ![認証の変更ダイアログ](./azure-ad-b2c/_static/changeauthdialog.png)

5. 次の値をフォームに入力します。
    
    | 設定                       | 値                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **ドメイン名**               | *B2C テナントのドメイン名を &lt;&gt;*          |
    | **アプリケーション ID**            | *&lt;クリップボードからアプリケーション ID を貼り付け&gt;* |
    | **コールバックパス**             | *既定値を使用 &lt;&gt;*                       |
    | **サインアップまたはサインインポリシー** | `B2C_1_SiUpIn`                                        |
    | **パスワードポリシーのリセット**     | `B2C_1_SSPR`                                          |
    | **プロファイルポリシーの編集**       | *空白のまま &lt;&gt;*                                 |
    
    **[応答 uri]** の横にある **[コピー]** リンクを選択して、応答 uri をクリップボードにコピーします。 [ **OK]** を選択して **[認証の変更]** ダイアログを閉じます。 [ **OK]** を選択して web アプリを作成します。

## <a name="finish-the-b2c-app-registration"></a>B2C アプリの登録を完了する

B2C アプリのプロパティを開いたままブラウザーウィンドウに戻ります。 前に指定した一時的な**応答 URL**を、Visual Studio からコピーした値に変更します。 ウィンドウの上部にある **[保存]** を選択します。

> [!TIP]
> 応答 URL をコピーしていない場合は、web プロジェクトのプロパティの [デバッグ] タブにある HTTPS アドレスを使用して、 *appsettings*からの [の転送**パス**] の値を追加します。

## <a name="configure-policies"></a>ポリシーの構成

Azure AD B2C のドキュメントに記載されている手順に従って、[サインアップまたはサインインポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)し、[パスワードリセットポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)します。 **Id プロバイダー**、**サインアップ属性**、および**アプリケーション要求**に関するドキュメントに記載されている値の例を使用します。 ドキュメントで説明されているように、 **[今すぐ実行]** ボタンを使用してポリシーをテストすることは、オプションです。

> [!WARNING]
> ポリシー名は、Visual Studio の **[認証の変更]** ダイアログで使用されていたので、ドキュメントに記載されているとおりに記述してください。 ポリシー名は、 *appsettings*で検証できます。

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>基になる OpenIdConnectOptions/JwtBearer/Cookie オプションを構成する

基になるオプションを直接構成するには、`Startup.ConfigureServices`で適切な scheme 定数を使用します。

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>アプリを実行する

Visual Studio で、 **F5**キーを押してアプリをビルドして実行します。 Web アプリが起動したら、 **[同意]** する を選択して、(メッセージが表示された場合は) cookie の使用を受け入れ、 **[サインイン]** を選択します。

![アプリにサインインする](./azure-ad-b2c/_static/signin.png)

ブラウザーが Azure AD B2C テナントにリダイレクトされます。 (ポリシーのテストを作成した場合は) 既存のアカウントでサインインするか、 **[今すぐサインアップ]** を選択して新しいアカウントを作成します。 忘れたパスワードをリセットするには、[**パスワードを忘れ**た場合] リンクを使用します。

![Azure AD B2C のログイン](./azure-ad-b2c/_static/b2csts.png)

サインインに成功すると、ブラウザーが web アプリにリダイレクトされます。

![成功](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>次のステップ:

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure Active Directory B2C テナントの作成
> * Azure AD B2C にアプリを登録する
> * Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成された ASP.NET Core Web アプリケーションを作成する
> * Azure AD B2C テナントの動作を制御するポリシーを構成する

認証に Azure AD B2C を使用するように ASP.NET Core アプリが構成されたので、[承認属性](xref:security/authorization/simple)を使用してアプリをセキュリティで保護することができます。 次のことを学習してアプリの開発を続けます。

* [Azure AD B2C ユーザーインターフェイスをカスタマイズ](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)します。
* [パスワードの複雑さの要件を構成](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)します。
* [多要素認証を有効に](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)します。
* [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)など、その他の id プロバイダーを構成します。
* [Azure AD Graph API を使用](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet)して、Azure AD B2C テナントからグループメンバーシップなどの追加のユーザー情報を取得します。
* [Azure AD B2C を使用して ASP.NET Core WEB API をセキュリティで保護](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/)します。
* [Azure AD B2C を使用して .net web アプリから .net WEB API を呼び出し](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)ます。