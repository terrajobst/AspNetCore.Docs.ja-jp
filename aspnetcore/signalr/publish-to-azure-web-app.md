---
title: 発行、ASP.NET Core SignalR にアプリを Azure App Service
author: bradygaster
description: Azure App Service に ASP.NET Core SignalR のアプリを発行する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406114"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>発行、ASP.NET Core SignalR にアプリを Azure App Service

によって[Brady Gaster](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview)は、 [Microsoft クラウド コンピューティング](https://azure.microsoft.com/)ASP.NET Core を含む、web アプリをホストするためのプラットフォーム サービスです。

> [!NOTE]
> この記事では、Visual Studio から ASP.NET Core SignalR アプリの発行を指します。 詳細については、次を参照してください。 [for Azure SignalR サービス](https://azure.microsoft.com/services/signalr-service)します。

## <a name="publish-the-app"></a>アプリの発行

この記事では、Visual Studio でツールを使用して公開について説明します。 Visual Studio Code のユーザーが使用できる[Azure CLI](/cli/azure)アプリを Azure に発行するコマンド。 詳細については、次を参照してください。[コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを発行](/azure/app-service/app-service-web-get-started-dotnet)します。

1. **[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。

1. 確認します**App Service**と**新規作成**で選択されている場合は、**発行先を選択**ダイアログ。

1. 選択**プロファイルの作成**から、**発行**ダウン ドロップダウン ボタンをクリックします。

   次の表で説明されている情報を入力して、 **App Service の作成**ダイアログと選択**作成**。

   | アイテム               | 説明 |
   | ------------------ | ----------- |
   | **Name**           | アプリの一意の名前。 |
   | **サブスクリプション**   | アプリを使用する azure サブスクリプション。 |
   | **リソース グループ** | アプリが所属する関連リソースのグループです。 |
   | **ホスティング プラン**   | Web アプリ用のプランの価格を設定します。 |

1. 選択、 **Azure SignalR サービス**で、**依存関係** > **追加**ドロップダウン リスト。

   ![Azure SignalR サービスの選択範囲を示す追加のドロップダウン リストでの依存関係の領域](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. **Azure SignalR サービス**ダイアログ ボックスで、 **Azure SignalR サービスの新しいインスタンスを作成**です。

1. 提供、**名前**、**リソース グループ**、および**場所**します。 戻り、 **Azure SignalR サービス**ダイアログと選択**追加**します。

Visual Studio では、次のタスクを実行します。

* 発行プロファイルを作成します。 発行の設定を格納しています。
* 作成、 *Azure Web App*で提供された詳細。
* アプリを発行します。
* Web アプリが読み込まれますブラウザーを起動します。

アプリの URL の形式が`{APP SERVICE NAME}.azurewebsites.net`します。 たとえば、という名前のアプリ`SignalRChatApp`の URL が`https://signalrchatapp.azurewebsites.net`します。

場合、HTTP *502.2 - 無効なゲートウェイ*プレビュー リリースの .NET Core を対象とするアプリをデプロイするときにエラーが発生しを参照してください[Azure App Service に ASP.NET Core の展開のプレビュー リリース](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)問題を解決します。

## <a name="configure-the-app-in-azure-app-service"></a>Azure App Service でアプリを構成します。

> [!NOTE]
> *このセクションでは、Azure SignalR サービスを使用していないアプリにのみ適用されます。*
>
> アプリでは、Azure SignalR サービスを使用する場合、App Service に Application Request Routing (ARR) アフィニティおよびこのセクションで説明されている Web ソケットの構成は不要です。 Azure SignalR サービスに、アプリに直接ではなく、クライアントはその Web ソケットを接続します。

Azure SignalR サービス ホストされているアプリを有効にします。

* [ARR アフィニティ](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html)同じ App Service インスタンスにユーザーからの要求をルーティングします。 既定の設定は**で**します。
* [Web ソケット](xref:fundamentals/websockets)関数への Web ソケット トランスポートを許可します。 既定の設定は**オフ**します。

1. Azure portal での web アプリに移動します**App Services**します。
1. 開いている**構成** > **全般設定**します。
1. 設定**Web ソケット**に**で**します。
1. いることを確認**ARR アフィニティ**に設定されている**で**します。

## <a name="app-service-plan-limits"></a>App Service プランの制限します。

Web ソケットと他のトランスポートに制限は選択されている App Service プランに基づきます。 詳細については、次を参照してください、 *Azure Cloud Services の制限*と*App Service の制限*のセクションでは、 [Azure サブスクリプションとサービスの制限、クォータ、制約](/azure/azure-subscription-service-limits#app-service-limits)。アーティクルです。

## <a name="additional-resources"></a>その他の技術情報

* [Azure SignalR サービスとは何ですか。](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [コマンド ライン ツールを使用して Azure に ASP.NET Core アプリを発行します。](/azure/app-service/app-service-web-get-started-dotnet)
* [ホストし、Azure で ASP.NET Core プレビュー アプリを展開します。](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
