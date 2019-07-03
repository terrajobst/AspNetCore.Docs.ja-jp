---
title: ASP.NET Core での Azure Active Directory B2C でのクラウド認証
author: camsoper
description: ASP.NET Core での Azure Active Directory B2C の認証を設定する方法を説明します。
ms.author: casoper
ms.custom: mvc
ms.date: 02/27/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 3cb878aff7bf0c6c8efe7f3f0c0f06c74acef477
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538727"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>ASP.NET Core での Azure Active Directory B2C でのクラウド認証

作成者: [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) は、web およびモバイル アプリのクラウド id 管理ソリューションです。 サービスは、クラウドとオンプレミスでホストされているアプリの認証を提供します。 認証の種類は、個々 のアカウントに、ソーシャル ネットワーク アカウントを含めるし、エンタープライズ アカウントをフェデレーションします。 また、Azure AD B2C では、最小構成での多要素認証を提供できます。

> [!TIP]
> Azure Active Directory (Azure AD) と Azure AD B2C は個別の製品を提供します。 Azure AD テナントは、組織を表し、Azure AD B2C テナントは証明書利用者アプリケーションで使用される id のコレクションを表します。 詳細についてを参照してください[Azure AD B2C:。よく寄せられる質問 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)します。

このチュートリアルで学習する方法。

> [!div class="checklist"]
> * Azure Active Directory B2C テナントを作成します。
> * Azure AD B2C にアプリを登録します。
> * Visual Studio を使用して、ASP.NET Core web アプリの認証に Azure AD B2C テナントを使用するように構成を作成するには
> * Azure AD B2C テナントの動作を制御するポリシーを構成します。

## <a name="prerequisites"></a>必須コンポーネント

次に、このチュートリアルでは必須です。

