---
title: ASP.NET Core の負荷/ストレステスト
author: Jeremy-Meng
description: ロードテストと ASP.NET Core アプリのストレステストを行うための、いくつかの注目すべきツールとアプローチについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: cb6015f737b6a93127016ab0f21b58e34b624ff3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "77004293"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="0406b-103">ASP.NET Core の負荷/ストレステスト</span><span class="sxs-lookup"><span data-stu-id="0406b-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="0406b-104">ロードテストとストレステストは、web アプリのパフォーマンスと拡張性を確保するために重要です。</span><span class="sxs-lookup"><span data-stu-id="0406b-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="0406b-105">これらの目標は、よく似たテストを共有している場合でも異なります。</span><span class="sxs-lookup"><span data-stu-id="0406b-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="0406b-106">**ロードテスト** – 応答の目標値を満たしながら、特定のシナリオでアプリが指定された負荷のユーザーを処理できるかどうかをテストします。</span><span class="sxs-lookup"><span data-stu-id="0406b-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="0406b-107">アプリは通常の状態で実行します。</span><span class="sxs-lookup"><span data-stu-id="0406b-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="0406b-108">**ストレステスト** &ndash; 多くの場合、長時間、極端な条件下でアプリの安定性をテストします。</span><span class="sxs-lookup"><span data-stu-id="0406b-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="0406b-109">テストでは、アプリの負荷を急増させたり徐々に増加させるようにユーザーの負荷を高くします。または、アプリのコンピューティングリソースを制限します。</span><span class="sxs-lookup"><span data-stu-id="0406b-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="0406b-110">ストレステストは、ストレスのかかったアプリが障害から回復し、正常に期待通りの動作に戻ることができるかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="0406b-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="0406b-111">ストレスがかかったアプリは、通常の状態で実行されません。</span><span class="sxs-lookup"><span data-stu-id="0406b-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="0406b-112">Visual studio 2019 は、ロードテスト機能を備えた最新バージョンの Visual Studio です。</span><span class="sxs-lookup"><span data-stu-id="0406b-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="0406b-113">今後ロードテストツールが必要なお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの別のツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0406b-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="0406b-114">詳細については、「 [Visual Studio 2019 リリースノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0406b-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="0406b-115">Visual Studio ツール</span><span class="sxs-lookup"><span data-stu-id="0406b-115">Visual Studio tools</span></span>

<span data-ttu-id="0406b-116">ユーザーは Visual Studio を使用して、web パフォーマンステストとロードテストを作成、開発、およびデバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="0406b-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="0406b-117">Web ブラウザーでアクションを記録することによって、テストを作成するためのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0406b-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="0406b-118">Visual Studio 2017 を使用してロードテストプロジェクトを作成、構成、および実行する方法については、「[クイックスタート: ロードテストプロジェクトを作成](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0406b-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="0406b-119">ロードテストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="0406b-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="0406b-120">サードパーティ製のツール</span><span class="sxs-lookup"><span data-stu-id="0406b-120">Third-party tools</span></span>

<span data-ttu-id="0406b-121">次の一覧には、さまざまな機能セットを備えたサードパーティの web パフォーマンスツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0406b-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="0406b-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="0406b-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="0406b-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="0406b-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="0406b-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="0406b-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="0406b-125">k6</span><span class="sxs-lookup"><span data-stu-id="0406b-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="0406b-126">Locust</span><span class="sxs-lookup"><span data-stu-id="0406b-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="0406b-127">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="0406b-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="0406b-128">Netling</span><span class="sxs-lookup"><span data-stu-id="0406b-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="0406b-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="0406b-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

