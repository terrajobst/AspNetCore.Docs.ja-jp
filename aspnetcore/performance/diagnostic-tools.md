---
title: パフォーマンス診断ツール
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスの問題を診断するための便利なツールです。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: d273897b9ad26d57eb94b196b58f14019a96d07d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815623"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="15e09-103">パフォーマンスの診断ツール</span><span class="sxs-lookup"><span data-stu-id="15e09-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="15e09-104">によって[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="15e09-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="15e09-105">この記事では、ASP.NET Core でのパフォーマンスの問題を診断するためのツールを紹介します。</span><span class="sxs-lookup"><span data-stu-id="15e09-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="15e09-106">Visual Studio 診断ツール</span><span class="sxs-lookup"><span data-stu-id="15e09-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="15e09-107">[プロファイリングと診断ツール](/visualstudio/profiling)パフォーマンスの問題の調査を開始点としては、Visual Studio に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="15e09-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="15e09-108">これらのツールとは、強力で、Visual Studio 開発環境から使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="15e09-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="15e09-109">ツールは、ASP.NET Core アプリでの CPU 使用率、メモリ使用量とパフォーマンス イベントの分析を使用できます。</span><span class="sxs-lookup"><span data-stu-id="15e09-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="15e09-110">組み込みの中では開発時に簡単にプロファイルします。</span><span class="sxs-lookup"><span data-stu-id="15e09-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="15e09-111">詳細については[Visual Studio ドキュメント](/visualstudio/profiling/profiling-overview)します。</span><span class="sxs-lookup"><span data-stu-id="15e09-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="15e09-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="15e09-112">Application Insights</span></span>

<span data-ttu-id="15e09-113">[Application Insights](/azure/application-insights/app-insights-overview)アプリの詳細なパフォーマンス データを提供します。</span><span class="sxs-lookup"><span data-stu-id="15e09-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="15e09-114">Application Insights は、自動的に反応率、エラー率、依存関係の応答時間に関するデータを収集します。</span><span class="sxs-lookup"><span data-stu-id="15e09-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="15e09-115">Application Insights では、アプリにカスタム イベントとメトリックが特定のログ記録をサポートします。</span><span class="sxs-lookup"><span data-stu-id="15e09-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="15e09-116">Azure Application Insights では、監視対象のアプリで洞察を提供する複数の方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="15e09-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="15e09-117">[アプリケーション マップ](/azure/application-insights/app-insights-app-map)– 分散アプリのすべてのコンポーネント間でスポット パフォーマンスのボトルネックや障害ホット スポットに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="15e09-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="15e09-118">[Azure メトリックス エクスプ ローラー](/azure/azure-monitor/platform/metrics-getting-started)はプロット グラフ、傾向を視覚的に関連付けることができる Microsoft Azure ポータルのコンポーネントで急増し急減メトリック内の値を調査します。</span><span class="sxs-lookup"><span data-stu-id="15e09-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="15e09-119">[Application Insights ポータルの [パフォーマンス] ブレード](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="15e09-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="15e09-120">監視対象のアプリでは、さまざまな操作のパフォーマンスの詳細を示しています。</span><span class="sxs-lookup"><span data-stu-id="15e09-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="15e09-121">すべての部分と依存関係を長い期間に影響を確認する 1 つの操作へのドリル ダウンできます。</span><span class="sxs-lookup"><span data-stu-id="15e09-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="15e09-122">パフォーマンス トレースはオンデマンドで収集するには、ここでは、Profiler を起動できます。</span><span class="sxs-lookup"><span data-stu-id="15e09-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="15e09-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler)定期的かつオンデマンドでの .NET アプリのプロファイリングを使用します。</span><span class="sxs-lookup"><span data-stu-id="15e09-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="15e09-124">Azure ポータルの表示では、呼び出し履歴とホット パスのパフォーマンス トレースをキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="15e09-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="15e09-125">PerfView を使用して、詳細な分析のトレース ファイルをダウンロードすることもできます。</span><span class="sxs-lookup"><span data-stu-id="15e09-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="15e09-126">Application Insights は、さまざまな環境で使用できます。</span><span class="sxs-lookup"><span data-stu-id="15e09-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="15e09-127">Azure で動作するように最適化します。</span><span class="sxs-lookup"><span data-stu-id="15e09-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="15e09-128">運用、開発、およびステージング環境で機能します。</span><span class="sxs-lookup"><span data-stu-id="15e09-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="15e09-129">ローカルで動作[Visual Studio](/azure/application-insights/app-insights-visual-studio)または他のホスト環境にします。</span><span class="sxs-lookup"><span data-stu-id="15e09-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="15e09-130">詳細については、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15e09-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="15e09-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="15e09-131">PerfView</span></span>

<span data-ttu-id="15e09-132">[PerfView](https://github.com/Microsoft/perfview)は .NET のパフォーマンスの問題を診断するためには、具体的には、.NET チームによって作成されたパフォーマンス分析ツールです。</span><span class="sxs-lookup"><span data-stu-id="15e09-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="15e09-133">PerfView は、CPU 使用率、メモリおよび GC の動作、パフォーマンス イベント、およびウォール クロック時間の分析を使用できます。</span><span class="sxs-lookup"><span data-stu-id="15e09-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="15e09-134">PerfView および開始する方法の詳細については、 [PerfView のチュートリアル ビデオ](https://channel9.msdn.com/Series/PerfView-Tutorial)またはツールで使用可能なユーザー ガイドを参照してまたは[github](https://github.com/Microsoft/perfview)します。</span><span class="sxs-lookup"><span data-stu-id="15e09-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="15e09-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="15e09-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="15e09-136">[Windows パフォーマンス ツールキット](/windows-hardware/test/wpt/)(WPT) は、2 つのコンポーネントで構成されています。Windows Performance Recorder (WPR) と Windows パフォーマンス アナライザー (WPA)。</span><span class="sxs-lookup"><span data-stu-id="15e09-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="15e09-137">ツールは、Windows オペレーティング システムおよびアプリの詳細なパフォーマンス プロファイルを生成します。</span><span class="sxs-lookup"><span data-stu-id="15e09-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="15e09-138">WPT がデータを視覚化するための豊富な方法が、そのデータの収集は PerfView のよりも低い。</span><span class="sxs-lookup"><span data-stu-id="15e09-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="15e09-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="15e09-139">PerfCollect</span></span>

<span data-ttu-id="15e09-140">PerfView が .NET シナリオ用の便利なパフォーマンス分析ツールの中でしか実行できません Windows のため、これを使用して、Linux 環境で実行されている ASP.NET Core アプリからトレースを収集することはできません。</span><span class="sxs-lookup"><span data-stu-id="15e09-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="15e09-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)ネイティブ Linux のプロファイリング ツールを使用する bash スクリプトは、([Perf](https://perf.wiki.kernel.org/index.php/Main_Page)と[LTTng](https://lttng.org/)) PerfView で分析できる Linux 上のトレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="15e09-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="15e09-142">PerfCollect は、PerfView を直接使用することはできません、Linux 環境でパフォーマンスの問題を表示する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="15e09-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="15e09-143">代わりに、PerfCollect すると、PerfView を使用して Windows コンピューター上で、分析し、.NET Core アプリからトレースを収集できます。</span><span class="sxs-lookup"><span data-stu-id="15e09-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="15e09-144">インストールして PerfCollect の使用方法の詳細については、使用可能な[github](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)します。</span><span class="sxs-lookup"><span data-stu-id="15e09-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="15e09-145">その他のサード パーティ製のパフォーマンス ツール</span><span class="sxs-lookup"><span data-stu-id="15e09-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="15e09-146">次に、.NET Core アプリケーションのパフォーマンス調査に役立ついくつかのサードパーティ製のパフォーマンス ツールの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="15e09-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="15e09-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="15e09-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="15e09-148">dotTrace、dotMemory jetbrains</span><span class="sxs-lookup"><span data-stu-id="15e09-148">dotTrace and dotMemory from JetBrains</span></span>
- <span data-ttu-id="15e09-149">Intel から VTune</span><span class="sxs-lookup"><span data-stu-id="15e09-149">VTune from Intel</span></span>
