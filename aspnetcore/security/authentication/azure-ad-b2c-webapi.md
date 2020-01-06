---
title: Web Api ASP.NET Core での Azure Active Directory B2C での認証
author: camsoper
description: ASP.NET Core Web API を使用した Azure Active Directory B2C の認証を設定する方法を説明します。 認証された web API と Postman をテストします。
ms.author: casoper
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: 9b18b19838a2d25944a2498b6eec1677e56b12cc
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358262"
---
# <a name="authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>Web Api ASP.NET Core での Azure Active Directory B2C での認証

作成者: [Cam Soper](https://twitter.com/camsoper)

<!-- Next update remove screenshots. They become obsolete too soon and are more work to update -->

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) は、web およびモバイル アプリのクラウド id 管理ソリューションです。 サービスは、クラウドとオンプレミスでホストされているアプリの認証を提供します。 認証の種類は、個々 のアカウントに、ソーシャル ネットワーク アカウントを含めるし、エンタープライズ アカウントをフェデレーションします。 Azure AD B2C では、最小構成での多要素認証も提供します。

Azure Active Directory (Azure AD) と Azure AD B2C は個別の製品を提供します。 Azure AD テナントは、組織を表し、Azure AD B2C テナントは証明書利用者アプリケーションで使用される id のコレクションを表します。 詳細についてを参照してください。 [Azure AD B2C: よく寄せられる質問 (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs)します。

Web Api にユーザー インターフェイスがあるないため、中、Azure AD B2C のような場合は、セキュリティ トークン サービスにユーザーをリダイレクトすること。 代わりに、API では、呼び出し元のアプリは、Azure AD B2C でユーザーが既に認証されてからベアラー トークンを渡されます。 API は、直接ユーザーの介入なしトークンを検証します。

このチュートリアルで学習する方法。

> [!div class="checklist"]
> * Azure Active Directory B2C テナントを作成します。
> * Azure AD B2C では、Web API を登録します。
> * Visual Studio を使用すると、認証に Azure AD B2C テナントを使用するのにように構成、Web API を作成できます。
> * Azure AD B2C テナントの動作を制御するポリシーを構成します。
> * 、ログイン ダイアログを表示する web アプリをシミュレートするために Postman を使用しては、トークンを取得し、使用して、web API に対する要求をします。

## <a name="prerequisites"></a>Prerequisites

次に、このチュートリアルでは必須です。

* [Microsoft Azure サブスクリプション](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントを作成します。

Azure AD B2C テナントを作成[ドキュメント」の説明に従って](/azure/active-directory-b2c/active-directory-b2c-get-started)します。 入力を求められたら、テナントを Azure サブスクリプションに関連付ける省略可能なこのチュートリアルでは。

## <a name="configure-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを構成します。

手順を使用して、Azure AD B2C のドキュメントを[サインアップまたはサインイン ポリシーを作成する](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)します。 ポリシーの名前**SiUpIn**します。  ドキュメントの例の値を使用して、 **Id プロバイダー**、**サインアップ属性**、および**アプリケーション要求**します。 使用して、**を今すぐ実行**ドキュメント」の説明に従って、ポリシーをテストするボタンは省略可能です。

## <a name="register-the-api-in-azure-ad-b2c"></a>Azure AD B2C で API を登録します。

新しく作成された Azure AD B2C テナントで登録 API を使用して、 [、ドキュメントの手順](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)下、 **web API の登録**セクション。

次の値を使用します。

| 設定                       | Value               | メモ                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **名前**                      | *API {name}*        | 入力、**名前**コンシューマーに、アプリについて説明しているアプリ。                     |
| **Web アプリを含める/web API** | ○                 |                                                                                        |
| **暗黙的なフローを許可します。**       | ○                 |                                                                                        |
| **応答 URL**                 | `https://localhost` | 応答 Url とは、Azure AD B2C が、アプリが要求したトークンを返すエンドポイントです。 |
| **アプリ ID URI**                | *api*               | URI は、物理アドレスに解決する必要があります。 それだけ、一意である必要があります。     |
| **ネイティブ クライアントを含める**     | いいえ                  |                                                                                        |

API は、登録後、テナント内のアプリと Api の一覧が表示されます。 既に登録済みの API を選択します。 選択、**コピー**アイコンの右側に、**アプリケーション ID**フィールドをクリップボードにコピーします。 選択**公開済みスコープ**の既定の確認と*user_impersonation*スコープが存在します。

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Visual Studio での ASP.NET Core アプリの作成

認証に Azure AD B2C テナントを使用する Visual Studio Web アプリケーション テンプレートを構成できます。

Visual Studio:

1. 新しい ASP.NET Core Web アプリケーションを作成します。 
2. 選択**Web API**テンプレートの一覧から。
3. 選択、**認証の変更**ボタンをクリックします。

    ![変更の認証 ボタン](./azure-ad-b2c-webapi/change-auth-button.png)

4. **認証の変更**ダイアログ ボックスで、**個々 のユーザー アカウント** >  **、クラウド内の既存のユーザー ストアへの接続**します。

    ![認証の変更ダイアログ](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. 次の値をフォームに入力します。

    | 設定                       | Value                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **ドメイン名**               | *{B2C テナントのドメイン名}*                |
    | **アプリケーション ID**            | *{アプリケーション ID をクリップボードから貼り付ける}*       |
    | **サインアップまたはサインイン ポリシー** | B2C_1_SiUpIn                                          |

    選択**OK**を閉じる、**認証の変更**ダイアログ。 選択**OK** web アプリを作成します。

Visual Studio では、web API をによりという名前のコント ローラーと*ValuesController.cs*を返す GET 要求の値をハードコーディングします。 クラスは、[承認](xref:security/authorization/simple)属性でマークされるため、すべての要求に認証が必要です。

## <a name="run-the-web-api"></a>Web API を実行します。

Visual Studio で、API を実行します。 Visual Studio では、API のルート URL で参照されているブラウザーを起動します。 アドレス バーに URL をメモし、バック グラウンドで実行されている API のままにします。

> [!NOTE]
> コント ローラーのルート URL の定義がないために、404 (ページが見つかりません) エラーがブラウザーに表示します。 これは通常の動作です。

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Postman を使用してトークンを取得し、API をテストします。

[Postman](https://getpostman.com/postman) web Api をテストするためのツールです。 このチュートリアルでは、Postman は、ユーザーの代理で web API にアクセスする web アプリをシミュレートします。

### <a name="register-postman-as-a-web-app"></a>Postman を web アプリとして登録します。

Postman は、Azure AD B2C テナントからトークンを取得する web アプリをシミュレートするため必要があります登録する必要が、テナントで web アプリとして。 Postman を使用して登録[、ドキュメントの手順](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)下、 **web アプリを登録**セクション。 停止時刻、 **web アプリ クライアント シークレットの作成**セクション。 このチュートリアルでは、クライアント シークレットが必要はありません。 

次の値を使用します。

| 設定                       | Value                            | メモ                           |
|-------------------------------|----------------------------------|---------------------------------|
| **名前**                      | Postman                          |                                 |
| **Web アプリを含める/web API** | ○                              |                                 |
| **暗黙的なフローを許可します。**       | ○                              |                                 |
| **応答 URL**                 | `https://getpostman.com/postman` |                                 |
| **アプリ ID URI**                | *{空白のままにしました*                  | このチュートリアルでは必要ありません。 |
| **ネイティブ クライアントを含める**     | いいえ                               |                                 |

新しく登録した web アプリには、ユーザーの代理で web API にアクセスするためのアクセス許可が必要があります。  

1. 選択**Postman**クリックしてアプリの一覧で**API アクセス**左側のメニューから。
1. 選択 **+ 追加**します。
1. **API の選択**ドロップダウンで、web API の名前を選択します。
1. **スコープの選択**ドロップダウンで、すべてのスコープが選択されていることを確認します。
1. 選択**Ok**します。

ベアラー トークンを取得するためには、Postman アプリのアプリケーション ID に注意してください。

### <a name="create-a-postman-request"></a>Postman の要求を作成します。

Postman を起動します。 既定では、Postman が表示されます、**新規作成**ダイアログを起動するとします。 ダイアログ ボックスが表示されていない場合は、選択、 **+ 新規**画面の左ボタンをクリックします。

**新規作成**ダイアログ。

1. 選択**要求**します。

    ![要求 ボタン](./azure-ad-b2c-webapi/postman-create-new.png)

2. 入力*値の取得*で、**要求名**ボックス。
3. 選択 **+ コレクションの作成**要求を格納するための新しいコレクションを作成します。 コレクションの名前を付けます*ASP.NET Core チュートリアル*し、チェック マークを選択します。

    ![新しいコレクションを作成します。](./azure-ad-b2c-webapi/postman-create-collection.png)

4. 選択、 **ASP.NET Core のチュートリアルへの保存**ボタンをクリックします。

### <a name="test-the-web-api-without-authentication"></a>Web API 認証なしのテストします。

Web API に認証が必要であることを確認するには、まず認証を使用せず、要求を確認します。

1. **要求 URL を入力します。** の URL を入力ボックスに、`ValuesController`します。 URL がブラウザーで表示されるものと同じ**api/値**追加されます。 たとえば、 `https://localhost:44375/api/values`のようにします。
2. 選択、**送信**ボタンをクリックします。
3. 応答のステータスは*401 Unauthorized*します。

    ![401 応答](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> "応答を取得できませんでした" というエラーが表示される場合は、 [Postman 設定](https://learning.getpostman.com/docs/postman/launching_postman/settings)で SSL 証明書の検証を無効にする必要がある場合があります。

### <a name="obtain-a-bearer-token"></a>ベアラー トークンを取得します。

Web API に認証された要求を作成するには、ベアラー トークンが必要です。 Postman では、簡単に Azure AD B2C テナントにサインインし、トークンを取得できます。

1. **承認** タブで、**型** ドロップダウンで、 **OAuth 2.0**します。 **承認データを追加**] ドロップダウンで、[**要求ヘッダー**します。 選択**新しいアクセス トークン取得**します。

    ![[承認] タブの設定](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. 完了、**新しいアクセス トークンの取得**ダイアログ ボックスの次のとおりです。

   |                設定                 |                                             Value                                             |                                                                                                                                    メモ                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      **トークン名**       |                                          *トークン {name}*                                       |                                                                                                                   トークンのわかりやすい名前を入力します。                                                                                                                    |
   |      **付与タイプ**       |                                           暗黙的                                            |                                                                                                                                                                                                                                                                              |
   |     **コールバック URL**      |                                 `https://getpostman.com/postman`                              |                                                                                                                                                                                                                                                                              |
   |       **認証 URL**        | `https://login.microsoftonline.com/{tenant domain name}/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` |  置換 *{テナント ドメイン名}* テナントのドメイン名を使用します。 **重要な**: この URL は、としてはあるものと同じドメイン名をいる必要があります`AzureAdB2C.Instance`で web API の*appsettings.json*ファイル。 注記&dagger;します。                                                  |
   |       **クライアント ID**       |                *{Postman アプリの入力**アプリケーション ID**}*                              |                                                                                                                                                                                                                                                                              |
   |         **スコープ**         |         `https://{tenant domain name}/{api}/user_impersonation openid offline_access`       | 置換 *{テナント ドメイン名}* テナントのドメイン名を使用します。 置換 *{api}* アプリ ID URI を持つ指定した web API 最初に登録したときに (この場合、 `api`)。 URL のパターン:`https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope name}`します。         |
   |         **状態**         |                                      *{空白のままにしました*                                          |                                                                                                                                                                                                                                                                              |
   | **クライアントの認証** |                                クライアントの資格情報の本文で送信します。                                |                                                                                                                                                                                                                                                                              |

    > [!NOTE]
    > &dagger; Azure Active Directory B2C ポータルで、ポリシーの設定 ダイアログには、2 つの可能な Url が表示されます: 1 つの形式で`https://login.microsoftonline.com/`{テナント ドメイン名}/{追加パス情報} およびその他の形式で`https://{tenant name}.b2clogin.com/`{テナント ドメイン名}/{追加パス情報}。 **重要な**内にあるドメインで`AzureAdB2C.Instance`で web API の*appsettings.json*ファイル構造で、web アプリの使用と一致する*appsettings.json*ファイル。 これは、Postman の [認証 URL] フィールドに使用する同じドメインです。 Visual Studio が、ポータルに表示される内容よりもわずかに異なる URL 形式を使用することに注意してください。 ドメインが一致する限り、URL は機能します。

3. 選択、**トークン要求**ボタンをクリックします。

4. Postman では、Azure AD B2C テナントのサインイン ダイアログを含む新しいウィンドウが開きます。 (この場合、ポリシーをテストする 1 つが作成された) 既存のアカウントでサインインまたは選択**今すぐサインアップ**新しいアカウントを作成します。 **パスワードを忘れた場合でしょうか。** 忘れたパスワードをリセットするリンクを使用します。

5. 正常にサインインした後、ウィンドウを閉じると、**アクセス トークンの管理**ダイアログが表示されます。 クリックし、一番下までスクロール、**使用トークン**ボタンをクリックします。

    !["使用 Token"のボタンの検索場所](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>認証を使用した web API をテストします。

選択、**送信**もう一度要求を送信するボタンをクリックします。 今回は、応答の状態は*200 OK* JSON ペイロードが応答に表示されると**本文** タブ。

![ペイロードと成功状態](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>次のステップ:

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Azure Active Directory B2C テナントを作成します。
> * Azure AD B2C では、Web API を登録します。
> * Visual Studio を使用すると、認証に Azure AD B2C テナントを使用するのにように構成、Web API を作成できます。
> * Azure AD B2C テナントの動作を制御するポリシーを構成します。
> * 、ログイン ダイアログを表示する web アプリをシミュレートするために Postman を使用しては、トークンを取得し、使用して、web API に対する要求をします。

学習することにより、API の開発を続行するには。

* [セキュリティで保護された ASP.NET Core web アプリが Azure AD B2C を使用して](xref:security/authentication/azure-ad-b2c)します。
* [Azure AD B2C を使用して .NET web アプリから .NET web API を呼び出す](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet)します。
* [Azure AD B2C ユーザー インターフェイスをカスタマイズ](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)します。
* [パスワードの複雑さの要件を構成する](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)します。
* [多要素認証を有効にする](/azure/active-directory-b2c/active-directory-b2c-reference-mfa)します。
* など、追加の id プロバイダーを構成[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)、 [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app)、 [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app)、 [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app)、 [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)、およびその他。
* [Azure AD Graph API を使用して、](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) Azure AD B2C テナントからグループのメンバーシップなどの追加のユーザー情報を取得します。
