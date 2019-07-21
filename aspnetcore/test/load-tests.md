---
title: ASP.NET Core のロード/ストレス テスト
author: Jeremy-Meng
description: いくつかの注目すべきツールとロード テストとストレスの ASP.NET Core アプリをテストするための方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 4b07dd1af7e0c1d3ce9baa167b69fd8f80df204a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815172"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core のロード/ストレス テスト

ロード テストとストレス テストは、web アプリが高パフォーマンスであることを確認する重要な拡張性の高いです。 多くの場合、類似のテストを共有する場合でも、その目標が異なります。

**ロード テスト**&ndash;アプリは、応答の目標を引き続き満たしながら特定のシナリオのユーザーの指定した負荷を処理できるかどうかをテストします。 アプリは、通常の状況で実行されます。

**ストレス テスト**&ndash;極端な場合は、長期間の多くの場合、実行するときに、アプリの安定性をテストします。 テストは、アプリの高いユーザー負荷、スパイクや徐々 に増加のロードのいずれかを配置またはアプリのコンピューティング リソースを制限します。

ストレス テストは、ストレス条件下でアプリがエラーを修復し、適切に想定される動作に戻るかどうかを決定します。 ストレス条件下でアプリを通常の条件下で実行されていません。

Visual Studio 2019 は、ロード テスト機能を使用して、最新の Visual Studio のバージョンです。 お客様のロード テスト ツールは、今後必要とする場合は、Apache JMeter、Akamai の CloudTest BlazeMeter などの別のツールを勧めします。 詳細については、次を参照してください。、 [Visual Studio 2019 のリリース ノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)します。

ロード テスト サービスを Azure DevOps では、2020年で終了します。 詳細については、次を参照してください。[クラウド ベースのロード テスト サービス終了](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)します。

## <a name="visual-studio-tools"></a>Visual Studio ツール

Visual Studio では、作成、開発、および web パフォーマンスとロード テストをデバッグすることができます。 オプションでは、web ブラウザーでアクションを記録することによってテストを作成します。

作成、構成、およびロード テストを Visual Studio 2017 を使用してプロジェクトを実行する方法については、次を参照してください。[クイック スタート。ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。 詳細については、次を参照してください。、[資料](#additional-resources)セクション。

Azure DevOps を使用してクラウドでオンプレミスまたは実行を実行するのには、ロード テストを構成できます。

## <a name="azure-devops"></a>Azure DevOps

使用してロード テストの実行を開始することができます、 [Azure DevOps テスト計画](/azure/devops/test/load-test/index?view=vsts)サービス。

![Azure DevOps のロード テストのランディング ページ](./load-tests/_static/azure-devops-load-test.png)

サービスには、次のテスト形式がサポートされています。

* Visual Studio &ndash; Web テストを Visual Studio で作成します。
* HTTP アーカイブ&ndash;アーカイブ内のキャプチャされた HTTP トラフィックがテスト中に再生されます。
* [URL ベース](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;テスト、要求の種類、ヘッダー、およびクエリ文字列を読み込む Url を指定できます。 実行時間などのパラメーターの設定を実行するには、ロード パターン、およびユーザーの数を構成できます。
* [Apache JMeter](https://jmeter.apache.org/)します。

## <a name="azure-portal"></a>Azure Portal

[Azure ポータルでは、設定して web アプリのロード テストを実行する](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)から直接、**パフォーマンス**Azure portal で App Service のタブ。

![Azure portal で azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

テストでは、指定した URL または複数の Url をテストできる、Visual Studio Web テスト ファイルで手動テストを指定できます。

![Azure portal で新しいパフォーマンス テスト ページ](./load-tests/_static/azure-appservice-perf-test-config.png)

テストの終わりは、生成されたレポートは、アプリのパフォーマンス特性を表示します。 統計の例は次のとおりです。

* 平均応答時間
* 最大スループット: 1 秒あたりの要求
* 失敗率

## <a name="third-party-tools"></a>サード パーティのツール

次の一覧には、さまざまな機能セットとサード パーティの web パフォーマンス ツールが含まれています。

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

## <a name="additional-resources"></a>その他の資料

* [ロード テストのブログ シリーズ](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
