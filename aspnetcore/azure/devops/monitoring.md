---
title: 監視とデバッグ - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ASP.NET Core と Azure を使用した DevOps ソリューションの一部としてのコードの監視とデバッグ
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: azure/devops/monitor
ms.openlocfilehash: 1d8ed99f4387dbc99929164c558cc2ce14bd9ea0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647456"
---
# <a name="monitor-and-debug"></a>監視とデバッグ

アプリをデプロイし、DevOps パイプラインを作成したら、アプリの監視とトラブルシューティングの方法を理解することが重要です。

このセクションでは、次のタスクを実行します。

* Azure portal で基本的な監視とトラブルシューティングのデータを見つける
* Azure Monitor が、すべての Azure サービスにわたってメトリックに関するより詳しい調査をどのように提供しているかを学習する
* アプリ プロファイル用に Web アプリを Application Insights と接続する
* ログ記録を有効にし、ログをダウンロードする場所を確認する
* リアルタイムでログをストリームする
* アラートを設定する場所を学習する
* Azure App Service Web アプリのリモート デバッグについて学習する

## <a name="basic-monitoring-and-troubleshooting"></a>基本的な監視とトラブルシューティング

App Service Web アプリは、リアルタイムで簡単に監視できます。 Azure portal では、わかりやすいチャートとグラフでメトリックがレンダリングされます。

