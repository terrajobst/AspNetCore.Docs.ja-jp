---
title: ASP.NET Core のロード テスト/ストレス テスト
author: Jeremy-Meng
description: ASP.NET Core アプリのロード テストとストレス テストを行うために、いくつかの注目すべきツールとアプローチについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649640"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core のロード テスト/ストレス テスト

ロード テストとストレス テストは、Web アプリのパフォーマンスと拡張性を確保するために重要です。 これらの目標は、よく似たテストを共有する場合でも異なります。

**ロード テスト** &ndash; アプリが応答の目標を満たしつつ、特定のシナリオで特定のユーザーの負荷を処理することができるかをテストします。 アプリは通常の状態で実行されます。

**ストレス テスト** &ndash; 極端な条件でのアプリの安定性をテストします。多くの場合は、長い時間にわたるテストを行います。 このテストでは、急激な負荷または徐々に増加する負荷によって、アプリに高いユーザー負荷をかけます。または、アプリのコンピューティング リソースを制限します。

ストレス テストでは、負荷がかかっているアプリが障害から復旧し、期待される動作に適切に戻ることができるかどうかを判断します。 ストレスをかけるアプリは、通常の状況では実行されません。

Visual Studio 2019 は、ロード テスト機能を備えた最後のバージョンの Visual Studio です。 今後、ロード テスト ツールを必要とするお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの代替のツールを推奨します。 詳細については、「[Visual Studio 2019 リリース ノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。

## <a name="visual-studio-tools"></a>Visual Studio ツール

ユーザーは、Visual Studio を使用して Web パフォーマンス テストおよびロード テストを作成、開発、およびデバッグできます。 Web ブラウザーでアクションを記録してテストを作成するオプションを使用できます。

Visual Studio 2017 を使用してロード テスト プロジェクトを作成、構成、および実行する方法については、「[クイックスタート: ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。

ロード テストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。

## <a name="third-party-tools"></a>サードパーティ製のツール

次の一覧には、さまざまな機能セットを備えたサードパーティ製の Web パフォーマンス ツールが含まれています。

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

