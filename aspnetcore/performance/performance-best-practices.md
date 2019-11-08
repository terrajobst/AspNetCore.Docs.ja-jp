---
title: ASP.NET Core パフォーマンスのベストプラクティス
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスを向上させ、一般的なパフォーマンスの問題を回避するためのヒントです。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/26/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 1cd4ca6fccfee674f46e87ba051e049f7daa5b66
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799522"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="e82b2-103">ASP.NET Core パフォーマンスのベストプラクティス</span><span class="sxs-lookup"><span data-stu-id="e82b2-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="e82b2-104">[Mike/sos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="e82b2-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="e82b2-105">この記事では、ASP.NET Core のパフォーマンスのベストプラクティスに関するガイドラインを示します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="e82b2-106">積極的にキャッシュ</span><span class="sxs-lookup"><span data-stu-id="e82b2-106">Cache aggressively</span></span>

<span data-ttu-id="e82b2-107">キャッシュについては、このドキュメントのいくつかの部分で説明します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="e82b2-108">詳細については、「<xref:performance/caching/response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="e82b2-109">ホットコードパスについて</span><span class="sxs-lookup"><span data-stu-id="e82b2-109">Understand hot code paths</span></span>

<span data-ttu-id="e82b2-110">このドキュメントでは、*ホットコードパス*は、頻繁に呼び出される、実行時間の大半が発生するコードパスとして定義されています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="e82b2-111">ホットコードパスは、通常、アプリのスケールアウトとパフォーマンスを制限し、このドキュメントのいくつかの部分で説明されています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="e82b2-112">ブロック呼び出しを回避する</span><span class="sxs-lookup"><span data-stu-id="e82b2-112">Avoid blocking calls</span></span>

<span data-ttu-id="e82b2-113">ASP.NET Core アプリは、多数の要求を同時に処理するように設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="e82b2-114">非同期 Api を使用すると、スレッドの小さなプールで、ブロックしている呼び出しを待機せずに、数千の同時要求を処理することができます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="e82b2-115">実行時間の長い同期タスクが完了するのを待機するのではなく、スレッドが別の要求で作業できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="e82b2-116">ASP.NET Core アプリのパフォーマンスに関する一般的な問題は、非同期の呼び出しをブロックしていることです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="e82b2-117">多くの同期ブロッキング呼び出しは、[スレッドプールの枯渇](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)と応答時間の低下につながります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="e82b2-118">**実行**しない:</span><span class="sxs-lookup"><span data-stu-id="e82b2-118">**Do not**:</span></span>

* <span data-ttu-id="e82b2-119">[Task. Wait](/dotnet/api/system.threading.tasks.task.wait)または[task. Result](/dotnet/api/system.threading.tasks.task-1.result)を呼び出して、非同期実行をブロックします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="e82b2-120">共通コードパスのロックを取得します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="e82b2-121">ASP.NET Core アプリは、コードを並列実行するように設計されている場合に最もパフォーマンスが高くなります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>

<span data-ttu-id="e82b2-122">**操作**:</span><span class="sxs-lookup"><span data-stu-id="e82b2-122">**Do**:</span></span>

* <span data-ttu-id="e82b2-123">[ホットコードパス](#understand-hot-code-paths)を非同期にします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-123">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="e82b2-124">データアクセスと長時間実行される操作 Api を非同期に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-124">Call data access and long-running operations APIs asynchronously.</span></span>
* <span data-ttu-id="e82b2-125">コントローラー/Razor ページのアクションを非同期にします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-125">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="e82b2-126">非同期[/await](/dotnet/csharp/programming-guide/concepts/async/)パターンを活用するために、呼び出し履歴全体が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-126">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="e82b2-127">[Perfview](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、[スレッドプール](/windows/desktop/procthread/thread-pools)に頻繁に追加されるスレッドを見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-127">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="e82b2-128">`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` イベントは、スレッドプールに追加されたスレッドを示します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-128">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="e82b2-129">大きなオブジェクトの割り当てを最小限にする</span><span class="sxs-lookup"><span data-stu-id="e82b2-129">Minimize large object allocations</span></span>

<span data-ttu-id="e82b2-130">[.Net Core ガベージコレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリで自動的にメモリの割り当てと解放を管理します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-130">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="e82b2-131">自動ガベージコレクションは、一般に、メモリが解放される方法とタイミングについて開発者が心配する必要がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-131">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="e82b2-132">ただし、参照されていないオブジェクトをクリーンアップすると CPU 時間がかかるため、開発者は[ホットコードパス](#understand-hot-code-paths)内のオブジェクトの割り当てを最小限に抑える必要があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-132">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="e82b2-133">ガベージコレクションは、大きなオブジェクト (> 85 K バイト) で特にコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-133">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="e82b2-134">ラージオブジェクトは[大きなオブジェクトヒープ](/dotnet/standard/garbage-collection/large-object-heap)に格納され、クリーンアップするには、完全な (ジェネレーション 2) ガベージコレクションが必要です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-134">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="e82b2-135">ジェネレーション0およびジェネレーション1のコレクションとは異なり、ジェネレーション2のコレクションでは、アプリの実行を一時的に中断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-135">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="e82b2-136">大きなオブジェクトを頻繁に割り当てたり割り当て解除したりすると、パフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-136">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="e82b2-137">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-137">Recommendations:</span></span>

* <span data-ttu-id="e82b2-138">頻繁に使用されるラージオブジェクトをキャッシュすること**を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="e82b2-138">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="e82b2-139">大きなオブジェクトをキャッシュすると、コストのかかる割り当てを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-139">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="e82b2-140">大きな配列を格納するには、 [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1)を使用してバッファー**をプールし**ます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-140">**Do** pool buffers by using an [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="e82b2-141">[ホットコードパス](#understand-hot-code-paths)には、有効期間が短い大きなオブジェクトを多数割り当て**ない**でください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-141">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="e82b2-142">前述のようなメモリの問題は、 [Perfview](https://github.com/Microsoft/perfview)でガベージコレクション (GC) の統計を確認して調べることによって診断できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-142">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="e82b2-143">ガベージコレクションの一時停止時刻。</span><span class="sxs-lookup"><span data-stu-id="e82b2-143">Garbage collection pause time.</span></span>
* <span data-ttu-id="e82b2-144">ガベージコレクションで消費されるプロセッサ時間の割合。</span><span class="sxs-lookup"><span data-stu-id="e82b2-144">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="e82b2-145">ジェネレーション0、1、および2のガベージコレクションの数。</span><span class="sxs-lookup"><span data-stu-id="e82b2-145">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="e82b2-146">詳細については、「[ガベージコレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-146">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access"></a><span data-ttu-id="e82b2-147">データアクセスの最適化</span><span class="sxs-lookup"><span data-stu-id="e82b2-147">Optimize Data Access</span></span>

<span data-ttu-id="e82b2-148">多くの場合、データストアやその他のリモートサービスとのやり取りは、ASP.NET Core アプリの最も低速な部分です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-148">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="e82b2-149">効率的なパフォーマンスを利用するには、データの読み取りと書き込みを効率的に行うことが重要です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-149">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="e82b2-150">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-150">Recommendations:</span></span>

* <span data-ttu-id="e82b2-151">すべてのデータアクセス Api を非同期**に呼び出します**。</span><span class="sxs-lookup"><span data-stu-id="e82b2-151">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="e82b2-152">必要以上に多くのデータを取得**しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-152">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="e82b2-153">現在の HTTP 要求に必要なデータだけを返すクエリを記述します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-153">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="e82b2-154">少し古いデータが許容される場合は、データベースまたはリモートサービスから取得した頻繁にアクセスされるデータをキャッシュすること**を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="e82b2-154">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="e82b2-155">シナリオに応じて、 [Memorycache](xref:performance/caching/memory)または[microsoft.web.distributedcache](xref:performance/caching/distributed)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-155">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="e82b2-156">詳細については、「<xref:performance/caching/response>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-156">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="e82b2-157">ネットワークラウンドトリップ**を最小限に**抑えます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-157">**Do** minimize network round trips.</span></span> <span data-ttu-id="e82b2-158">目的は、複数の呼び出しではなく、1回の呼び出しで必要なデータを取得することです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-158">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="e82b2-159">読み取り専用の目的でデータにアクセスする場合**は**、Entity Framework Core で[追跡なしのクエリ](/ef/core/querying/tracking#no-tracking-queries)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-159">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="e82b2-160">EF Core は、追跡しないクエリの結果をより効率的に返すことができます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-160">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="e82b2-161">(たとえば、`.Where`、`.Select`、または `.Sum` ステートメントを使用して) LINQ クエリのフィルター処理と集計を**実行**し、データベースによってフィルター処理が実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-161">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="e82b2-162">EF Core に**よってクライアント**上の一部のクエリ演算子が解決されるため、クエリの実行効率が悪くなることがあります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-162">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="e82b2-163">詳細については、「[クライアント評価のパフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-163">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="e82b2-164">コレクションに対してプロジェクションクエリを使用しないでください。これにより、"N + 1" 個の SQL クエリが実行される可能性が**あり**ます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-164">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="e82b2-165">詳細については、「[相関サブクエリの最適化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-165">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="e82b2-166">高スケールアプリのパフォーマンスを向上させる方法については、「 [EF High performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-166">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="e82b2-167">DbContext プーリング</span><span class="sxs-lookup"><span data-stu-id="e82b2-167">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="e82b2-168">明示的にコンパイルされたクエリ</span><span class="sxs-lookup"><span data-stu-id="e82b2-168">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="e82b2-169">コードベースをコミットする前に、上記の高パフォーマンスアプローチの影響を測定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-169">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="e82b2-170">コンパイル済みクエリの複雑さが増えると、パフォーマンスの向上には合わない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-170">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="e82b2-171">[Application Insights](/azure/application-insights/app-insights-overview)またはプロファイリングツールでデータにアクセスするために費やされた時間を確認することで、クエリの問題を検出できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-171">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="e82b2-172">ほとんどのデータベースでは、頻繁に実行されるクエリに関する統計も利用できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-172">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="e82b2-173">HttpClientFactory を使用して HTTP 接続をプールする</span><span class="sxs-lookup"><span data-stu-id="e82b2-173">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="e82b2-174">[Httpclient](/dotnet/api/system.net.http.httpclient)は `IDisposable` インターフェイスを実装していますが、再利用できるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-174">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="e82b2-175">閉じられた `HttpClient` インスタンスは、短時間、`TIME_WAIT` の状態でソケットを開いたままにします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-175">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="e82b2-176">`HttpClient` オブジェクトを作成および破棄するコードパスが頻繁に使用される場合、アプリは使用可能なソケットを消費する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-176">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="e82b2-177">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)は、この問題の解決策として ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e82b2-177">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="e82b2-178">これは、パフォーマンスと信頼性を最適化するために、プール HTTP 接続を処理します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-178">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="e82b2-179">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-179">Recommendations:</span></span>

* <span data-ttu-id="e82b2-180">`HttpClient` インスタンスを直接作成して破棄しない**で**ください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-180">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="e82b2-181">`HttpClient` インスタンスを取得するに**は**、 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-181">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="e82b2-182">詳細については、「 [HttpClientFactory を使用して弾力性のある HTTP 要求を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-182">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="e82b2-183">共通コードパスを高速に保つ</span><span class="sxs-lookup"><span data-stu-id="e82b2-183">Keep common code paths fast</span></span>

<span data-ttu-id="e82b2-184">すべてのコードが高速になるようにするには、コードパスと呼ばれることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-184">You want all of your code to be fast, frequently called code paths are the most critical to optimize:</span></span>

* <span data-ttu-id="e82b2-185">アプリケーションの要求処理パイプラインのミドルウェアコンポーネント (特に、ミドルウェアはパイプラインの早い段階で実行されます)。</span><span class="sxs-lookup"><span data-stu-id="e82b2-185">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="e82b2-186">これらのコンポーネントは、パフォーマンスに大きな影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-186">These components have a large impact on performance.</span></span>
* <span data-ttu-id="e82b2-187">要求ごとに、または要求ごとに複数回実行されるコード。</span><span class="sxs-lookup"><span data-stu-id="e82b2-187">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="e82b2-188">たとえば、カスタムログ、承認ハンドラー、または一時的なサービスの初期化などです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-188">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="e82b2-189">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-189">Recommendations:</span></span>

* <span data-ttu-id="e82b2-190">実行時間の長いタスクでカスタムミドルウェアコンポーネントを使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-190">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="e82b2-191">[ホットコードパス](#understand-hot-code-paths)を特定するには、 [Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)や[perfview](https://github.com/Microsoft/perfview)などのパフォーマンスプロファイリングツール**を使用し**ます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-191">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="e82b2-192">HTTP 要求の外部で実行時間の長いタスクを完了する</span><span class="sxs-lookup"><span data-stu-id="e82b2-192">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="e82b2-193">ASP.NET Core アプリに対するほとんどの要求は、必要なサービスを呼び出すコントローラーまたはページモデルによって処理され、HTTP 応答を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-193">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="e82b2-194">長時間実行されるタスクが含まれている一部の要求では、要求-応答プロセス全体を非同期にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-194">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="e82b2-195">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-195">Recommendations:</span></span>

* <span data-ttu-id="e82b2-196">通常の HTTP 要求処理の一部として、長時間実行されるタスクが完了するまで待機しない**で**ください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-196">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="e82b2-197">[バックグラウンドサービス](xref:fundamentals/host/hosted-services)で長時間実行される要求や、 [Azure 関数](/azure/azure-functions/)を使用したアウトプロセスを処理すること**を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="e82b2-197">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="e82b2-198">特に、CPU を集中的に使用するタスクには、アウトプロセスの完了が役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-198">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="e82b2-199">[SignalR](xref:signalr/introduction)などのリアルタイム通信オプション**を使用し**て、クライアントと非同期的に通信します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-199">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="e82b2-200">クライアント資産の縮小</span><span class="sxs-lookup"><span data-stu-id="e82b2-200">Minify client assets</span></span>

<span data-ttu-id="e82b2-201">複雑なフロントエンドを使用する ASP.NET Core アプリは、多くの JavaScript、CSS、またはイメージファイルに頻繁に対応します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-201">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="e82b2-202">初期読み込み要求のパフォーマンスは、次の方法で改善できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-202">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="e82b2-203">バンドル。複数のファイルを1つに結合します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-203">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="e82b2-204">縮小。空白とコメントを削除することによってファイルのサイズを縮小します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-204">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="e82b2-205">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-205">Recommendations:</span></span>

* <span data-ttu-id="e82b2-206">クライアント資産のバンドルと縮小には ASP.NET Core の[組み込みサポート](xref:client-side/bundling-and-minification)**を使用し**ます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-206">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="e82b2-207">複雑なクライアント資産管理には、 [Webpack](https://webpack.js.org/)などの他のサードパーティ製ツールを使用すること**を検討してください。**</span><span class="sxs-lookup"><span data-stu-id="e82b2-207">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="e82b2-208">応答を圧縮する</span><span class="sxs-lookup"><span data-stu-id="e82b2-208">Compress responses</span></span>

 <span data-ttu-id="e82b2-209">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-209">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="e82b2-210">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-210">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="e82b2-211">詳細については、「[応答の圧縮](xref:performance/response-compression)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-211">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="e82b2-212">最新の ASP.NET Core リリースを使用する</span><span class="sxs-lookup"><span data-stu-id="e82b2-212">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="e82b2-213">ASP.NET Core の新しいリリースにはそれぞれ、パフォーマンスが向上しています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-213">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="e82b2-214">.NET Core と ASP.NET Core での最適化により、新しいバージョンの方が以前のバージョンより高い値になります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-214">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="e82b2-215">たとえば、.NET Core 2.1 では、コンパイルされた正規表現と享受のサポートが[`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx)から追加されました。</span><span class="sxs-lookup"><span data-stu-id="e82b2-215">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="e82b2-216">ASP.NET Core 2.2 では、HTTP/2 のサポートが追加されました。</span><span class="sxs-lookup"><span data-stu-id="e82b2-216">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="e82b2-217">[ASP.NET Core 3.0](xref:aspnetcore-3.0)では、メモリ使用量を減らし、スループットを向上させる多くの機能強化が加えられています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-217">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="e82b2-218">パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-218">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="e82b2-219">例外を最小化する</span><span class="sxs-lookup"><span data-stu-id="e82b2-219">Minimize exceptions</span></span>

<span data-ttu-id="e82b2-220">例外はめったに発生しません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-220">Exceptions should be rare.</span></span> <span data-ttu-id="e82b2-221">例外のスローとキャッチは、他のコードフローパターンと比べて低速です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-221">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="e82b2-222">このため、通常のプログラムフローを制御するために例外を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-222">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="e82b2-223">推奨事項</span><span class="sxs-lookup"><span data-stu-id="e82b2-223">Recommendations:</span></span>

* <span data-ttu-id="e82b2-224">特に[ホットコードパス](#understand-hot-code-paths)では、通常のプログラムフローの手段として例外をスローまたはキャッチし**ない**ようにします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-224">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="e82b2-225">例外を発生させる条件を検出して処理するには、アプリにロジック**を含めます**。</span><span class="sxs-lookup"><span data-stu-id="e82b2-225">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="e82b2-226">例外的または予期しない条件の例外**をスローまた**はキャッチします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-226">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="e82b2-227">Application Insights などのアプリ診断ツールを使用すると、アプリケーションでのパフォーマンスに影響する可能性のある一般的な例外を識別できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-227">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="e82b2-228">パフォーマンスと信頼性</span><span class="sxs-lookup"><span data-stu-id="e82b2-228">Performance and reliability</span></span>

<span data-ttu-id="e82b2-229">次のセクションでは、パフォーマンスのヒントと既知の信頼性の問題と解決策について説明します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-229">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="e82b2-230">HttpRequest/Httpresponse.cache 本体で同期読み取りまたは書き込みを回避する</span><span class="sxs-lookup"><span data-stu-id="e82b2-230">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="e82b2-231">ASP.NET Core の IO はすべて非同期です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-231">All IO in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="e82b2-232">サーバーは、同期と非同期の両方のオーバーロードを持つ `Stream` インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-232">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="e82b2-233">スレッドプールのスレッドがブロックされないようにするために、非同期のものを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-233">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="e82b2-234">ブロックしているスレッドは、スレッドプールの枯渇につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-234">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="e82b2-235">**この**操作は避けてください。次の例では、<xref:System.IO.StreamReader.ReadToEnd*>を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-235">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="e82b2-236">このメソッドは、現在のスレッドが結果を待機するのをブロックします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-236">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="e82b2-237">非同期的な[同期](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-237">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="e82b2-238">上記のコードでは、`Get` は、HTTP 要求の本体全体をメモリに同期的に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-238">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="e82b2-239">クライアントが低速でアップロードしている場合、アプリは非同期で同期を実行しています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-239">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="e82b2-240">Kestrel では同期読み取りがサポート**されてい**ないため、アプリは非同期経由で同期します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-240">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="e82b2-241">次の**手順を実行します。** 次の例では <xref:System.IO.StreamReader.ReadToEndAsync*> を使用し、読み取り中にスレッドをブロックしません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-241">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="e82b2-242">上記のコードは、HTTP 要求本文全体を非同期的にメモリに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-242">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="e82b2-243">要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-243">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="e82b2-244">OOM を使用すると、サービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-244">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="e82b2-245">詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-245">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="e82b2-246">次の**手順を実行します。** 次の例は、バッファーされていない要求本文を使用する完全な非同期です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-246">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="e82b2-247">上のコードは、要求本文を非同期的にC#オブジェクトにシリアル化解除します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-247">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="e82b2-248">要求で ReadFormAsync を優先します。フォーム</span><span class="sxs-lookup"><span data-stu-id="e82b2-248">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="e82b2-249">`HttpContext.Request.ReadFormAsync` の代わりに `HttpContext.Request.Form` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-249">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="e82b2-250">`HttpContext.Request.Form` は、次の条件で安全に読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-250">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="e82b2-251">フォームは `ReadFormAsync`への呼び出しによって読み取られました。</span><span class="sxs-lookup"><span data-stu-id="e82b2-251">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="e82b2-252">キャッシュされたフォーム値は `HttpContext.Request.Form` を使用して読み取られています</span><span class="sxs-lookup"><span data-stu-id="e82b2-252">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="e82b2-253">**この**操作は避けてください。次の例では、`HttpContext.Request.Form`を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-253">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="e82b2-254">`HttpContext.Request.Form` では[async over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)が使用されるため、スレッドプールが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-254">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="e82b2-255">次の**手順を実行します。** 次の例では、`HttpContext.Request.ReadFormAsync` を使用してフォーム本文を非同期に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-255">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="e82b2-256">大きな要求本文または応答本文をメモリに読み込むことは避けてください</span><span class="sxs-lookup"><span data-stu-id="e82b2-256">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="e82b2-257">.NET では、85 KB を超えるすべてのオブジェクト割り当ては、大きなオブジェクトヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) で終了します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-257">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="e82b2-258">ラージオブジェクトは、次の2つの方法でコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-258">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="e82b2-259">新しく割り当てられたラージオブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-259">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="e82b2-260">CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-260">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="e82b2-261">LOH は、ヒープの残りの部分で収集されます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-261">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="e82b2-262">LOH には、フル[ガベージコレクション](/dotnet/standard/garbage-collection/fundamentals)または[Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-262">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="e82b2-263">この[ブログ投稿](https://adamsitnik.com/Array-Pool/#the-problem)では、問題について簡潔に説明しています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-263">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="e82b2-264">ラージオブジェクトが割り当てられると、ジェネレーション2のオブジェクトとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-264">When a large object is allocated, it’s marked as Gen 2 object.</span></span> <span data-ttu-id="e82b2-265">小さいオブジェクトの場合は Gen 0 として生成されません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-265">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="e82b2-266">結果として、LOH でメモリが不足していると、LOH だけでなく、マネージヒープ全体が GC によってクリーンアップされます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-266">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="e82b2-267">そのため、LOH を含む Gen 0、Gen 1、Gen 2 をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-267">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="e82b2-268">これはフルガベージコレクションと呼ばれ、最も時間のかかるガベージコレクションです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-268">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="e82b2-269">多くのアプリケーションでは、許容される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-269">For many applications, it can be acceptable.</span></span> <span data-ttu-id="e82b2-270">しかし、高パフォーマンスの web サーバーでは、平均的な web 要求を処理するために大量のメモリバッファーが必要になることはありません (ソケットからの読み取り、圧縮解除、JSON & のデコードなど)。</span><span class="sxs-lookup"><span data-stu-id="e82b2-270">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="e82b2-271">ネイティブは、大規模な要求または応答の本文を1つの `byte[]` または `string`に格納します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-271">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="e82b2-272">LOH の領域がすぐに不足する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-272">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="e82b2-273">完全な Gc が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-273">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="e82b2-274">同期データ処理 API を使用した作業</span><span class="sxs-lookup"><span data-stu-id="e82b2-274">Working with a synchronous data processing API</span></span>

<span data-ttu-id="e82b2-275">同期読み取りと書き込みのみをサポートするシリアライザー/デシリアライザーを使用する場合 (たとえば、 [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span><span class="sxs-lookup"><span data-stu-id="e82b2-275">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="e82b2-276">シリアライザー/デシリアライザーに渡す前に、データをメモリに非同期的にバッファーします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-276">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="e82b2-277">要求が大きい場合、メモリ不足 (OOM) 状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-277">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="e82b2-278">OOM を使用すると、サービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-278">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="e82b2-279">詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-279">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="e82b2-280">ASP.NET Core 3.0 では、JSON シリアル化のために既定で <xref:System.Text.Json> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-280">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="e82b2-281"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="e82b2-281"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="e82b2-282">非同期で JSON の読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="e82b2-282">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="e82b2-283">UTF-8 テキスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-283">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="e82b2-284">通常、`Newtonsoft.Json` よりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-284">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="e82b2-285">フィールドに IHttpContextAccessor を格納しない</span><span class="sxs-lookup"><span data-stu-id="e82b2-285">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="e82b2-286">[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)は、要求スレッドからアクセスされるときに、アクティブな要求の `HttpContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-286">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="e82b2-287">`IHttpContextAccessor.HttpContext` をフィールドまたは変数に格納することはでき**ません**。</span><span class="sxs-lookup"><span data-stu-id="e82b2-287">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="e82b2-288">**この**操作は避けてください。次の例では、フィールドに `HttpContext` を格納し、後でそれを使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-288">**Do not do this:** The following example stores the `HttpContext` in a field, and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="e82b2-289">上記のコードでは、コンストラクター内の null または正しくない `HttpContext` が頻繁にキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-289">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="e82b2-290">次の**手順を実行します。** 次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-290">**Do this:** The following example:</span></span>

* <span data-ttu-id="e82b2-291">フィールドに <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を格納します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-291">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="e82b2-292">は、正しい時刻に `HttpContext` フィールドを使用し、`null`を確認します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-292">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="e82b2-293">複数のスレッドから HttpContext にアクセスしない</span><span class="sxs-lookup"><span data-stu-id="e82b2-293">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="e82b2-294">`HttpContext` はスレッドセーフでは*ありません*。</span><span class="sxs-lookup"><span data-stu-id="e82b2-294">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="e82b2-295">複数のスレッドから並行して `HttpContext` にアクセスすると、ハング、クラッシュ、データ破損などの未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-295">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="e82b2-296">**この**操作は避けてください。次の例では、3つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-296">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="e82b2-297">要求パスは、複数のスレッドから同時にアクセスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-297">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="e82b2-298">次の**手順を実行します。** 次の例では、3つの並列要求を行う前に、受信要求からすべてのデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-298">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="e82b2-299">要求の完了後に HttpContext を使用しない</span><span class="sxs-lookup"><span data-stu-id="e82b2-299">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="e82b2-300">`HttpContext` は、ASP.NET Core パイプラインにアクティブな HTTP 要求がある限り有効です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-300">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="e82b2-301">ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-301">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="e82b2-302">このチェーンから返された `Task` が完了すると、`HttpContext` がリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-302">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="e82b2-303">**この**操作は避けてください。次の例では、最初の `await` に達したときに HTTP 要求が完了するように `async void` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-303">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="e82b2-304">これは、ASP.NET Core アプリでは**常**に不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-304">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="e82b2-305">HTTP 要求が完了した後、`HttpResponse` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-305">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="e82b2-306">プロセスをクラッシュさせる。</span><span class="sxs-lookup"><span data-stu-id="e82b2-306">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="e82b2-307">次の**手順を実行します。** 次の例では、アクションが完了するまで HTTP 要求が完了しないように、フレームワークに `Task` を返します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-307">**Do this:** The following example returns a `Task` to the framework so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="e82b2-308">バックグラウンドスレッドで HttpContext をキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="e82b2-308">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="e82b2-309">**この**操作は避けてください。次の例は、`Controller` プロパティから `HttpContext` をキャプチャすることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-309">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="e82b2-310">作業項目は次のようになる可能性があるため、この方法は不適切です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-310">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="e82b2-311">要求スコープの外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-311">Run outside of the request scope.</span></span>
* <span data-ttu-id="e82b2-312">間違った `HttpContext`を読み取ろうとしました。</span><span class="sxs-lookup"><span data-stu-id="e82b2-312">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="e82b2-313">次の**手順を実行します。** 次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-313">**Do this:** The following example:</span></span>

* <span data-ttu-id="e82b2-314">要求中にバックグラウンドタスクで必要なデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-314">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="e82b2-315">コントローラーから何も参照していません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-315">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

<span data-ttu-id="e82b2-316">バックグラウンドタスクはホステッドサービスとして実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-316">Background tasks should be implemented as hosted services.</span></span> <span data-ttu-id="e82b2-317">詳細については、「[Background tasks with hosted services](xref:fundamentals/host/hosted-services)」(ホストされるタスクを使用するバックグラウンド タスク) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-317">For more information, see [Background tasks with hosted services](xref:fundamentals/host/hosted-services).</span></span>

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="e82b2-318">バックグラウンドスレッドでコントローラーに挿入されたサービスをキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="e82b2-318">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="e82b2-319">**この**操作は避けてください。次の例は、`Controller` アクションパラメーターから `DbContext` をキャプチャすることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-319">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="e82b2-320">これは不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="e82b2-320">This is a bad practice.</span></span>  <span data-ttu-id="e82b2-321">この作業項目は、要求スコープの外部で実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-321">The work item could run outside of the request scope.</span></span> <span data-ttu-id="e82b2-322">`ContosoDbContext` は要求に対してスコープが設定され、`ObjectDisposedException`になります。</span><span class="sxs-lookup"><span data-stu-id="e82b2-322">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="e82b2-323">次の**手順を実行します。** 次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-323">**Do this:** The following example:</span></span>

* <span data-ttu-id="e82b2-324">バックグラウンド作業項目のスコープを作成するために <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> を挿入します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-324">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="e82b2-325">`IServiceScopeFactory` はシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="e82b2-325">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="e82b2-326">バックグラウンドスレッドで新しい依存関係挿入スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-326">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="e82b2-327">コントローラーから何も参照していません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-327">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="e82b2-328">は、受信要求から `ContosoDbContext` をキャプチャしません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-328">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="e82b2-329">次の強調表示されたコード。</span><span class="sxs-lookup"><span data-stu-id="e82b2-329">The following highlighted code:</span></span>

* <span data-ttu-id="e82b2-330">バックグラウンド操作の有効期間のスコープを作成し、そこからサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-330">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="e82b2-331">は、正しいスコープの `ContosoDbContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-331">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="e82b2-332">応答本文が開始された後に状態コードまたはヘッダーを変更しない</span><span class="sxs-lookup"><span data-stu-id="e82b2-332">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="e82b2-333">ASP.NET Core は、HTTP 応答の本文をバッファーしません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-333">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="e82b2-334">最初に応答が書き込まれたとき:</span><span class="sxs-lookup"><span data-stu-id="e82b2-334">The first time the response is written:</span></span>

* <span data-ttu-id="e82b2-335">ヘッダーは、本文のそのチャンクと共にクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-335">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="e82b2-336">応答ヘッダーを変更することはできなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e82b2-336">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="e82b2-337">**この**操作は避けてください。次のコードでは、応答が既に開始された後に応答ヘッダーを追加しようとしています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-337">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="e82b2-338">前のコードでは、`next()` が応答に書き込まれた場合、`context.Response.Headers["test"] = "test value";` は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="e82b2-338">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="e82b2-339">次の**手順を実行します。** 次の例では、ヘッダーを変更する前に、HTTP 応答が開始されたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-339">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="e82b2-340">次の**手順を実行します。** 次の例では、`HttpResponse.OnStarting` を使用して、応答ヘッダーをクライアントにフラッシュする前にヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-340">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="e82b2-341">応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="e82b2-341">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="e82b2-342">応答が開始されていないかどうかを確認しています:</span><span class="sxs-lookup"><span data-stu-id="e82b2-342">Checking if the response has not started:</span></span>

* <span data-ttu-id="e82b2-343">ヘッダーをジャストインタイムで追加またはオーバーライドする機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="e82b2-343">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="e82b2-344">では、パイプライン内の次のミドルウェアに関する知識は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e82b2-344">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="e82b2-345">応答本文への書き込みを既に開始している場合は、next () を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="e82b2-345">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="e82b2-346">コンポーネントは、応答を処理および操作できる場合にのみ呼び出されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="e82b2-346">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>
