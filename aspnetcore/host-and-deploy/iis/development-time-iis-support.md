---
title: Visual Studio for ASP.NET Core の開発時 IIS サポート
author: guardrex
description: ASP.NET Core アプリが Windows Server 上の IIS で実行されている場合に、そのデバッグのサポートを検出します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 704a8dae9da904e4bbdfae0754a6fcdabee6dc82
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952033"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>Visual Studio for ASP.NET Core の開発時 IIS サポート

著者: [Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Luke Latham](https://github.com/guardrex)

この記事では、Windows Server 上の IIS で実行されている ASP.NET Core アプリをデバッグするための、[Visual Studio](https://visualstudio.microsoft.com) のサポートについて説明します。 このトピックでは、このシナリオを有効にしてプロジェクトを設定する手順を示します。

## <a name="prerequisites"></a>必須コンポーネント

* [Visual Studio (Windows 版)](https://visualstudio.microsoft.com/downloads/)。
* **ASP.NET および Web の開発**ワークロード
* **.NET Core クロスプラットフォームの開発**ワークロード
* X.509 セキュリティ証明書 (HTTPS のサポート用)

## <a name="enable-iis"></a>IIS を有効にする

1. Windows で、 **[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]**  >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。
1. **[インターネット インフォメーション サービス]** チェック ボックスをオンにします。 **[OK]** を選択します。

IIS のインストールには、システムの再起動が必要になる場合があります。

## <a name="configure-iis"></a>IIS の構成

IIS には、次のように構成された Web サイトが含まれている必要があります。

* **ホスト名** &ndash; 通常、`localhost` の**ホスト名**では**既定の Web サイト**が使用されます。 ただし、一意のホスト名を持つ任意の有効な IIS Web サイトが動作します。
* **サイト バインド**
  * HTTPS を必要とするアプリの場合は、証明書でポート 443 へのバインドを作成します。 通常は、**IIS Express 開発証明書**が使用されますが、任意の有効な証明書を使用できます。
  * HTTP を使用するアプリの場合は、ポート 80 へのバインドが存在することを確認するか、または新しいサイト用にポート 80 へのバインドを作成します。
  * HTTP または HTTPS のいずれかに対する 1 つのバインドを使用します。 **HTTP と HTTPS の両方のポートに同時にバインドすることはサポートされていません。**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio の開発時 IIS サポートを有効にする

1. Visual Studio インストーラーを起動します。
1. IIS 開発時のサポート用に使用する Visual Studio のインストールの **[変更]** を選択します。
1. **ASP.NET と Web 開発**ワークロードで、**開発時 IIS サポート** コンポーネントを探してインストールします。

   ワークロードの右側にある **[インストールの詳細]** パネルの**開発時 IIS サポート**の下の **[省略可能]** セクションの一覧に、コンポーネントが表示されます。 このコンポーネントにより、IIS を使用した ASP.NET Core アプリの実行に必要なネイティブの IIS モジュールである [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。

## <a name="configure-the-project"></a>プロジェクトを構成する

### <a name="https-redirection"></a>HTTPS リダイレクト

HTTPS が必要な新しいプロジェクトの場合は、 **[新しい ASP.NET Core Web アプリケーションを作成する]** ウィンドウで、 **[HTTPS 用の構成]** のチェック ボックスをオンにします。 チェック ボックスをオンにすると、アプリの作成時に [HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)がアプリに追加されます。

HTTPS が必要な既存のプロジェクトでは、`Startup.Configure` 内で HTTPS リダイレクトと HSTS ミドルウェアを使用します。 詳細については、「<xref:security/enforcing-ssl>」を参照してください。

HTTP を使用するプロジェクトの場合は、[HTTPS リダイレクトと HSTS ミドルウェア](xref:security/enforcing-ssl)はアプリに追加されません。 アプリの構成は必要ありません。

### <a name="iis-launch-profile"></a>IIS 起動プロファイル

新しい起動プロファイルを作成して、開発時 IIS サポートを追加します。

::: moniker range=">= aspnetcore-3.0"

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックします。 **[プロパティ]** を選びます。 **[デバッグ]** タブを開きます。
1. **[プロファイル]** で、 **[新規]** ボタンを選択します。 ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。 **[OK]** をクリックして、プロファイルを作成します。
1. **[起動]** の設定で、一覧から **[IIS]** を選択します。
1. **[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。

   アプリで HTTPS が必要な場合は、HTTPS エンドポイント (`https://`) を使用します。 HTTP の場合は、HTTP エンドポイント (`http://`) を使用します。

   [前に指定した IIS 構成で使用されている](#configure-iis)ものと同じホスト名とポートを指定します。通常は `localhost` です。

   URL の最後でアプリの名前を指定します。

   たとえば、`https://localhost/WebApplication1` (HTTPS) や `http://localhost/WebApplication1`(HTTP) は有効なエンドポイント URL です。
1. **[環境変数]** セクションで、 **[追加]** ボタンを選択します。 `ASPNETCORE_ENVIRONMENT` の **[名前]** および `Development` の **[値]** で環境変数を指定します。
1. **[Web サーバーの設定]** 領域で、 **[アプリの URL]** を **[ブラウザーの起動]** エンドポイント URL に使用したものと同じ値に設定します。
1. Visual Studio 2019 以降の **[ホスティング モデル]** の設定では、 **[既定]** を選択して、プロジェクトによって使用されるホスティング モデルを使用します。 プロジェクトのプロジェクト ファイルで `<AspNetCoreHostingModel>` プロパティが設定されている場合は、プロパティ (`InProcess` または `OutOfProcess`) の値が使用されます。 プロパティが存在しない場合は、アプリの既定のホスティング モデルが使用され、それはインプロセスです。 アプリの通常のホスティング モデルとは異なるホスティング モデルを明示的に設定する必要があるアプリの場合は、必要に応じて、 **[ホスティング モデル]** を `In Process` または `Out Of Process` に設定します。
1. プロファイルを保存します。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. **ソリューション エクスプローラー**で、プロジェクトを右クリックします。 **[プロパティ]** を選びます。 **[デバッグ]** タブを開きます。
1. **[プロファイル]** で、 **[新規]** ボタンを選択します。 ポップアップ ウィンドウで、プロファイルに "IIS" という名前を付けます。 **[OK]** をクリックして、プロファイルを作成します。
1. **[起動]** の設定で、一覧から **[IIS]** を選択します。
1. **[ブラウザーの起動]** チェック ボックスをオンにして、エンドポイント URL を指定します。

   アプリで HTTPS が必要な場合は、HTTPS エンドポイント (`https://`) を使用します。 HTTP の場合は、HTTP エンドポイント (`http://`) を使用します。

   [前に指定した IIS 構成で使用されている](#configure-iis)ものと同じホスト名とポートを指定します。通常は `localhost` です。

   URL の最後でアプリの名前を指定します。

   たとえば、`https://localhost/WebApplication1` (HTTPS) や `http://localhost/WebApplication1`(HTTP) は有効なエンドポイント URL です。
1. **[環境変数]** セクションで、 **[追加]** ボタンを選択します。 `ASPNETCORE_ENVIRONMENT` の **[名前]** および `Development` の **[値]** で環境変数を指定します。
1. **[Web サーバーの設定]** 領域で、 **[アプリの URL]** を **[ブラウザーの起動]** エンドポイント URL に使用したものと同じ値に設定します。
1. Visual Studio 2019 以降の **[ホスティング モデル]** の設定では、 **[既定]** を選択して、プロジェクトによって使用されるホスティング モデルを使用します。 プロジェクトのプロジェクト ファイルで `<AspNetCoreHostingModel>` プロパティが設定されている場合は、プロパティ (`InProcess` または `OutOfProcess`) の値が使用されます。 プロパティが存在しない場合は、アプリの既定のホスティング モデルが使用され、それはアウトプロセスです。 アプリの通常のホスティング モデルとは異なるホスティング モデルを明示的に設定する必要があるアプリの場合は、必要に応じて、 **[ホスティング モデル]** を `In Process` または `Out Of Process` に設定します。
1. プロファイルを保存します。

::: moniker-end

Visual Studio を使用していない場合は、起動プロファイルを *Properties* フォルダーの [launchSettings.json](https://json.schemastore.org/launchsettings) ファイルに手動で追加します。 次の例では、HTTPS プロトコルを使用するようにプロファイルを構成しています。

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

`applicationUrl` エンドポイントと `launchUrl` エンドポイントが一致し、IIS バインド構成と同じプロトコル (HTTP または HTTPS) が使用されていることを確認してください。

## <a name="run-the-project"></a>プロジェクトを実行する

Visual Studio を管理者として実行します。

* ビルド構成のドロップダウン リストが **[デバッグ]** に設定されていることを確認します。
* [[デバッグの開始] ボタン](/visualstudio/debugger/debugger-feature-tour)を **[IIS]** プロファイルに設定し、ボタンを選択してアプリを起動します。

管理者として実行していない場合、Visual Studio によって再起動を求められる場合があります。 その場合は、Visual Studio を再起動します。

信頼されていない開発証明書を使用すると、信頼されていない証明書に対する例外を作成するように、ブラウザーによって求められる場合があります。

> [!NOTE]
> [マイ コードのみ](/visualstudio/debugger/just-my-code)とコンパイラの最適化を使用してリリースのビルド構成をデバッグすると、エクスペリエンスの品質が低下します。 たとえば、ブレーク ポイントがヒットしません。

## <a name="additional-resources"></a>その他の技術情報

* [IIS での IIS マネージャーの概要](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>
