---
title: Visual Studio Code で ASP.NET Core アプリを Azure に公開する
author: ricardoserradas
description: Visual Studio Code を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5d92775d6245494c34bfe691d7ade663b2078d5
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082404"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Visual Studio Code で ASP.NET Core アプリを Azure に公開する

投稿者: [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

App Service の配置に関する問題を解決するには、「<xref:test/troubleshoot-azure-iis>」を参照してください。

## <a name="intro"></a>イントロ

このチュートリアルでは、ASP.Net Core MVC アプリケーションを作成し、Visual Studio Code 内にそれをデプロイする方法を学習します。

## <a name="set-up"></a>設定

- Azure アカウントをお持ちでない場合は、[Azure 無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成します。
- [.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします
- [Visual Studio Code](https://code.visualstudio.com/Download) をインストールします
  - Visual Studio Code の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)をインストールします
  - Visual Studio Code の [Azure App Service 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)をインストールし、構成してから先に進みます

## <a name="create-an-aspnet-core-mvc-project"></a>ASP.NET Core MVC プロジェクトを作成する

ターミナルを使用し、プロジェクトを作成するフォルダーに移動し、次のコマンドを使用します。

```dotnetcli
dotnet new mvc
```

フォルダーの構造は次のようになります。

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Visual Studio Code でプロジェクトを開く

プロジェクトが作成されたら、次のいずれかの方法を利用し、Visual Studio Code でプロジェクトを開きます。

### <a name="through-the-command-line"></a>コマンド ラインから

プロジェクトを作成したフォルダー内で次のコマンドを使用します。

```cmd
> code .
```

下のコマンドが機能しない場合、[このリンク](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)を参照し、インストールが正しく構成されていることを確認してください。

### <a name="through-visual-studio-code-interface"></a>Visual Studio Code インターフェイスから

- Visual Studio Code を開きます
- メニューで `File > Open Folder` を選択します
- MVC プロジェクトを作成したフォルダーのルートを選択します

プロジェクト フォルダーを開くと、ビルドやデバッグに必要なアセットが足りないというメッセージが表示されます。 [はい] を選択し、アセットを追加します。

![プロジェクトが読み込まれた Visual Studio Code インターフェイス](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

`.vscode` フォルダーがプロジェクト構造の下に作成されます。 これには次のファイルが含まれます。

```cmd
launch.json
tasks.json
```

.NET Core Web アプリのビルドとデバッグに役立つユーティリティ ファイルがあります。

## <a name="run-the-app"></a>アプリを実行する

アプリを Azure にデプロイする前に、ローカル コンピューターでアプリが正しく実行されていることを確認してください。

- F5 を押し、プロジェクトを実行します。

既定のブラウザーの新しいタブで Web アプリが実行を開始します。 起動の直後にプライバシーに関する警告が表示されます。 これはアプリが HTTP と HTTPS のいずれかで起動するためです。既定では HTTPS エンドポイントに移動します。

![アプリをローカルでデバッグするときに表示されるプライバシーに関する警告](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

デバッグ セッションを維持するには、`Advanced` をクリックし、次に `Continue to localhost (unsafe)` をクリックします。

## <a name="generate-the-deployment-package-locally"></a>デプロイ パッケージをローカルで生成する

- Visual Studio Code ターミナルを開きます
- 次のコマンドを使用し、`publish` という名前のサブフォルダーに `Release` パッケージを生成します。
  - `dotnet publish -c Release -o ./publish`
- 新しい `publish` フォルダーがプロジェクト構造の下に作成されます

![発行フォルダーの構造](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Azure App Service に発行する

Visual Studio Code 用の Azure App Service 拡張機能を活用し、下の手順で Azure App Service に直接、Web サイトを発行します。

### <a name="if-youre-creating-a-new-web-app"></a>新しい Web アプリを作成する場合

- `publish` フォルダーを右クリックし、`Deploy to Web App...` を選択します
- Web アプリを作成するサブスクリプションを選択します
- `Create New Web App` を選択します
- Web アプリの名前を入力します

拡張機能により新しい Web アプリが作成され、それにパッケージが自動的にデプロイされます。 デプロイが完了したら、`Browse Website` をクリックし、デプロイの有効性を検証します。

![デプロイが成功したときのメッセージ](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

`Browse Website` をクリックすると、既定のブラウザーでそこに移動します。

![新しい Web アプリが正常にデプロイされました](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>既存の Web アプリにデプロイする場合

- `publish` フォルダーを右クリックし、`Deploy to Web App...` を選択します
- 既存の Web アプリが存在するサブスクリプションを選択します
- 一覧から Web アプリを選択します
- Visual Studio Code から、既存のコンテンツを上書きするかどうかたずねられます。 `Deploy` をクリックして確定します

拡張機能により、更新後のコンテンツが Web アプリにデプロイされます。 完了したら、`Browse Website` をクリックし、デプロイの有効性を検証します。

![既存の Web アプリが正常にデプロイされました](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>次の手順

- [最初の Azure DevOps パイプラインを作成する](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>その他の技術情報

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Azure リソース グループ](/azure/azure-resource-manager/resource-group-overview#resource-groups)