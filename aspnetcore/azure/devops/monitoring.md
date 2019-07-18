---
title: 監視とデバッグ - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: 監視と ASP.NET Core と Azure で DevOps ソリューションの一部として、コードのデバッグ
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68307959"
---
# <a name="monitor-and-debug"></a>監視とデバッグ

アプリ展開と DevOps パイプラインを構築するには、重要性を監視し、アプリのトラブルシューティングの方法を理解します。

このセクションで、次のタスクを完了します。

* 基本的な監視とトラブルシューティング、Azure portal でのデータを検索します。
* Azure Monitor ですべての Azure サービス間でのメトリックについて詳しく説明を提供する方法について説明します。
* アプリのプロファイリング用 Application Insights による web アプリを接続します。
* ログ記録をオンにし、ログをダウンロードする方法を学習します
* Stream のログをリアルタイムで
* アラートを設定する方法を学習します
* リモート Azure App Service web アプリのデバッグについて説明します。

## <a name="basic-monitoring-and-troubleshooting"></a>基本的な監視とトラブルシューティング

App Service web apps が簡単にリアルタイムで監視されます。 Azure portal では、わかりやすいチャートやグラフのメトリックを表示します。

1. 開く、 [Azure portal](https://portal.azure.com)、順に移動します、 *mywebapp\<unique_number\>*  App Service。

1. **概要**タブには、最新のメトリックを表示するグラフを含む、「概要」の有用な情報が表示されます。

    ![スクリーン ショットが表示された [概要] パネル](./media/monitoring/overview.png)

    * **Http 5xx**:サーバー側のエラーの数。通常は ASP.NET Core コードで例外が発生します。
    * **データ**:Web アプリに送信されるデータの受信。
    * **送信データ**:Web アプリからクライアントへのデータ送信。
    * **要求**:HTTP 要求の数。
    * **平均応答時間**:Web アプリが HTTP 要求に応答するまでの平均時間。

    このページ上のトラブルシューティングと最適化のためのいくつかのセルフ サービス ツールにもあります。

    ![スクリーン ショットが表示されたセルフ サービス ツール](./media/monitoring/wizards.png)

    * **診断し、問題の解決**はセルフ サービスのトラブルシューティング ツール。
    * **Application Insights**パフォーマンスとアプリの動作のプロファイルには、このセクションでは、後述します。
    * **App Service Advisor**はアプリのエクスペリエンスを調整する推奨を作成します。

## <a name="advanced-monitoring"></a>高度な監視

[Azure Monitor](/azure/monitoring-and-diagnostics/)はすべてのメトリックを監視および Azure サービス間でアラートを設定するための一元的なサービスです。 Azure Monitor 内で、管理者が細かくのパフォーマンスを追跡および傾向を特定できます。 各 Azure サービスは、独自[一連のメトリック](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)Azure Monitor にします。

## <a name="profile-with-application-insights"></a>Application Insights でのプロファイル

[Application Insights](/azure/application-insights/app-insights-overview)はパフォーマンスと安定性の web アプリとユーザーがそれらを使用する方法を分析するための Azure のサービスです。 Application Insights からデータは、Azure Monitor のより深いです。 データには、開発者および管理者にアプリを向上させるためのキーの情報を提供できます。 Application Insights は、コードを変更せずに Azure App Service リソースに追加できます。

1. 開く、 [Azure portal](https://portal.azure.com)、順に移動します、 *mywebapp\<unique_number\>*  App Service。
1. **概要**タブをクリックし、 **Application Insights**を並べて表示します。

    ![Application Insights タイルします。](./media/monitoring/app-insights.png)

1. 選択、**新しいリソースを作成**ラジオ ボタンをクリックします。 既定のリソース名を使用し、Application Insights リソースの場所を選択します。 場所の web アプリと一致する必要はありません。

    ![Application Insights のセットアップ](./media/monitoring/new-app-insights.png)

1. **ランタイム/フレームワーク**、 **ASP.NET Core**します。 既定の設定をそのまま使用します。
1. **[OK]** を選択します。 選択の確認を求められたら、**続行**します。
1. リソースが作成された後は、Application Insights のページに直接移動する Application Insights リソースの名前をクリックします。

    ![新しい Application Insights リソースが準備完了](./media/monitoring/new-app-insights-done.png)

アプリが使用されるため、データが蓄積されます。 選択**更新**ブレードに新しいデータを再読み込みします。

![Application Insights の概要 タブ](./media/monitoring/app-insights-overview.png)

Application Insights は、追加構成なしで有用なサーバー側の情報を提供します。 Application Insights から最大限の価値を取得する[Application Insights SDK を使用してアプリケーションをインストルメント化](/azure/application-insights/app-insights-asp-net-core)します。 適切に構成されたサービスは、web サーバーおよびクライアント側のパフォーマンスを含む、ブラウザー間でのエンド ツー エンド監視を提供します。 詳細については、、 [Application Insights のドキュメント](/azure/application-insights/app-insights-overview)を参照してください。

## <a name="logging"></a>ログの記録

Azure App Service で既定では、web サーバーとアプリのログが無効になります。 次の手順でログを有効にします。

1. 開く、 [Azure portal](https://portal.azure.com)に移動し、 *mywebapp\<unique_number\>*  App Service。
1. 左側のメニューでスクロールして、**監視**セクション。 選択**診断ログ**します。

    ![診断ログのリンク](./media/monitoring/logging.png)

1. オンにする**アプリケーションのログ記録 (ファイル システム)** します。 メッセージが表示されたら、アプリ、web アプリでのログ記録を有効にする拡張機能をインストールするボックスをクリックします。
1. 設定**Web サーバーのログ記録**に**ファイル システム**します。
1. 入力、**保有期間**日以内にします。 たとえば、30 です。
1. **[保存]** をクリックします。

Web アプリでは、ASP.NET Core と web サーバー (App Service) のログが生成されます。 表示される FTP または FTPS の情報を使用してダウンロードできます。 パスワードは、このガイドの前半で作成したデプロイ資格情報と同じです。 ログを指定できます[PowerShell または Azure CLI をローカル コンピューターに直接ストリーム転送](/azure/app-service/web-sites-enable-diagnostic-log#download)します。 記録をできますも[Application Insights で表示](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)します。

## <a name="log-streaming"></a>ログ ストリーミング

アプリと web サーバーのログは、ポータルからリアルタイムでストリーミングできます。

1. 開く、 [Azure portal](https://portal.azure.com)に移動し、 *mywebapp\<unique_number\>*  App Service。
1. 左側のメニューでスクロールして、**監視**セクションし、選択**ログ ストリーム**します。

    ![ログ ストリーム リンクを示すスクリーン ショット](./media/monitoring/log-stream.png)

ログこともできます[Azure CLI または Azure PowerShell を使用してストリーミング](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)など、Cloud Shell を使用します。

## <a name="alerts"></a>アラート

Azure Monitor も用意されています。[リアルタイム アラート](/azure/monitoring-and-diagnostics/insights-alerts-portal)メトリック、イベントの管理、およびその他の条件に基づいて。

> *注 :現在、web アプリのメトリックに対するアラートは、Alerts (クラシック) サービスでのみ使用できます。*

[アラート (クラシック) サービス](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)Azure Monitor で、または 、**監視**App Service の設定のセクション。

![アラート (クラシック) のリンク](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>ライブ デバッグ

Azure App Service、 [Visual Studio でリモートでデバッグ](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)ログが十分な情報を指定しない場合。 ただし、リモート デバッグには、アプリは、デバッグ シンボルを指定してコンパイルする必要があります。 デバッグしないでくださいで行う、運用環境を除く、最後の手段として。

## <a name="conclusion"></a>まとめ

このセクションでは、次のタスクが完了しました。

* 基本的な監視とトラブルシューティング、Azure portal でのデータを検索します。
* Azure Monitor ですべての Azure サービス間でのメトリックについて詳しく説明を提供する方法について説明します。
* アプリのプロファイリング用 Application Insights による web アプリを接続します。
* ログ記録をオンにし、ログをダウンロードする方法を学習します
* Stream のログをリアルタイムで
* アラートを設定する方法を学習します
* リモート Azure App Service web アプリのデバッグについて説明します。

## <a name="additional-reading"></a>その他の参考資料

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Application Insights で Azure web アプリのパフォーマンスを監視します。](/azure/application-insights/app-insights-azure-web-apps)
* [Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)
* [Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Monitor での Azure サービス クラシック メトリック アラートを作成する Azure portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