* [Microsoft Azure サブスクリプション](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントを作成します。

Azure Active Directory B2C テナントの作成[ドキュメント」の説明に従って](/azure/active-directory-b2c/active-directory-b2c-get-started)します。 入力を求められたら、テナントを Azure サブスクリプションに関連付ける省略可能なこのチュートリアルでは。

## <a name="register-the-app-in-azure-ad-b2c"></a>Azure AD B2C でアプリを登録します。

新しく作成された Azure AD B2C テナントで登録を使用して、アプリ[、ドキュメントの手順](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app)下、 **web アプリを登録**セクション。 停止時刻、 **web アプリ クライアント シークレットの作成**セクション。 このチュートリアルでは、クライアント シークレットが必要はありません。 

次の値を使用します。

| 設定                       | [値]                     | メモ                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                      | *&lt;アプリ名&gt;*        | 入力、**名前**コンシューマーに、アプリについて説明しているアプリ。                                                                                                                                 |
| **Web アプリを含める/web API** | [はい]                       |                                                                                                                                                                                                    |
| **暗黙的なフローを許可します。**       | [はい]                       |                                                                                                                                                                                                    |
| **応答 URL**                 | `https://localhost:44300/signin-oidc` | 応答 Url とは、Azure AD B2C が、アプリが要求したトークンを返すエンドポイントです。 Visual Studio では、使用する応答 URL を提供します。 ここでは、次のように入力します。`https://localhost:44300/signin-oidc`フォームを完了します。 |
| **アプリ ID URI**                | 空白のままに               | このチュートリアルでは必要ありません。                                                                                                                                                                    |
| **ネイティブ クライアントを含める**     | いいえ                        |                                                                                                                                                                                                    |

> [!WARNING]
> かどうかは、を認識する localhost 以外の応答 URL では、設定、[応答 URL の一覧で許可されている内容に対する制約](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-web-app-or-api-reply-url)します。 

アプリを登録すると、テナント内のアプリの一覧が表示されます。 登録されたアプリを選択します。 選択、**コピー**アイコンの右側に、**アプリケーション ID**フィールドをクリップボードにコピーします。

Nothing の詳細はこの時点で Azure AD B2C テナントで構成できますが、ブラウザー ウィンドウを開いたままにしておきます。 ASP.NET Core アプリを作成した後の構成の詳細は。

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Visual Studio で ASP.NET Core アプリを作成します。

認証に Azure AD B2C テナントを使用する Visual Studio Web アプリケーション テンプレートを構成できます。

Visual Studio:

1. 新しい ASP.NET Core Web アプリケーションを作成します。 
2. 選択**Web アプリケーション**テンプレートの一覧から。
3. 選択、**認証の変更**ボタンをクリックします。
    
    ![変更の認証 ボタン](./azure-ad-b2c/_static/changeauth.png)

4. **認証の変更**ダイアログ ボックスで、**個々 のユーザー アカウント**、し、 **、クラウド内の既存のユーザー ストアへの接続**ドロップダウン リストにします。 
    
    ![認証の変更ダイアログ](./azure-ad-b2c/_static/changeauthdialog.png)

5. 次の値をフォームに入力します。
    
    | 設定                       | [値]                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **ドメイン名**               | *&lt;B2C テナントのドメイン名&gt;*          |
    | **アプリケーション ID**            | *&lt;クリップボードからのアプリケーション ID を貼り付けます&gt;* |
    | **コールバック パス**             | *&lt;既定値を使用します。&gt;*                       |
    | **サインアップまたはサインイン ポリシー** | `B2C_1_SiUpIn`                                        |
    | **パスワードのリセット ポリシー**     | `B2C_1_SSPR`                                          |
    | **プロファイルのポリシーを編集します。**       | *&lt;空白のままに&gt;*                                 |
    
    選択、**コピー**リンクの横に**応答 URI**応答 URI をクリップボードにコピーします。 選択**OK**を閉じる、**認証の変更**ダイアログ。 選択**OK** web アプリを作成します。

## <a name="finish-the-b2c-app-registration"></a>B2C アプリの登録を完了します。

まだ開いて B2C アプリのプロパティを備えたブラウザー ウィンドウに戻ります。 一時的な変更**応答 URL** Visual Studio から以前の値にコピーを指定します。 選択**保存**ウィンドウの上部にあります。

> [!TIP]
> [デバッグ] タブからの HTTPS アドレスを使用して、web プロジェクトのプロパティと追加の応答 URL をコピーしなかった場合、 **CallbackPath**値から*appsettings.json*します。

## <a name="configure-policies"></a>ポリシーを構成します。

手順を使用して、Azure AD B2C のドキュメントを[サインアップまたはサインイン ポリシーを作成する](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy)、し[パスワードのリセット ポリシーを作成](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy)。 ドキュメントの例の値を使用して、 **Id プロバイダー**、**サインアップ属性**、および**アプリケーション要求**します。 使用して、**を今すぐ実行**ドキュメント」の説明に従って、ポリシーをテストするボタンは省略可能です。

> [!WARNING]
> ポリシー名は、のドキュメントで説明したとおりでこれらのポリシーが使用されていたことを確認、**認証の変更**Visual Studio でダイアログ。 ポリシー名を検証できます*appsettings.json*します。

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>基になる OpenIdConnectOptions/JwtBearer/Cookie オプションを構成します。

基になるオプションを直接構成するには、内の適切なスキームの定数を使用して、 `Startup.ConfigureServices`:

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

Visual Studio で、キーを押して**F5**アプリをビルドして実行します。 Web アプリが起動後、選択**Accept** (要求) の場合、cookie の使用に同意し、選択**サインイン**します。

![アプリにサインインします。](./azure-ad-b2c/_static/signin.png)

ブラウザーは、Azure AD B2C テナントにリダイレクトします。 (この場合、ポリシーをテストする 1 つが作成された) 既存のアカウントでサインインまたは選択**今すぐサインアップ**新しいアカウントを作成します。 **パスワードを忘れた場合でしょうか。** 忘れたパスワードをリセットするリンクを使用します。

![Azure AD B2C のログイン](./azure-ad-b2c/_static/b2csts.png)

正常にサインインした後、ブラウザーに web アプリにリダイレクトします。

![成功](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Azure Active Directory B2C テナントを作成します。
> * Azure AD B2C にアプリを登録します。
> * Visual Studio を使用して、認証に Azure AD B2C テナントを使用するように構成の ASP.NET Core Web アプリケーションを作成するには
> * Azure AD B2C テナントの動作を制御するポリシーを構成します。

認証に Azure AD B2C を使用する ASP.NET Core アプリが構成されたので、 [Authorize 属性](xref:security/authorization/simple)アプリをセキュリティで保護するために使用できます。 学習することにより、アプリの開発を続行するには。

* [Azure AD B2C ユーザー インターフェイスをカスタマイズ](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)します。
* [パスワードの複雑さの要件を構成する](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)します。
* [多要素認証を有効にする](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)します。
* など、追加の id プロバイダーを構成[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)、およびその他。
* [Azure AD Graph API を使用して、](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) Azure AD B2C テナントからグループのメンバーシップなどの追加のユーザー情報を取得します。
* [セキュリティで保護された ASP.NET Core web API が Azure AD B2C を使用して](xref:security/authentication/azure-ad-b2c-webapi)します。
* [Azure AD B2C を使用して .NET web アプリから .NET web API を呼び出す](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)します。
