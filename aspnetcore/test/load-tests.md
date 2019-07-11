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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="2ce97-103">ASP.NET Core のロード/ストレス テスト</span><span class="sxs-lookup"><span data-stu-id="2ce97-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="2ce97-104">ロード テストとストレス テストは、web アプリが高パフォーマンスであることを確認する重要な拡張性の高いです。</span><span class="sxs-lookup"><span data-stu-id="2ce97-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="2ce97-105">多くの場合、類似のテストを共有する場合でも、その目標が異なります。</span><span class="sxs-lookup"><span data-stu-id="2ce97-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="2ce97-106">**ロード テスト**&ndash;アプリは、応答の目標を引き続き満たしながら特定のシナリオのユーザーの指定した負荷を処理できるかどうかをテストします。</span><span class="sxs-lookup"><span data-stu-id="2ce97-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="2ce97-107">アプリは、通常の状況で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="2ce97-108">**ストレス テスト**&ndash;極端な場合は、長期間の多くの場合、実行するときに、アプリの安定性をテストします。</span><span class="sxs-lookup"><span data-stu-id="2ce97-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="2ce97-109">テストは、アプリの高いユーザー負荷、スパイクや徐々 に増加のロードのいずれかを配置またはアプリのコンピューティング リソースを制限します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="2ce97-110">ストレス テストは、ストレス条件下でアプリがエラーを修復し、適切に想定される動作に戻るかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="2ce97-111">ストレス条件下でアプリを通常の条件下で実行されていません。</span><span class="sxs-lookup"><span data-stu-id="2ce97-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="2ce97-112">Visual Studio 2019 は、ロード テスト機能を使用して、最新の Visual Studio のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="2ce97-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="2ce97-113">お客様のロード テスト ツールは、今後必要とする場合は、Apache JMeter、Akamai の CloudTest BlazeMeter などの別のツールを勧めします。</span><span class="sxs-lookup"><span data-stu-id="2ce97-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="2ce97-114">詳細については、次を参照してください。、 [Visual Studio 2019 のリリース ノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="2ce97-115">ロード テスト サービスを Azure DevOps では、2020年で終了します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="2ce97-116">詳細については、次を参照してください。[クラウド ベースのロード テスト サービス終了](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="2ce97-117">Visual Studio ツール</span><span class="sxs-lookup"><span data-stu-id="2ce97-117">Visual Studio tools</span></span>

<span data-ttu-id="2ce97-118">Visual Studio では、作成、開発、および web パフォーマンスとロード テストをデバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="2ce97-119">オプションでは、web ブラウザーでアクションを記録することによってテストを作成します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="2ce97-120">作成、構成、およびロード テストを Visual Studio 2017 を使用してプロジェクトを実行する方法については、次を参照してください。[クイック スタート。ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2ce97-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span> <span data-ttu-id="2ce97-121">詳細については、次を参照してください。、[資料](#additional-resources)セクション。</span><span class="sxs-lookup"><span data-stu-id="2ce97-121">For more information, see the [Additional resources](#additional-resources) section.</span></span>

<span data-ttu-id="2ce97-122">Azure DevOps を使用してクラウドでオンプレミスまたは実行を実行するのには、ロード テストを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-122">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="2ce97-123">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2ce97-123">Azure DevOps</span></span>

<span data-ttu-id="2ce97-124">使用してロード テストの実行を開始することができます、 [Azure DevOps テスト計画](/azure/devops/test/load-test/index?view=vsts)サービス。</span><span class="sxs-lookup"><span data-stu-id="2ce97-124">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps のロード テストのランディング ページ](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="2ce97-126">サービスには、次のテスト形式がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2ce97-126">The service supports the following test formats:</span></span>

* <span data-ttu-id="2ce97-127">Visual Studio &ndash; Web テストを Visual Studio で作成します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-127">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="2ce97-128">HTTP アーカイブ&ndash;アーカイブ内のキャプチャされた HTTP トラフィックがテスト中に再生されます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-128">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="2ce97-129">[URL ベース](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;テスト、要求の種類、ヘッダー、およびクエリ文字列を読み込む Url を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-129">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="2ce97-130">実行時間などのパラメーターの設定を実行するには、ロード パターン、およびユーザーの数を構成できます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-130">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="2ce97-131">[Apache JMeter](https://jmeter.apache.org/)します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-131">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="2ce97-132">Azure Portal</span><span class="sxs-lookup"><span data-stu-id="2ce97-132">Azure portal</span></span>

<span data-ttu-id="2ce97-133">[Azure ポータルでは、設定して web アプリのロード テストを実行する](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)から直接、**パフォーマンス**Azure portal で App Service のタブ。</span><span class="sxs-lookup"><span data-stu-id="2ce97-133">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure portal で azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="2ce97-135">テストでは、指定した URL または複数の Url をテストできる、Visual Studio Web テスト ファイルで手動テストを指定できます。</span><span class="sxs-lookup"><span data-stu-id="2ce97-135">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Azure portal で新しいパフォーマンス テスト ページ](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="2ce97-137">テストの終わりは、生成されたレポートは、アプリのパフォーマンス特性を表示します。</span><span class="sxs-lookup"><span data-stu-id="2ce97-137">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="2ce97-138">統計の例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2ce97-138">Example statistics include:</span></span>

* <span data-ttu-id="2ce97-139">平均応答時間</span><span class="sxs-lookup"><span data-stu-id="2ce97-139">Average response time</span></span>
* <span data-ttu-id="2ce97-140">最大スループット: 1 秒あたりの要求</span><span class="sxs-lookup"><span data-stu-id="2ce97-140">Max throughput: requests per second</span></span>
* <span data-ttu-id="2ce97-141">失敗率</span><span class="sxs-lookup"><span data-stu-id="2ce97-141">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="2ce97-142">サード パーティのツール</span><span class="sxs-lookup"><span data-stu-id="2ce97-142">Third-party tools</span></span>

<span data-ttu-id="2ce97-143">次の一覧には、さまざまな機能セットとサード パーティの web パフォーマンス ツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2ce97-143">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="2ce97-144">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="2ce97-144">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="2ce97-145">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="2ce97-145">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="2ce97-146">ガットリング</span><span class="sxs-lookup"><span data-stu-id="2ce97-146">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="2ce97-147">上機嫌</span><span class="sxs-lookup"><span data-stu-id="2ce97-147">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="2ce97-148">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="2ce97-148">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="2ce97-149">Netling</span><span class="sxs-lookup"><span data-stu-id="2ce97-149">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="2ce97-150">Vegeta</span><span class="sxs-lookup"><span data-stu-id="2ce97-150">Vegeta</span></span>](https://github.com/tsenart/vegeta)

## <a name="additional-resources"></a><span data-ttu-id="2ce97-151">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2ce97-151">Additional resources</span></span>

* [<span data-ttu-id="2ce97-152">ロード テストのブログ シリーズ</span><span class="sxs-lookup"><span data-stu-id="2ce97-152">Load Test blog series</span></span>](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