1. [Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。

1. **[概要]** タブには、最近のメトリックを表示するグラフなど、"ひとめでわかる" 情報が表示されます。

    ![概要パネルを示すスクリーンショット](./media/monitoring/overview.png)

    * **Http 5xx**:サーバー側のエラー (通常は ASP.NET Core コードの例外) の数。
    * **受信データ**: Web アプリで受信されるデータ受信。
    * **送信データ**: Web アプリからクライアントへのデータ送信。
    * **要求**: HTTP 要求の数。
    * **平均応答時間**: Web アプリが HTTP 要求に応答するまでの平均時間。

    トラブルシューティングと最適化のためのセルフサービス ツールは、このページにもいくつかあります。

    ![セルフサービス ツールを示すスクリーンショット](./media/monitoring/wizards.png)

    * **問題の診断と解決**は、セルフサービスのトラブルシューティング ツールです。
    * **Application Insights** は、パフォーマンスとアプリの動作のプロファイル用です。このセクションで後述します。
    * **App Service Advisor** では、アプリのエクスペリエンスを調整するための推奨事項が作成されます。

## <a name="advanced-monitoring"></a>高度な監視

[Azure Monitor](/azure/monitoring-and-diagnostics/) は、すべてのメトリックを監視し、Azure サービス全体にアラートを設定するための一元化されたサービスです。 Azure Monitor では、管理者はパフォーマンスを細かく追跡し、傾向を特定できます。 各 Azure サービスでは、Azure Monitor に対して独自の[メトリック セット](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)が提供されています。

## <a name="profile-with-application-insights"></a>Application Insights を使用したプロファイル

[Application Insights](/azure/application-insights/app-insights-overview) は、Web アプリのパフォーマンスと安定性、およびユーザーがそれらをどのように使用しているかを分析するための Azure サービスです。 Application Insights のデータは、Azure Monitor よりも広範で深いものです。 開発者や管理者は、このデータからアプリを改善するための重要な情報を得ることができます。 Application Insights は、コードを変更することなく、Azure App Service リソースに追加できます。

1. [Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。
1. **[概要]** タブで、 **[Application Insights]** タイルをクリックします。

    ![[Application Insights] タイル](./media/monitoring/app-insights.png)

1. **[新しいリソースの作成]** ラジオ ボタンを選択します。 既定のリソース名を使用して、Application Insights リソースの場所を選択します。 この場所は、Web アプリの場所と一致している必要はありません。

    ![Application Insights のセットアップ](./media/monitoring/new-app-insights.png)

1. **[ランタイム/フレームワーク]** には、 **[ASP.NET Core]** を選択します。 既定の設定をそのまま使用します。
1. **[OK]** を選択します。 確認を求めるメッセージが表示されたら、 **[続行]** を選択します。
1. リソースが作成されたら、Application Insights リソースの名前をクリックして、[Application Insights] ページに直接移動します。

    ![新しい Application Insights リソースの準備ができました](./media/monitoring/new-app-insights-done.png)

アプリが使用されると、データが蓄積されます。 **[更新]** を選択して、新しいデータでブレードを再度読み込みます。

![Application Insights の [概要] タブ](./media/monitoring/app-insights-overview.png)

追加の構成をしなくても、Application Insights から有用なサーバー側の情報が提供されます。 Application Insights から最大限の価値を得るには、[Application Insights SDK を使用してアプリをインストルメント化](/azure/application-insights/app-insights-asp-net-core)します。 適切に構成されている場合、サービスは、クライアント側のパフォーマンスを含め、Web サーバーとブラウザー全体にエンドツーエンドの監視を提供します。 詳細については、[Application Insights のドキュメント](/azure/application-insights/app-insights-overview)を参照してください。

## <a name="logging"></a>ログの記録

Azure App Service では、Web サーバーとアプリのログは既定で無効になっています。 次の手順でログを有効にします。

1. [Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。
1. 左側のメニューで、 **[監視]** セクションまで下にスクロールします。 **[診断ログ]** を選択します。

    ![診断ログのリンク](./media/monitoring/logging.png)

1. **[アプリケーション ログ (ファイルシステム)]** をオンにします。 メッセージが表示されたら、ボックスをクリックして拡張機能をインストールし、Web アプリでアプリのログ記録を有効にします。
1. **[Web サーバーのログ記録]** を **[ファイル システム]** に設定します。
1. **[保持期間]** を日数で入力します。 たとえば、30 を入力します。
1. **[保存]** をクリックします。

Web アプリ用に ASP.NET Core と Web サーバー (App Service) のログが生成されます。 これらは、表示される FTP/FTPS 情報を使用してダウンロードできます。 パスワードは、このガイドで前に作成したデプロイ資格情報と同じです。 ログは、[PowerShell または Azure CLI を使用してローカル コンピューターに直接ストリームする](/azure/app-service/web-sites-enable-diagnostic-log#download)ことができます。 ログは、[Application Insights で表示](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)することもできます。

## <a name="log-streaming"></a>ログ ストリーミング

アプリと Web サーバーのログは、ポータルを使用してリアルタイムでストリームできます。

1. [Azure portal](https://portal.azure.com) を開き、"*mywebapp\<unique_number\>* " App Service に移動します。
1. 左側のメニューで、 **[監視]** セクションまで下にスクロールして、 **[ログ ストリーム]** を選択します。

    ![ログ ストリーム リンクを示すスクリーンショット](./media/monitoring/log-stream.png)

ログは、[Azure CLI または Azure PowerShell (Cloud Shell を含む) を使用してストリームする](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)こともできます。

## <a name="alerts"></a>アラート

Azure Monitor では、メトリック、管理イベント、その他の条件に基づいて、[リアルタイム アラート](/azure/monitoring-and-diagnostics/insights-alerts-portal)も提供します。

> *注:現在、Web アプリのメトリックに対するアラートは、アラート (クラシック) サービスでのみ使用できます。*

[アラート (クラシック) サービス](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)は Azure Monitor 内、または App Service 設定の **[監視]** セクションの下にあります。

![アラート (クラシック) リンク](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>ライブ デバッグ

ログが十分な情報を提供していない場合は、Azure App Service を [Visual Studio を使用してリモートでデバッグ](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)できます。 ただし、リモート デバッグでは、デバッグ シンボルを使用してアプリをコンパイルする必要があります。 デバッグは、最後の手段として以外は、運用環境では行わないでください。

## <a name="conclusion"></a>まとめ

このセクションでは、次のタスクを完了しました。

* Azure portal で基本的な監視とトラブルシューティングのデータを見つける
* Azure Monitor が、すべての Azure サービスにわたってメトリックに関するより詳しい調査をどのように提供しているかを学習する
* アプリ プロファイル用に Web アプリを Application Insights と接続する
* ログ記録を有効にし、ログをダウンロードする場所を確認する
* リアルタイムでログをストリームする
* アラートを設定する場所を学習する
* Azure App Service Web アプリのリモート デバッグについて学習する

## <a name="additional-reading"></a>その他の参考資料

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Application Insights を使用した Azure Web アプリのパフォーマンスの監視](/azure/application-insights/app-insights-azure-web-apps)
* [Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)
* [Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Monitor での Azure サービス クラシック メトリック アラートの作成 - Azure portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
