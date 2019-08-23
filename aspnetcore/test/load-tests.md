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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="498f0-103">ASP.NET Core の負荷/ストレステスト</span><span class="sxs-lookup"><span data-stu-id="498f0-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="498f0-104">ロードテストとストレステストは、web アプリのパフォーマンスと拡張性を確保するために重要です。</span><span class="sxs-lookup"><span data-stu-id="498f0-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="498f0-105">これらの目標は、よく似たテストを共有している場合でも異なります。</span><span class="sxs-lookup"><span data-stu-id="498f0-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="498f0-106">**ロードテスト**&ndash;アプリが特定のシナリオで特定のユーザーの負荷を処理できるかどうかをテストしながら、応答の目標を満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="498f0-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="498f0-107">アプリは通常の状態で実行されます。</span><span class="sxs-lookup"><span data-stu-id="498f0-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="498f0-108">**ストレステスト**&ndash;非常に長い時間で頻繁に実行する場合は、アプリの安定性をテストします。</span><span class="sxs-lookup"><span data-stu-id="498f0-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="498f0-109">テストでは、アプリで負荷が急増または徐々に増加しているか、アプリのコンピューティングリソースが制限されているため、ユーザー負荷が高くなります。</span><span class="sxs-lookup"><span data-stu-id="498f0-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="498f0-110">ストレステストでは、負荷がかかっているアプリが障害から復旧できるかどうかを判断し、期待される動作に適切に戻ります。</span><span class="sxs-lookup"><span data-stu-id="498f0-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="498f0-111">ストレス下では、アプリは通常の状況では実行されません。</span><span class="sxs-lookup"><span data-stu-id="498f0-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="498f0-112">Visual studio 2019 は、ロードテスト機能を備えた最新バージョンの Visual Studio です。</span><span class="sxs-lookup"><span data-stu-id="498f0-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="498f0-113">今後ロードテストツールが必要なお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの別のツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="498f0-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="498f0-114">詳細については、「 [Visual Studio 2019 リリースノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="498f0-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="498f0-115">Azure DevOps のロードテストサービスは、2020で終了します。</span><span class="sxs-lookup"><span data-stu-id="498f0-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="498f0-116">詳細については、「[クラウドベースのロードテストサービスの終了](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="498f0-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="498f0-117">Visual Studio ツール</span><span class="sxs-lookup"><span data-stu-id="498f0-117">Visual Studio tools</span></span>

<span data-ttu-id="498f0-118">ユーザーは Visual Studio を使用して、web パフォーマンステストとロードテストを作成、開発、およびデバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="498f0-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="498f0-119">Web ブラウザーでアクションを記録することによって、テストを作成するためのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="498f0-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="498f0-120">Visual Studio 2017 を使用してロードテストプロジェクトを作成、構成、および実行する方法につい[ては、「クイックスタート:ロード テスト プロジェクトを作成する](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="498f0-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="498f0-121">ロードテストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="498f0-121">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="498f0-122">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="498f0-122">Azure DevOps</span></span>

<span data-ttu-id="498f0-123">ロードテストの実行は、 [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts)サービスを使用して開始できます。</span><span class="sxs-lookup"><span data-stu-id="498f0-123">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps のロードテストのランディングページ](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="498f0-125">サービスは、次のテスト形式をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="498f0-125">The service supports the following test formats:</span></span>

* <span data-ttu-id="498f0-126">Visual studio &ndash;で作成された visual studio Web テスト。</span><span class="sxs-lookup"><span data-stu-id="498f0-126">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="498f0-127">アーカイブ内&ndash;の http アーカイブでキャプチャされた http トラフィックは、テスト中に再生されます。</span><span class="sxs-lookup"><span data-stu-id="498f0-127">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="498f0-128">[URL ベース](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;ロードテスト、要求の種類、ヘッダー、およびクエリ文字列への url を指定できます。</span><span class="sxs-lookup"><span data-stu-id="498f0-128">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="498f0-129">実行設定パラメーター (期間、ロードパターン、ユーザー数など) を構成できます。</span><span class="sxs-lookup"><span data-stu-id="498f0-129">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="498f0-130">[Apache JMeter](https://jmeter.apache.org/)。</span><span class="sxs-lookup"><span data-stu-id="498f0-130">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="498f0-131">Azure Portal</span><span class="sxs-lookup"><span data-stu-id="498f0-131">Azure portal</span></span>

<span data-ttu-id="498f0-132">Azure portal を使用すると、Azure portal の App Service の **[パフォーマンス]** タブから[web アプリのロードテストを直接設定して実行でき](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)ます。</span><span class="sxs-lookup"><span data-stu-id="498f0-132">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure portal の Azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="498f0-134">テストは、指定された URL または Visual Studio Web テストファイルを使用した手動テストにすることができます。これにより、複数の Url をテストできます。</span><span class="sxs-lookup"><span data-stu-id="498f0-134">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Azure portal の新しいパフォーマンステストページ](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="498f0-136">テストの最後に、生成されたレポートには、アプリのパフォーマンス特性が表示されます。</span><span class="sxs-lookup"><span data-stu-id="498f0-136">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="498f0-137">統計の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="498f0-137">Example statistics include:</span></span>

* <span data-ttu-id="498f0-138">平均応答時間</span><span class="sxs-lookup"><span data-stu-id="498f0-138">Average response time</span></span>
* <span data-ttu-id="498f0-139">最大スループット: 1 秒あたりの要求数</span><span class="sxs-lookup"><span data-stu-id="498f0-139">Max throughput: requests per second</span></span>
* <span data-ttu-id="498f0-140">失敗率</span><span class="sxs-lookup"><span data-stu-id="498f0-140">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="498f0-141">サードパーティ製のツール</span><span class="sxs-lookup"><span data-stu-id="498f0-141">Third-party tools</span></span>

<span data-ttu-id="498f0-142">次の一覧には、さまざまな機能セットを備えたサードパーティの web パフォーマンスツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="498f0-142">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="498f0-143">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="498f0-143">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="498f0-144">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="498f0-144">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="498f0-145">Gatling</span><span class="sxs-lookup"><span data-stu-id="498f0-145">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="498f0-146">Locust</span><span class="sxs-lookup"><span data-stu-id="498f0-146">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="498f0-147">西風の WebSurge</span><span class="sxs-lookup"><span data-stu-id="498f0-147">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="498f0-148">Netling</span><span class="sxs-lookup"><span data-stu-id="498f0-148">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="498f0-149">Vegeta</span><span class="sxs-lookup"><span data-stu-id="498f0-149">Vegeta</span></span>](https://github.com/tsenart/vegeta)
