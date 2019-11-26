---
title: ASP.NET Core の負荷/ストレステスト
author: Jeremy-Meng
description: ロードテストと ASP.NET Core アプリのストレステストを行うための、いくつかの注目すべきツールとアプローチについて説明します。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289023"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="9f13d-103">ASP.NET Core の負荷/ストレステスト</span><span class="sxs-lookup"><span data-stu-id="9f13d-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="9f13d-104">ロードテストとストレステストは、web アプリのパフォーマンスと拡張性を確保するために重要です。</span><span class="sxs-lookup"><span data-stu-id="9f13d-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9f13d-105">これらの目標は、よく似たテストを共有している場合でも異なります。</span><span class="sxs-lookup"><span data-stu-id="9f13d-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="9f13d-106">**ロードテスト**&ndash;、アプリが特定のシナリオで特定のユーザーの負荷を処理できるかどうかをテストしながら、応答の目標を満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="9f13d-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9f13d-107">アプリは通常の状態で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9f13d-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9f13d-108">**ストレステスト**は、非常に長い時間で頻繁に実行される場合に、アプリケーションの安定性をテスト &ndash; ます。</span><span class="sxs-lookup"><span data-stu-id="9f13d-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="9f13d-109">テストでは、アプリで負荷が急増または徐々に増加しているか、アプリのコンピューティングリソースが制限されているため、ユーザー負荷が高くなります。</span><span class="sxs-lookup"><span data-stu-id="9f13d-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="9f13d-110">ストレステストでは、負荷がかかっているアプリが障害から復旧できるかどうかを判断し、期待される動作に適切に戻ります。</span><span class="sxs-lookup"><span data-stu-id="9f13d-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="9f13d-111">ストレス下では、アプリは通常の状況では実行されません。</span><span class="sxs-lookup"><span data-stu-id="9f13d-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="9f13d-112">Visual studio 2019 は、ロードテスト機能を備えた最新バージョンの Visual Studio です。</span><span class="sxs-lookup"><span data-stu-id="9f13d-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="9f13d-113">今後ロードテストツールが必要なお客様には、Apache JMeter、Akamai CloudTest、BlazeMeter などの別のツールをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9f13d-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="9f13d-114">詳細については、「 [Visual Studio 2019 リリースノート](/visualstudio/releases/2019/release-notes-v16.0#test-tools)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9f13d-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9f13d-115">Visual Studio ツール</span><span class="sxs-lookup"><span data-stu-id="9f13d-115">Visual Studio tools</span></span>

<span data-ttu-id="9f13d-116">ユーザーは Visual Studio を使用して、web パフォーマンステストとロードテストを作成、開発、およびデバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="9f13d-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9f13d-117">Web ブラウザーでアクションを記録することによって、テストを作成するためのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9f13d-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="9f13d-118">Visual Studio 2017 を使用してロードテストプロジェクトを作成、構成、および実行する方法については、「[クイックスタート: ロードテストプロジェクトを作成](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9f13d-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="9f13d-119">ロードテストは、オンプレミスで実行するように構成することも、Azure DevOps を使用してクラウドで実行するように構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="9f13d-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9f13d-120">サードパーティ製のツール</span><span class="sxs-lookup"><span data-stu-id="9f13d-120">Third-party tools</span></span>

<span data-ttu-id="9f13d-121">次の一覧には、さまざまな機能セットを備えたサードパーティの web パフォーマンスツールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9f13d-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="9f13d-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="9f13d-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="9f13d-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="9f13d-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="9f13d-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="9f13d-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="9f13d-125">Locust</span><span class="sxs-lookup"><span data-stu-id="9f13d-125">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="9f13d-126">西風の WebSurge</span><span class="sxs-lookup"><span data-stu-id="9f13d-126">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="9f13d-127">Netling</span><span class="sxs-lookup"><span data-stu-id="9f13d-127">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="9f13d-128">Vegeta</span><span class="sxs-lookup"><span data-stu-id="9f13d-128">Vegeta</span></span>](https://github.com/tsenart/vegeta)
