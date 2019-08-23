---
title: ASP.NET Core の負荷/ストレステスト
author: Jeremy-Meng
description: ロードテストと ASP.NET Core アプリのストレステストを行うための、いくつかの注目すべきツールとアプローチについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 7a9dfc1fedf747ab26daa573b61ed01c31709058
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975243"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core の負荷/ストレステスト

ロードテストとストレステストは、web アプリのパフォーマンスと拡張性を確保するために重要です。 これらの目標は、よく似たテストを共有している場合でも異なります。

**ロードテスト**&ndash;アプリが特定のシナリオで特定のユーザーの負荷を処理できるかどうかをテストしながら、応答の目標を満たすことができます。 アプリは通常の状態で実行されます。

**ストレステスト**&ndash;非常に長い時間で頻繁に実行する場合は、アプリの安定性をテストします。 テストでは、アプリで負荷が急増または徐々に増加しているか、アプリのコンピューティングリソースが制限されているため、ユーザー負荷が高くなります。

ストレステストでは、負荷がかかっているアプリが障害から復旧できるかどうかを判断し、期待される動作に適切に戻ります。 ストレス下では、アプリは通常の状況では実行されません。

Visual studio 2019 は、ロードテスト機能を備えた最新バージョンの Visual Studio です。 今後ロードテストツールが必要なお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの別のツールをお勧めします。 詳細については、「 [Visual Studio 2019 リリースノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。

Azure DevOps のロードテストサービスは、2020で終了します。 詳細については、「[クラウドベースのロードテストサービスの終了](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)」を参照してください。

## <a name="visual-studio-tools"></a>Visual Studio ツール

ユーザーは Visual Studio を使用して、web パフォーマンステストとロードテストを作成、開発、およびデバッグすることができます。 Web ブラウザーでアクションを記録することによって、テストを作成するためのオプションを使用できます。

Visual Studio 2017 を使用してロードテストプロジェクトを作成、構成、および実行する方法につい[ては、「クイックスタート:ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。

ロードテストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。

## <a name="azure-devops"></a>Azure DevOps

ロードテストの実行は、 [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts)サービスを使用して開始できます。

![Azure DevOps のロードテストのランディングページ](./load-tests/_static/azure-devops-load-test.png)

サービスは、次のテスト形式をサポートしています。

* Visual studio &ndash;で作成された visual studio Web テスト。
* アーカイブ内&ndash;の http アーカイブでキャプチャされた http トラフィックは、テスト中に再生されます。
* [URL ベース](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;ロードテスト、要求の種類、ヘッダー、およびクエリ文字列への url を指定できます。 実行設定パラメーター (期間、ロードパターン、ユーザー数など) を構成できます。
* [Apache JMeter](https://jmeter.apache.org/)。

## <a name="azure-portal"></a>Azure Portal

Azure portal を使用すると、Azure portal の App Service の **[パフォーマンス]** タブから[web アプリのロードテストを直接設定して実行でき](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)ます。

![Azure portal の Azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

テストは、指定された URL または Visual Studio Web テストファイルを使用した手動テストにすることができます。これにより、複数の Url をテストできます。

![Azure portal の新しいパフォーマンステストページ](./load-tests/_static/azure-appservice-perf-test-config.png)

テストの最後に、生成されたレポートには、アプリのパフォーマンス特性が表示されます。 統計の例を次に示します。

* 平均応答時間
* 最大スループット: 1 秒あたりの要求数
* 失敗率

## <a name="third-party-tools"></a>サードパーティ製のツール

次の一覧には、さまざまな機能セットを備えたサードパーティの web パフォーマンスツールが含まれています。

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [西風の WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
