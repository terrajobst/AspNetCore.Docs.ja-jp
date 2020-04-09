---
title: ASP.NETコア パフォーマンスのベスト プラクティス
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスを向上させ、一般的なパフォーマンスの問題を回避するためのヒント。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977185"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="1a750-103">ASP.NETコア パフォーマンスのベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="1a750-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="1a750-104">作成者: [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="1a750-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="1a750-105">この記事では、ASP.NET Core のパフォーマンスのベスト プラクティスに関するガイドラインを提供します。</span><span class="sxs-lookup"><span data-stu-id="1a750-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="1a750-106">積極的にキャッシュ</span><span class="sxs-lookup"><span data-stu-id="1a750-106">Cache aggressively</span></span>

<span data-ttu-id="1a750-107">キャッシュについては、このドキュメントのいくつかの部分で説明します。</span><span class="sxs-lookup"><span data-stu-id="1a750-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="1a750-108">詳細については、「<xref:performance/caching/response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="1a750-109">ホット コード パスを理解する</span><span class="sxs-lookup"><span data-stu-id="1a750-109">Understand hot code paths</span></span>

<span data-ttu-id="1a750-110">このドキュメントでは、*ホット コード パス*は、頻繁に呼び出されるコード パスとして定義され、実行時間の多くが発生する場所です。</span><span class="sxs-lookup"><span data-stu-id="1a750-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="1a750-111">通常、ホット コード パスはアプリのスケールアウトとパフォーマンスを制限します。</span><span class="sxs-lookup"><span data-stu-id="1a750-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="1a750-112">通話のブロックを避ける</span><span class="sxs-lookup"><span data-stu-id="1a750-112">Avoid blocking calls</span></span>

<span data-ttu-id="1a750-113">core アプリASP.NET、多数の要求を同時に処理するように設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="1a750-114">非同期 API を使用すると、スレッドの小さなプールが、ブロッキング呼び出しを待機しないことによって数千の同時要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="1a750-115">実行時間の長い同期タスクの完了を待機するのではなく、スレッドは別の要求で動作できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="1a750-116">ASP.NET Core アプリでの一般的なパフォーマンスの問題は、非同期である可能性のある呼び出しをブロックすることです。</span><span class="sxs-lookup"><span data-stu-id="1a750-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="1a750-117">多くの同期ブロッキング呼び出しは[、スレッドプールの枯不足](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)と応答時間の低下につながります。</span><span class="sxs-lookup"><span data-stu-id="1a750-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="1a750-118">**しないでください**:</span><span class="sxs-lookup"><span data-stu-id="1a750-118">**Do not**:</span></span>

* <span data-ttu-id="1a750-119">[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)または[Task.Result](/dotnet/api/system.threading.tasks.task-1.result)を呼び出して非同期実行をブロックします。</span><span class="sxs-lookup"><span data-stu-id="1a750-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="1a750-120">共通コード パスのロックを取得します。</span><span class="sxs-lookup"><span data-stu-id="1a750-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="1a750-121">ASP.NET Core アプリは、コードを並列に実行するように設計された場合に最もパフォーマンスが高くなります。</span><span class="sxs-lookup"><span data-stu-id="1a750-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>
* <span data-ttu-id="1a750-122">[Task.Run を](/dotnet/api/system.threading.tasks.task.run)呼び出し、すぐにそれを待ちます。</span><span class="sxs-lookup"><span data-stu-id="1a750-122">Call [Task.Run](/dotnet/api/system.threading.tasks.task.run) and immediately await it.</span></span> <span data-ttu-id="1a750-123">ASP.NET Core は既に通常のスレッド プール スレッドでアプリ コードを実行しているので、Task.Run を呼び出すと余分な不要なスレッド プールのスケジュールが作成されるだけです。</span><span class="sxs-lookup"><span data-stu-id="1a750-123">ASP.NET Core already runs app code on normal Thread Pool threads, so calling Task.Run only results in extra unnecessary Thread Pool scheduling.</span></span> <span data-ttu-id="1a750-124">スケジュールされたコードがスレッドをブロックする場合でも、Task.Run は、それを防止しません。</span><span class="sxs-lookup"><span data-stu-id="1a750-124">Even if the scheduled code would block a thread, Task.Run does not prevent that.</span></span>

<span data-ttu-id="1a750-125">**行う**:</span><span class="sxs-lookup"><span data-stu-id="1a750-125">**Do**:</span></span>

* <span data-ttu-id="1a750-126">[ホット コード パス](#understand-hot-code-paths)を非同期にします。</span><span class="sxs-lookup"><span data-stu-id="1a750-126">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="1a750-127">非同期 API が使用可能な場合は、データ アクセス、I/O、および長時間実行の操作 API を非同期に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1a750-127">Call data access, I/O, and long-running operations APIs asynchronously if an asynchronous API is available.</span></span> <span data-ttu-id="1a750-128">同期 API を非同期にする場合は[、Task.Run](/dotnet/api/system.threading.tasks.task.run)を使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-128">Do **not** use [Task.Run](/dotnet/api/system.threading.tasks.task.run) to make a synchronus API asynchronous.</span></span>
* <span data-ttu-id="1a750-129">コントローラー/Razor ページ アクションを非同期にします。</span><span class="sxs-lookup"><span data-stu-id="1a750-129">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="1a750-130">[非同期/待機](/dotnet/csharp/programming-guide/concepts/async/)パターンの恩恵を受けるために、呼び出し履歴全体が非同期です。</span><span class="sxs-lookup"><span data-stu-id="1a750-130">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="1a750-131">[PerfView](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、スレッド プールに頻繁に追加される[スレッド](/windows/desktop/procthread/thread-pools)を検索できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-131">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="1a750-132">この`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`イベントは、スレッド プールに追加されたスレッドを示します。</span><span class="sxs-lookup"><span data-stu-id="1a750-132">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="1a750-133">大きなオブジェクトの割り当てを最小限に抑える</span><span class="sxs-lookup"><span data-stu-id="1a750-133">Minimize large object allocations</span></span>

<span data-ttu-id="1a750-134">[NET Core ガベージ コレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリでメモリの割り当てと解放を自動的に管理します。</span><span class="sxs-lookup"><span data-stu-id="1a750-134">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="1a750-135">自動ガベージ コレクションは、一般に、開発者がメモリが解放される方法やタイミングを心配する必要がならないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="1a750-135">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="1a750-136">ただし、参照されていないオブジェクトのクリーンアップには CPU 時間がかかるため、開発者は[ホット コード パス](#understand-hot-code-paths)でのオブジェクトの割り当てを最小限に抑える必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-136">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="1a750-137">ガベージ コレクションは、大きなオブジェクト (> 85 Kb) では特に負荷が高くなります。</span><span class="sxs-lookup"><span data-stu-id="1a750-137">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="1a750-138">ラージ オブジェクトは[ラージ オブジェクト ヒープ](/dotnet/standard/garbage-collection/large-object-heap)に格納され、クリーンアップには完全な (ジェネレーション 2) ガベージ コレクションが必要です。</span><span class="sxs-lookup"><span data-stu-id="1a750-138">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="1a750-139">ジェネレーション 0 およびジェネレーション 1 コレクションとは異なり、ジェネレーション 2 コレクションでは、アプリの実行を一時的に中断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-139">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="1a750-140">大きなオブジェクトの頻繁な割り当てと割り当て解除は、パフォーマンスの一貫性に欠けます。</span><span class="sxs-lookup"><span data-stu-id="1a750-140">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="1a750-141">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-141">Recommendations:</span></span>

* <span data-ttu-id="1a750-142">頻繁に使用される大きなオブジェクトをキャッシュすることを検討**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-142">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="1a750-143">大きなオブジェクトをキャッシュすると、負荷の高い割り当てを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="1a750-143">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="1a750-144">大規模な配列を格納するために[、ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1)を使用して、プール・バッファーを**実行**します。</span><span class="sxs-lookup"><span data-stu-id="1a750-144">**Do** pool buffers by using an [ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="1a750-145">[ホット コード パス](#understand-hot-code-paths)に、短時間で使用する多数の大きなオブジェクトを割り当て**ないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-145">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="1a750-146">上記のようなメモリの問題は[、PerfView](https://github.com/Microsoft/perfview)でガベージ コレクション (GC) の統計を確認し、次の点を調べることによって診断できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-146">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="1a750-147">ガベージ コレクションの休止時間。</span><span class="sxs-lookup"><span data-stu-id="1a750-147">Garbage collection pause time.</span></span>
* <span data-ttu-id="1a750-148">ガベージ コレクションに費やされるプロセッサ時間の割合。</span><span class="sxs-lookup"><span data-stu-id="1a750-148">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="1a750-149">世代 0、1、2 のガベージ コレクションの数。</span><span class="sxs-lookup"><span data-stu-id="1a750-149">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="1a750-150">詳細については、「[ガベージ コレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-150">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access-and-io"></a><span data-ttu-id="1a750-151">データ アクセスと I/O を最適化する</span><span class="sxs-lookup"><span data-stu-id="1a750-151">Optimize data access and I/O</span></span>

<span data-ttu-id="1a750-152">データ ストアやその他のリモート サービスとのやり取りは、多くの場合、ASP.NET Core アプリの最も低速な部分です。</span><span class="sxs-lookup"><span data-stu-id="1a750-152">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="1a750-153">パフォーマンスを向上させるには、データの読み取りと書き込みを効率的に行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-153">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="1a750-154">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-154">Recommendations:</span></span>

* <span data-ttu-id="1a750-155">すべてのデータ アクセス API を非同期的に呼び出**します**。</span><span class="sxs-lookup"><span data-stu-id="1a750-155">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="1a750-156">必要以上のデータを取得**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-156">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="1a750-157">現在の HTTP 要求に必要なデータだけを返すクエリを記述します。</span><span class="sxs-lookup"><span data-stu-id="1a750-157">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="1a750-158">少しデータが最新のデータに問題がある場合は、データベースまたはリモート サービスから取得される頻繁にアクセスされるデータをキャッシュすることを検討**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-158">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="1a750-159">シナリオに応じて、メモリ[キャッシュ](xref:performance/caching/memory)または[分散キャッシュ](xref:performance/caching/distributed)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-159">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="1a750-160">詳細については、「<xref:performance/caching/response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-160">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="1a750-161">ネットワークのラウンド トリップ**を**最小限に抑えます。</span><span class="sxs-lookup"><span data-stu-id="1a750-161">**Do** minimize network round trips.</span></span> <span data-ttu-id="1a750-162">目標は、複数の呼び出しではなく、1 回の呼び出しで必要なデータを取得することです。</span><span class="sxs-lookup"><span data-stu-id="1a750-162">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="1a750-163">読み取り専用の目的でデータにアクセスする場合は、Entity Framework Core で[追跡クエリ](/ef/core/querying/tracking#no-tracking-queries)を使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-163">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="1a750-164">EF Core は、追跡を行っていないクエリの結果をより効率的に返すことができます。</span><span class="sxs-lookup"><span data-stu-id="1a750-164">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="1a750-165">データベース**によってフィルター処理**が実行されるように、LINQ `.Where``.Select`クエリ`.Sum`(、、、または ステートメントなど) をフィルター処理して集計します。</span><span class="sxs-lookup"><span data-stu-id="1a750-165">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="1a750-166">EF Core がクライアント上の一部のクエリ演算子を解決し、クエリの実行が非効率的になる可能性があることを考慮**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-166">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="1a750-167">詳細については、「[クライアント評価パフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-167">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="1a750-168">"N + 1" SQL クエリを実行する可能性があるコレクションに対して、プロジェクション クエリを使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-168">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="1a750-169">詳しくは、[相関サブクエリの最適化を](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-169">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="1a750-170">ハイ スケール アプリのパフォーマンスを向上させる可能性のあるアプローチについては[、「EF ハイ](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)パフォーマンス」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1a750-170">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="1a750-171">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="1a750-171">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="1a750-172">明示的にコンパイルされたクエリ</span><span class="sxs-lookup"><span data-stu-id="1a750-172">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="1a750-173">コード ベースをコミットする前に、前述の高性能アプローチの影響を測定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1a750-173">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="1a750-174">コンパイルされたクエリの複雑さが増しても、パフォーマンスの向上が正当化されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-174">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="1a750-175">クエリの問題は[、Application Insights](/azure/application-insights/app-insights-overview)を使用してデータにアクセスするのに要した時間を確認するか、プロファイリング ツールを使用して検出できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-175">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="1a750-176">ほとんどのデータベースでは、頻繁に実行されるクエリに関する統計情報も利用できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-176">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="1a750-177">HTTP 接続を使用して HTTP 接続をプールします。</span><span class="sxs-lookup"><span data-stu-id="1a750-177">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="1a750-178">[HttpClient は](/dotnet/api/system.net.http.httpclient)インターフェイスを`IDisposable`実装していますが、再利用用に設計されています。</span><span class="sxs-lookup"><span data-stu-id="1a750-178">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="1a750-179">クローズ`HttpClient`されたインスタンスは、ソケットを`TIME_WAIT`短時間開いたままにします。</span><span class="sxs-lookup"><span data-stu-id="1a750-179">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="1a750-180">`HttpClient`オブジェクトを作成して破棄するコード パスが頻繁に使用される場合、アプリは利用可能なソケットを使い果たす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-180">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="1a750-181">[この問題の](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)解決策として、ASP.NETコア 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="1a750-181">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="1a750-182">パフォーマンスと信頼性を最適化するために、HTTP 接続のプールを処理します。</span><span class="sxs-lookup"><span data-stu-id="1a750-182">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="1a750-183">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-183">Recommendations:</span></span>

* <span data-ttu-id="1a750-184">インスタンスを直接作成して破棄**しないでください。** `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="1a750-184">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="1a750-185">インスタンスを取得`HttpClient`するには[、HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を使用**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-185">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="1a750-186">詳細については、「[HttpClientFactory を使用して回復力の高い HTTP 要求を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-186">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="1a750-187">共通のコード パスを高速に維持する</span><span class="sxs-lookup"><span data-stu-id="1a750-187">Keep common code paths fast</span></span>

<span data-ttu-id="1a750-188">すべてのコードを高速にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-188">You want all of your code to be fast.</span></span> <span data-ttu-id="1a750-189">頻繁に呼び出されるコード パスは、最適化が最も重要です。</span><span class="sxs-lookup"><span data-stu-id="1a750-189">Frequently-called code paths are the most critical to optimize.</span></span> <span data-ttu-id="1a750-190">次の設定があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-190">These include:</span></span>

* <span data-ttu-id="1a750-191">アプリの要求処理パイプラインのミドルウェア コンポーネント、特にミドルウェアはパイプラインの初期段階で実行されます。</span><span class="sxs-lookup"><span data-stu-id="1a750-191">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="1a750-192">これらのコンポーネントは、パフォーマンスに大きな影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="1a750-192">These components have a large impact on performance.</span></span>
* <span data-ttu-id="1a750-193">要求ごとに実行されるコード、または要求ごとに複数回実行されるコード。</span><span class="sxs-lookup"><span data-stu-id="1a750-193">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="1a750-194">たとえば、カスタム ログ、承認ハンドラー、一時的なサービスの初期化などです。</span><span class="sxs-lookup"><span data-stu-id="1a750-194">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="1a750-195">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-195">Recommendations:</span></span>

* <span data-ttu-id="1a750-196">実行時間の長いタスクでは、カスタムミドルウェアコンポーネントを使用**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-196">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="1a750-197">パフォーマンス プロファイリング ツール[(Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)や[PerfView](https://github.com/Microsoft/perfview)など) を使用して[、ホット コード パス](#understand-hot-code-paths)を識別**する**。</span><span class="sxs-lookup"><span data-stu-id="1a750-197">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="1a750-198">HTTP 要求以外の長時間実行タスクを完了する</span><span class="sxs-lookup"><span data-stu-id="1a750-198">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="1a750-199">ASP.NET Core アプリへのほとんどの要求は、必要なサービスを呼び出して HTTP 応答を返すコントローラーまたはページ モデルによって処理できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-199">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="1a750-200">実行時間の長いタスクを含む一部の要求では、要求/応答プロセス全体を非同期にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1a750-200">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="1a750-201">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-201">Recommendations:</span></span>

* <span data-ttu-id="1a750-202">通常の HTTP 要求処理の一部として、長時間実行されるタスクが完了するのを待**つ必要はありません**。</span><span class="sxs-lookup"><span data-stu-id="1a750-202">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="1a750-203">[Azure 関数](/azure/azure-functions/)**を**使用して[、バックグラウンド サービス](xref:fundamentals/host/hosted-services)またはアウト プロセスで長時間実行される要求を処理することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-203">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="1a750-204">プロセス外で作業を完了することは、CPU を集中的に使用するタスクに特に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1a750-204">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="1a750-205">**クライアント**と非同期通信するには、 などの[SignalR](xref:signalr/introduction)リアルタイム通信オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-205">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="1a750-206">顧客資産の縮小</span><span class="sxs-lookup"><span data-stu-id="1a750-206">Minify client assets</span></span>

<span data-ttu-id="1a750-207">複雑なフロントエンドを備えたASP.NETコアアプリは、多くのJavaScript、CSS、または画像ファイルを頻繁に提供します。</span><span class="sxs-lookup"><span data-stu-id="1a750-207">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="1a750-208">初期ロード要求のパフォーマンスは、次の方法で改善できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-208">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="1a750-209">複数のファイルを 1 つに結合するバンドル。</span><span class="sxs-lookup"><span data-stu-id="1a750-209">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="1a750-210">縮小 : 空白やコメントを削除してファイルのサイズを縮小します。</span><span class="sxs-lookup"><span data-stu-id="1a750-210">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="1a750-211">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-211">Recommendations:</span></span>

* <span data-ttu-id="1a750-212">クライアント資産のバンドルと縮小に対する Core の[組み込みサポート](xref:client-side/bundling-and-minification)ASP.NET使用**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-212">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="1a750-213">複雑なクライアント資産管理のために[、Webpack](https://webpack.js.org/)などの他のサードパーティ製ツールを検討**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-213">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="1a750-214">応答を圧縮する</span><span class="sxs-lookup"><span data-stu-id="1a750-214">Compress responses</span></span>

 <span data-ttu-id="1a750-215">応答のサイズを小さくすると、通常、アプリの応答性が向上し、多くの場合、劇的に増加します。</span><span class="sxs-lookup"><span data-stu-id="1a750-215">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="1a750-216">ペイロード サイズを小さくする方法の 1 つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="1a750-216">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="1a750-217">詳細については、[応答圧縮](xref:performance/response-compression)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-217">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="1a750-218">最新のASP.NETコア リリースを使用する</span><span class="sxs-lookup"><span data-stu-id="1a750-218">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="1a750-219">ASP.NET Core の新しいリリースごとに、パフォーマンスの向上が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1a750-219">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="1a750-220">NET Core と ASP.NET Core の最適化は、新しいバージョンが一般的に古いバージョンよりも優れ、その結果を上回るということです。</span><span class="sxs-lookup"><span data-stu-id="1a750-220">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="1a750-221">たとえば、.NET Core 2.1 はコンパイルされた正規表現のサポートを追加し[、Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx)の恩恵を受けています。</span><span class="sxs-lookup"><span data-stu-id="1a750-221">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="1a750-222">ASP.NETコア 2.2 HTTP/2 のサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="1a750-222">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="1a750-223">[ASP.NET Core 3.0 では、](xref:aspnetcore-3.0)メモリ使用量を削減し、スループットを向上させる多くの機能強化が追加されました。</span><span class="sxs-lookup"><span data-stu-id="1a750-223">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="1a750-224">パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-224">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="1a750-225">例外を最小限に抑える</span><span class="sxs-lookup"><span data-stu-id="1a750-225">Minimize exceptions</span></span>

<span data-ttu-id="1a750-226">例外はまれです。</span><span class="sxs-lookup"><span data-stu-id="1a750-226">Exceptions should be rare.</span></span> <span data-ttu-id="1a750-227">例外のスローとキャッチは、他のコード フロー パターンに比べて遅くなります。</span><span class="sxs-lookup"><span data-stu-id="1a750-227">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="1a750-228">このため、通常のプログラム フローを制御するために例外を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1a750-228">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="1a750-229">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="1a750-229">Recommendations:</span></span>

* <span data-ttu-id="1a750-230">通常のプログラム フローの手段として例外をスローまたはキャッチ**しないでください**[。](#understand-hot-code-paths)</span><span class="sxs-lookup"><span data-stu-id="1a750-230">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="1a750-231">例外の原因となる条件を検出して処理するロジックをアプリ**に含めます**。</span><span class="sxs-lookup"><span data-stu-id="1a750-231">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="1a750-232">異常なまたは予期しない条件の例外をスローまたはキャッチ**してください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-232">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="1a750-233">アプリケーションインサイトなどのアプリ診断ツールは、パフォーマンスに影響を与える可能性のあるアプリの一般的な例外を特定するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1a750-233">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="1a750-234">パフォーマンスと信頼性</span><span class="sxs-lookup"><span data-stu-id="1a750-234">Performance and reliability</span></span>

<span data-ttu-id="1a750-235">以下のセクションでは、パフォーマンスに関するヒント、既知の信頼性の問題と解決策を示します。</span><span class="sxs-lookup"><span data-stu-id="1a750-235">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="1a750-236">HttpRequest/HttpResponse 本体で同期読み取りまたは書き込みを回避する</span><span class="sxs-lookup"><span data-stu-id="1a750-236">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="1a750-237">ASP.NETコアのすべての I/O は非同期です。</span><span class="sxs-lookup"><span data-stu-id="1a750-237">All I/O in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="1a750-238">サーバーは、同期`Stream`および非同期の両方のオーバーロードを持つインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="1a750-238">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="1a750-239">スレッド プールスレッドをブロックしないように、非同期のスレッドを優先する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-239">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="1a750-240">スレッドをブロックすると、スレッド プールの不足が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-240">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="1a750-241">**次の操作を行わない**。次の例では、<xref:System.IO.StreamReader.ReadToEnd*>を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-241">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="1a750-242">現在のスレッドが結果を待つブロックされます。</span><span class="sxs-lookup"><span data-stu-id="1a750-242">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="1a750-243">これは、非同期での[同期の](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)例です。</span><span class="sxs-lookup"><span data-stu-id="1a750-243">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="1a750-244">上記のコードでは、`Get`同期的に、HTTP 要求本文全体をメモリに読み取ります。</span><span class="sxs-lookup"><span data-stu-id="1a750-244">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="1a750-245">クライアントのアップロードが遅い場合、アプリは非同期で同期を実行しています。</span><span class="sxs-lookup"><span data-stu-id="1a750-245">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="1a750-246">Kestrel は同期読み取りをサポート**していないため**、アプリは非同期で同期します。</span><span class="sxs-lookup"><span data-stu-id="1a750-246">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="1a750-247">**これを行います。** 次の例では<xref:System.IO.StreamReader.ReadToEndAsync*>、読み取り中にスレッドを使用し、ブロックしません。</span><span class="sxs-lookup"><span data-stu-id="1a750-247">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="1a750-248">上記のコードは、HTTP 要求本文全体をメモリに非同期的に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="1a750-248">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="1a750-249">要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-249">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="1a750-250">OOM はサービス拒否の原因になります。</span><span class="sxs-lookup"><span data-stu-id="1a750-250">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="1a750-251">詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込まないように](#arlb)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-251">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="1a750-252">**これを行います。** 次の例は、バッファリングされていない要求本文を使用して完全に非同期です。</span><span class="sxs-lookup"><span data-stu-id="1a750-252">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="1a750-253">上記のコードでは、要求本文を非同期に C# オブジェクトに逆シリアル化します。</span><span class="sxs-lookup"><span data-stu-id="1a750-253">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="1a750-254">要求よりも ReadFormAsync を優先します。</span><span class="sxs-lookup"><span data-stu-id="1a750-254">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="1a750-255">`HttpContext.Request.ReadFormAsync` の代わりに `HttpContext.Request.Form` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-255">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="1a750-256">`HttpContext.Request.Form`次の条件で安全に読み取り専用にすることができます。</span><span class="sxs-lookup"><span data-stu-id="1a750-256">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="1a750-257">フォームは、 への`ReadFormAsync`呼び出しによって読み取られた</span><span class="sxs-lookup"><span data-stu-id="1a750-257">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="1a750-258">キャッシュされたフォーム値を使用して読み取っています`HttpContext.Request.Form`</span><span class="sxs-lookup"><span data-stu-id="1a750-258">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="1a750-259">**次の操作を行わない**。次の例では`HttpContext.Request.Form`、 を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-259">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="1a750-260">`HttpContext.Request.Form`[は非同期同期を](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)使用するため、スレッド プールの枯れにつながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-260">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="1a750-261">**これを行います。** 次の例では`HttpContext.Request.ReadFormAsync`、フォーム本体を非同期的に読み取るために使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-261">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="1a750-262">大きな要求本文または応答本文をメモリに読み込まないようにする</span><span class="sxs-lookup"><span data-stu-id="1a750-262">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="1a750-263">.NET では、85 KB を超えるオブジェクト割り当てはすべて、ラージ オブジェクト ヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) に配置されます。</span><span class="sxs-lookup"><span data-stu-id="1a750-263">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="1a750-264">大きなオブジェクトは、次の 2 つの点でコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="1a750-264">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="1a750-265">新しく割り当てられたラージ オブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="1a750-265">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="1a750-266">CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。</span><span class="sxs-lookup"><span data-stu-id="1a750-266">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="1a750-267">LOH はヒープの残りの部分と共に収集されます。</span><span class="sxs-lookup"><span data-stu-id="1a750-267">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="1a750-268">LOH には、フル[ガベージ コレクション](/dotnet/standard/garbage-collection/fundamentals)または[Gen2 コレクション](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。</span><span class="sxs-lookup"><span data-stu-id="1a750-268">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="1a750-269">この[ブログ記事](https://adamsitnik.com/Array-Pool/#the-problem)では、問題を簡潔に説明します。</span><span class="sxs-lookup"><span data-stu-id="1a750-269">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="1a750-270">大きなオブジェクトが割り当てられると、Gen 2 オブジェクトとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="1a750-270">When a large object is allocated, it's marked as Gen 2 object.</span></span> <span data-ttu-id="1a750-271">小さなオブジェクトに関しては Gen 0 ではありません。</span><span class="sxs-lookup"><span data-stu-id="1a750-271">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="1a750-272">その結果、LOH でメモリが不足すると、GC は LOH だけでなくマネージ ヒープ全体をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="1a750-272">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="1a750-273">そこで、LOH を含む Gen 0、Gen 1、および Gen 2 をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="1a750-273">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="1a750-274">これはフル ガベージ コレクションと呼ばれ、最も時間のかかるガベージ コレクションです。</span><span class="sxs-lookup"><span data-stu-id="1a750-274">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="1a750-275">多くのアプリケーションでは、許容できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-275">For many applications, it can be acceptable.</span></span> <span data-ttu-id="1a750-276">しかし、平均的なウェブリクエスト(ソケットからの読み取り、解凍、JSON&のデコード)を処理するために必要な大きなメモリバッファがほとんどない高性能ウェブサーバーには、間違いなくありません。</span><span class="sxs-lookup"><span data-stu-id="1a750-276">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="1a750-277">単純に単一`byte[]`または`string`に大きな要求または応答の本文を格納します。</span><span class="sxs-lookup"><span data-stu-id="1a750-277">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="1a750-278">LOH のスペースが急速に不足する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-278">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="1a750-279">フル GC が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-279">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="1a750-280">同期データ処理 API の操作</span><span class="sxs-lookup"><span data-stu-id="1a750-280">Working with a synchronous data processing API</span></span>

<span data-ttu-id="1a750-281">同期読み取りと書き込みのみをサポートするシリアライザー/逆シリアライザーを使用する場合 ([たとえば、JSON.NET):](https://www.newtonsoft.com/json/help/html/Introduction.htm)</span><span class="sxs-lookup"><span data-stu-id="1a750-281">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="1a750-282">シリアライザー/逆シリアライザーに渡す前に、データを非同期にメモリにバッファーします。</span><span class="sxs-lookup"><span data-stu-id="1a750-282">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="1a750-283">要求が大きい場合は、メモリ不足 (OOM) 状態が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-283">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="1a750-284">OOM はサービス拒否の原因になります。</span><span class="sxs-lookup"><span data-stu-id="1a750-284">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="1a750-285">詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込まないように](#arlb)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-285">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="1a750-286">ASP.NET Core 3.0 では、既定で JSON シリアル化が使用<xref:System.Text.Json>されます。</span><span class="sxs-lookup"><span data-stu-id="1a750-286">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="1a750-287"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="1a750-287"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="1a750-288">非同期で JSON の読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="1a750-288">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="1a750-289">UTF-8 テキスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="1a750-289">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="1a750-290">通常、`Newtonsoft.Json` よりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="1a750-290">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="1a750-291">フィールドに IHttp コンテキストアクセサー.Http コンテキストを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="1a750-291">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="1a750-292">[要求](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)スレッドからアクセスされると、アクティブな要求`HttpContext`のを返します。</span><span class="sxs-lookup"><span data-stu-id="1a750-292">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="1a750-293">フィールド`IHttpContextAccessor.HttpContext`または変数には格納**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1a750-293">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="1a750-294">**次の操作を行わない**。次の例では、`HttpContext`をフィールドに格納し、後で使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="1a750-294">**Do not do this:** The following example stores the `HttpContext` in a field and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="1a750-295">上記のコードでは、コンストラクターで null または`HttpContext`誤りがある場合に、頻繁にキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="1a750-295">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="1a750-296">**これを行います。** 次の例を示します。</span><span class="sxs-lookup"><span data-stu-id="1a750-296">**Do this:** The following example:</span></span>

* <span data-ttu-id="1a750-297">フィールドに<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>を格納します。</span><span class="sxs-lookup"><span data-stu-id="1a750-297">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="1a750-298">フィールドを`HttpContext`正しい時刻に使用し、`null`をチェックします。</span><span class="sxs-lookup"><span data-stu-id="1a750-298">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="1a750-299">複数のスレッドから HttpContext にアクセスしない</span><span class="sxs-lookup"><span data-stu-id="1a750-299">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="1a750-300">`HttpContext`はスレッドセーフ*ではありません*。</span><span class="sxs-lookup"><span data-stu-id="1a750-300">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="1a750-301">複数の`HttpContext`スレッドから並列にアクセスすると、ハング、クラッシュ、データの破損などの未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-301">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="1a750-302">**次の操作を行わない**。次の例では、3 つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="1a750-302">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="1a750-303">要求パスは、複数のスレッドからアクセスされ、場合によっては並列でアクセスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-303">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="1a750-304">**これを行います。** 次の例では、3 つの並列要求を行う前に、受信要求からすべてのデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="1a750-304">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="1a750-305">要求が完了した後に HttpContext を使用しない</span><span class="sxs-lookup"><span data-stu-id="1a750-305">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="1a750-306">`HttpContext`は、ASP.NETコア パイプラインにアクティブな HTTP 要求がある場合にのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="1a750-306">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="1a750-307">ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。</span><span class="sxs-lookup"><span data-stu-id="1a750-307">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="1a750-308">このチェーン`Task`から返されたが完了すると、が`HttpContext`リサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="1a750-308">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="1a750-309">**次の操作を行わない**。次の例では`async void`、最初`await`の要求に達したときに HTTP 要求を完了させる例を使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-309">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="1a750-310">これは**常に**ASP.NETコアアプリでは悪い習慣です。</span><span class="sxs-lookup"><span data-stu-id="1a750-310">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="1a750-311">HTTP 要求`HttpResponse`が完了した後に にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="1a750-311">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="1a750-312">プロセスをクラッシュさせます。</span><span class="sxs-lookup"><span data-stu-id="1a750-312">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="1a750-313">**これを行います。** 次の例では、`Task`フレームワークに a が返されるため、アクションが完了するまで HTTP 要求は完了しません。</span><span class="sxs-lookup"><span data-stu-id="1a750-313">**Do this:** The following example returns a `Task` to the framework, so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="1a750-314">バックグラウンド スレッドで HttpContext をキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="1a750-314">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="1a750-315">**次の操作を行わない**。次の例は、クロージャが`HttpContext``Controller`プロパティからキャプチャされていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="1a750-315">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="1a750-316">作業項目は次の処理を行うことができるため、これは不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="1a750-316">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="1a750-317">要求スコープの外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="1a750-317">Run outside of the request scope.</span></span>
* <span data-ttu-id="1a750-318">間違った`HttpContext`を読み取ろうとします。</span><span class="sxs-lookup"><span data-stu-id="1a750-318">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="1a750-319">**これを行います。** 次の例を示します。</span><span class="sxs-lookup"><span data-stu-id="1a750-319">**Do this:** The following example:</span></span>

* <span data-ttu-id="1a750-320">要求中にバックグラウンド タスクに必要なデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="1a750-320">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="1a750-321">コントローラーから何も参照しません。</span><span class="sxs-lookup"><span data-stu-id="1a750-321">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="1a750-322">バックグラウンド タスクは、ホストされたサービスとして実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1a750-322">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="1a750-323">詳細については、「[Background tasks with hosted services](xref:fundamentals/host/hosted-services)」(ホストされるタスクを使用するバックグラウンド タスク) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1a750-323">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="1a750-324">バックグラウンド スレッドのコントローラに挿入されたサービスをキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="1a750-324">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="1a750-325">**次の操作を行わない**。次の例は、`DbContext``Controller`クロージャが action パラメーターからキャプチャされていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="1a750-325">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="1a750-326">これは悪い習慣です。</span><span class="sxs-lookup"><span data-stu-id="1a750-326">This is a bad practice.</span></span>  <span data-ttu-id="1a750-327">作業項目は、要求スコープの外部で実行できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-327">The work item could run outside of the request scope.</span></span> <span data-ttu-id="1a750-328">は`ContosoDbContext`、要求の範囲が指定され、その結果`ObjectDisposedException`、 が返されます。</span><span class="sxs-lookup"><span data-stu-id="1a750-328">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="1a750-329">**これを行います。** 次の例を示します。</span><span class="sxs-lookup"><span data-stu-id="1a750-329">**Do this:** The following example:</span></span>

* <span data-ttu-id="1a750-330">バックグラウンド作業項目に<xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>スコープを作成するために、 を挿入します。</span><span class="sxs-lookup"><span data-stu-id="1a750-330">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="1a750-331">`IServiceScopeFactory`シングルトンです。</span><span class="sxs-lookup"><span data-stu-id="1a750-331">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="1a750-332">バックグラウンド スレッドに新しい依存関係挿入スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="1a750-332">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="1a750-333">コントローラーから何も参照しません。</span><span class="sxs-lookup"><span data-stu-id="1a750-333">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="1a750-334">受信した要求から`ContosoDbContext`をキャプチャしません。</span><span class="sxs-lookup"><span data-stu-id="1a750-334">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="1a750-335">次の強調表示されたコード。</span><span class="sxs-lookup"><span data-stu-id="1a750-335">The following highlighted code:</span></span>

* <span data-ttu-id="1a750-336">バックグラウンド操作の有効期間のスコープを作成し、そこからサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="1a750-336">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="1a750-337">正`ContosoDbContext`しいスコープから使用します。</span><span class="sxs-lookup"><span data-stu-id="1a750-337">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="1a750-338">応答本文が開始された後、状態コードまたはヘッダーを変更しない</span><span class="sxs-lookup"><span data-stu-id="1a750-338">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="1a750-339">ASP.NETコアは HTTP 応答本体をバッファしません。</span><span class="sxs-lookup"><span data-stu-id="1a750-339">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="1a750-340">初めて応答を書き込む場合:</span><span class="sxs-lookup"><span data-stu-id="1a750-340">The first time the response is written:</span></span>

* <span data-ttu-id="1a750-341">ヘッダーは、その本体のチャンクと共にクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="1a750-341">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="1a750-342">応答ヘッダーを変更することはできなくなりました。</span><span class="sxs-lookup"><span data-stu-id="1a750-342">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="1a750-343">**次の操作を行わない**。次のコードは、応答が既に開始された後に応答ヘッダーを追加しようとします。</span><span class="sxs-lookup"><span data-stu-id="1a750-343">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="1a750-344">上記のコードでは、`context.Response.Headers["test"] = "test value";`応答に書き込まれた`next()`場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="1a750-344">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="1a750-345">**これを行います。** 次の例では、ヘッダーを変更する前に HTTP 応答が開始されているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="1a750-345">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="1a750-346">**これを行います。** 応答ヘッダーがクライアント`HttpResponse.OnStarting`にフラッシュされる前にヘッダーを設定する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1a750-346">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="1a750-347">応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="1a750-347">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="1a750-348">応答が開始されていないかどうかの確認:</span><span class="sxs-lookup"><span data-stu-id="1a750-348">Checking if the response has not started:</span></span>

* <span data-ttu-id="1a750-349">ヘッダーを追加またはオーバーライドする機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="1a750-349">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="1a750-350">パイプライン内の次のミドルウェアに関する知識は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1a750-350">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="1a750-351">応答本文への書き込みを既に開始している場合は next() を呼び出さない</span><span class="sxs-lookup"><span data-stu-id="1a750-351">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="1a750-352">コンポーネントは、応答を処理して操作できる場合にのみ呼び出されることを想定します。</span><span class="sxs-lookup"><span data-stu-id="1a750-352">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>
