---
title: Facebook、Google、ASP.NET Core での外部プロバイダーの認証
author: rick-anderson
description: このチュートリアルでは、OAuth 2.0 と外部の認証プロバイダーを使用して ASP.NET Core アプリを構築する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644816"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>Facebook、Google、ASP.NET Core での外部プロバイダーの認証

作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)

このチュートリアルでは、ユーザーが OAuth 2.0 と外部の認証プロバイダーの資格情報を使用してサインインできる、ASP.NET Core 3.0 アプリを構築する方法について説明します。

以下のセクションでは、[Facebook](xref:security/authentication/facebook-logins)、[Twitter](xref:security/authentication/twitter-logins)、[Google](xref:security/authentication/google-logins)、および [Microsoft](xref:security/authentication/microsoft-logins) の各プロバイダーを対象とします。また、この記事で作成するスタート プロジェクトを使用します。 他のプロバイダーは、[AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers)、[AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) などのサードパーティ パッケージで利用できます。

既存の資格情報でユーザーがサインインできるようになると:

* ユーザーにとって便利です。
* サインイン プロセスの複雑な管理の多くが、サード パーティに移ります。

ソーシャル ログインによってトラフィックとユーザーの変換を促進する方法の例については、[Facebook](https://www.facebook.com/unsupportedbrowser) と [Twitter](https://dev.twitter.com/resources/case-studies) によるケース スタディを参照してください。

## <a name="create-a-new-aspnet-core-project"></a>新しい .NET Core プロジェクトを作成する

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 新しいプロジェクトを作成します。
* **[ASP.NET Core Web アプリケーション]** 、 **[次へ]** の順に選択します。
* **[プロジェクト名]** を指定して、 **[場所]** を確認または変更します。 **[作成]** を選択します。
* ドロップダウン (**ASP.NET Core {X.y}** ) で ASP.NET Core の最新バージョンを選択し、 **[Web アプリケーション]** を選択します。
* **[認証]** の下で、 **[変更]** を選択して認証を **[個人のユーザー アカウント]** に設定します。 **[OK]** を選択します。
* **[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[作成]** を選択します。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* ターミナルを開きます。  Visual Studio Code の場合は、[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開くことができます。

* ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。

* Windows の場合、次のコマンドを実行します。

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  macOS および Linux の場合は、次のコマンドを実行します。

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * `dotnet new` コマンドでは、*WebApp1* フォルダーに新しい Razor Pages プロジェクトが作成されます。
  * `-au Individual` によって、個々の認証に対するコードを作成します。
  * `-uld` では、Windows 用の SQL Server Express の軽量バージョンである、LocalDB を使用します。 `-uld` を省略して SQLite を使用します。
  * `code` コマンドでは、Visual Studio Code の新しいインスタンス内に *WebApp1* フォルダーが開かれます。

---

## <a name="apply-migrations"></a>移行を適用する

* アプリを実行し、 **[登録]** リンクを選択します。
* 新しいアカウントの電子メール アドレスとパスワードを入力し、 **[登録]** を選択します。
* 指示に従って移行を適用します。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>SecretManager を使用して、ログイン プロバイダーから割り当てられたトークンを格納する

ソーシャル ログイン プロバイダーは、登録プロセス中に**アプリケーション ID** トークンと**アプリケーション シークレット** トークンを割り当てます。 完全なトークン名はプロバイダーにより異なります。 これらのトークンは、アプリが API にアクセスするために使用する資格情報を示します。 トークンは、[Secret Manager](xref:security/app-secrets#secret-manager) のヘルプにより、アプリの構成にリンクすることが可能な "シークレット" になります。 Secret Manager は、*appsettings.json* などの構成ファイルのトークンに格納される、セキュリティがさらに向上した方法です。

> [!IMPORTANT]
> Secret Manager は、開発目的のみのためのものです。 [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)により、Azure テストと運用のシークレットを格納し、保護することが可能です。

「[Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets)」(ASP.NET Core での開発中にアプリのシークレットを安全に格納する) トピックの手順に従い、以下の各ログイン プロバイダーから割り当てられたトークンを格納できるようにします。

## <a name="setup-login-providers-required-by-your-application"></a>アプリケーションに必要なログイン プロバイダーをセットアップする

各プロバイダーを使用するようにアプリケーションを構成するには、以下のトピックを参照してください。

* [Facebook](xref:security/authentication/facebook-logins) の手順
* [Twitter](xref:security/authentication/twitter-logins) の手順
* [Google](xref:security/authentication/google-logins) の手順
* [Microsoft](xref:security/authentication/microsoft-logins) の手順
* [その他のプロバイダー](xref:security/authentication/otherlogins)の手順

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>必要に応じてパスワードを設定する

外部ログイン プロバイダーに登録するときに、アプリケーションにパスワードは登録していません。 そのため、サイトのパスワードを作成し、記憶する作業は軽減されますが、外部ログイン プロバイダーに依存することにもなります。 外部ログイン プロバイダーを使用できない場合、Web サイトにサインインすることができません。

外部プロバイダーでのサインイン プロセス中に設定した電子メール アドレスを使用して、パスワードを作成し、サインインするには、次の手順を実行します。

* 右上にある **[Hello &lt; 電子メール エイリアス&gt;]** リンクを選択して**管理**ビューに移動します。

![Web アプリケーションの管理ビュー](index/_static/pass1a.png)

* **[作成]** を選択します。

![パスワード ページを設定する](index/_static/pass2a.png)

* 有効なパスワードを設定します。そのパスワードと電子メール アドレスを使用してサインインできます。

## <a name="next-steps"></a>次の手順

* ログイン ボタンをカスタマイズする方法については、[こちらの GitHub イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/10563)を参照してください。
* このキーの順に押します。では、外部認証プロバイダーを紹介し、外部ログインを ASP.NET Core アプリケーションに追加するために必要な前提条件について説明しました。
* アプリケーションに必要なプロバイダーのログインを構成するには、各プロバイダーのページを参照してください。
* ユーザーとそのアクセス トークン許可および更新トークンに関する追加のデータを保持することをお勧めします。 詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。
