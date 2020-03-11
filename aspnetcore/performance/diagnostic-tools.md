---
title: パフォーマンス診断ツール
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスの問題を診断するための便利なツール。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: d273897b9ad26d57eb94b196b58f14019a96d07d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652472"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="9fe6a-103">パフォーマンス診断ツール</span><span class="sxs-lookup"><span data-stu-id="9fe6a-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="9fe6a-104">作成者: [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="9fe6a-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="9fe6a-105">この記事では、ASP.NET Core のパフォーマンスの問題を診断するためのツールの一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="9fe6a-106">Visual Studio 診断ツール</span><span class="sxs-lookup"><span data-stu-id="9fe6a-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="9fe6a-107">Visual Studio に組み込まれている[プロファイリングツールと診断ツール](/visualstudio/profiling)は、パフォーマンスの問題の調査を開始するのに適した場所です。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="9fe6a-108">これらのツールは、Visual Studio 開発環境から使用するのに強力で便利です。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="9fe6a-109">ツールを使用すると、ASP.NET Core アプリの CPU 使用率、メモリ使用量、パフォーマンスイベントを分析できます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="9fe6a-110">組み込まれているので、開発時にプロファイリングを簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="9fe6a-111">詳細については、 [Visual Studio のドキュメント](/visualstudio/profiling/profiling-overview)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="9fe6a-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="9fe6a-112">Application Insights</span></span>

<span data-ttu-id="9fe6a-113">[Application Insights](/azure/application-insights/app-insights-overview)は、アプリの詳細なパフォーマンスデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="9fe6a-114">Application Insights は、応答率、エラー率、依存関係の応答時間などのデータを自動的に収集します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="9fe6a-115">Application Insights は、アプリに固有のカスタムイベントとメトリックのログ記録をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="9fe6a-116">Azure アプリケーション Insights には、監視対象のアプリに関する洞察を提供する複数の方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="9fe6a-117">[アプリケーションマップ](/azure/application-insights/app-insights-app-map)–分散アプリのすべてのコンポーネントで、パフォーマンスのボトルネックや障害のホットスポットを見つけるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="9fe6a-118">[Azure メトリックスエクスプローラー](/azure/azure-monitor/platform/metrics-getting-started)は、グラフのプロット、傾向の視覚的な関連付け、およびメトリックの値におけるスパイクと dip の調査を可能にする、Microsoft Azure portal のコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="9fe6a-119">[Application Insights ポータルのパフォーマンスブレード](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="9fe6a-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="9fe6a-120">監視対象のアプリのさまざまな操作のパフォーマンスの詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="9fe6a-121">1つの操作をドリルダウンして、長期間に寄与するすべてのパーツ/依存関係を確認できます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="9fe6a-122">Profiler をここから呼び出して、必要に応じてパフォーマンストレースを収集できます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="9fe6a-123">[Azure アプリケーション Insights Profiler](/azure/azure-monitor/app/profiler)を使用すると、.net アプリの通常のオンデマンドプロファイリングを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="9fe6a-124">Azure portal は、呼び出し履歴とホットパスを使用してキャプチャされたパフォーマンストレースを表示します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="9fe6a-125">また、PerfView を使用して詳細な分析を行うために、トレースファイルをダウンロードすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="9fe6a-126">Application Insights は、さまざまな環境で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="9fe6a-127">Azure で動作するように最適化されています。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="9fe6a-128">運用、開発、およびステージングで機能します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="9fe6a-129">[Visual Studio](/azure/application-insights/app-insights-visual-studio)または他のホスト環境からローカルで動作します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="9fe6a-130">詳細については、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="9fe6a-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="9fe6a-131">PerfView</span></span>

<span data-ttu-id="9fe6a-132">[Perfview](https://github.com/Microsoft/perfview)は .net チームによって作成されたパフォーマンス分析ツールで、.net パフォーマンスの問題を診断するためのものです。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="9fe6a-133">PerfView を使用すると、CPU の使用率、メモリと GC の動作、パフォーマンスイベント、およびウォールクロック時間を分析できます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="9fe6a-134">PerfView の詳細と、 [Perfview のビデオチュートリアル](https://channel9.msdn.com/Series/PerfView-Tutorial)を開始する方法、またはツールまたは[GitHub](https://github.com/Microsoft/perfview)で利用可能なユーザーガイドを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="9fe6a-135">Windows パフォーマンスツールキット</span><span class="sxs-lookup"><span data-stu-id="9fe6a-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="9fe6a-136">[Windows パフォーマンスツールキット](/windows-hardware/test/wpt/)(WPT) は、Windows パフォーマンスレコーダー (wpr) と Windows performance ANALYZER (WPA) の2つのコンポーネントで構成されています。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="9fe6a-137">これらのツールは、Windows オペレーティングシステムとアプリのパフォーマンスプロファイルを詳細に生成します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="9fe6a-138">WPT には、データを視覚化するための豊富な方法が用意されていますが、そのデータの収集は PerfView のより強力ではありません。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="9fe6a-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="9fe6a-139">PerfCollect</span></span>

<span data-ttu-id="9fe6a-140">PerfView は .NET シナリオ用の便利なパフォーマンス分析ツールですが、Windows 上でのみ実行されるため、Linux 環境で実行されている ASP.NET Core アプリからトレースを収集するために使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="9fe6a-141">[Perfcollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)は、ネイティブ linux プロファイリングツール ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page)および[LTTng](https://lttng.org/)) を使用して、perfcollect で分析できる linux 上のトレースを収集する bash スクリプトです。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="9fe6a-142">PerfCollect は、Perfcollect を直接使用できない Linux 環境でパフォーマンスの問題が発生した場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="9fe6a-143">代わりに、PerfCollect で .NET Core アプリからトレースを収集し、Perfcollect を使用して Windows コンピューターで分析することができます。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="9fe6a-144">PerfCollect をインストールして開始する方法の詳細については、 [GitHub を](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="9fe6a-145">その他のサードパーティ製のパフォーマンスツール</span><span class="sxs-lookup"><span data-stu-id="9fe6a-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="9fe6a-146">次に、.NET Core アプリケーションのパフォーマンス調査に役立つサードパーティ製のパフォーマンスツールをいくつか示します。</span><span class="sxs-lookup"><span data-stu-id="9fe6a-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="9fe6a-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="9fe6a-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="9fe6a-148">JetBrains からの dotTrace と Dottrace</span><span class="sxs-lookup"><span data-stu-id="9fe6a-148">dotTrace and dotMemory from JetBrains</span></span>
- <span data-ttu-id="9fe6a-149">Intel からの VTune</span><span class="sxs-lookup"><span data-stu-id="9fe6a-149">VTune from Intel</span></span>
