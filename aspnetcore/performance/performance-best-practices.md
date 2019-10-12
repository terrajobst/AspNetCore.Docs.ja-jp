---
title: ASP.NET Core のパフォーマンスに関するベスト プラクティス
author: mjrousos
description: ASP.NET Core アプリでのパフォーマンスが向上し、一般的なパフォーマンスの問題を回避するためのヒント。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/26/2019
uid: performance/performance-best-practices
ms.openlocfilehash: 3484a0233a0d56811235192c4b64aa9296e72b58
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289068"
---
# <a name="aspnet-core-performance-best-practices"></a><span data-ttu-id="2fbd1-103">ASP.NET Core のパフォーマンスに関するベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="2fbd1-103">ASP.NET Core Performance Best Practices</span></span>

<span data-ttu-id="2fbd1-104">によって[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="2fbd1-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="2fbd1-105">この記事では、ASP.NET Core のパフォーマンスのベストプラクティスに関するガイドラインを示します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-105">This article provides guidelines for performance best practices with ASP.NET Core.</span></span>

## <a name="cache-aggressively"></a><span data-ttu-id="2fbd1-106">積極的にキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-106">Cache aggressively</span></span>

<span data-ttu-id="2fbd1-107">キャッシュは、このドキュメントの複数の部分で説明します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-107">Caching is discussed in several parts of this document.</span></span> <span data-ttu-id="2fbd1-108">詳細については、「 <xref:performance/caching/response> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-108">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="understand-hot-code-paths"></a><span data-ttu-id="2fbd1-109">ホットコードパスについて</span><span class="sxs-lookup"><span data-stu-id="2fbd1-109">Understand hot code paths</span></span>

<span data-ttu-id="2fbd1-110">このドキュメントでは、*ホットコードパス*は、頻繁に呼び出される、実行時間の大半が発生するコードパスとして定義されています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-110">In this document, a *hot code path* is defined as a code path that is frequently called and where much of the execution time occurs.</span></span> <span data-ttu-id="2fbd1-111">ホットコードパスは、通常、アプリのスケールアウトとパフォーマンスを制限し、このドキュメントのいくつかの部分で説明されています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-111">Hot code paths typically limit app scale-out and performance and are discussed in several parts of this document.</span></span>

## <a name="avoid-blocking-calls"></a><span data-ttu-id="2fbd1-112">呼び出しがブロックされないように</span><span class="sxs-lookup"><span data-stu-id="2fbd1-112">Avoid blocking calls</span></span>

<span data-ttu-id="2fbd1-113">ASP.NET Core アプリを設計すると、同時に多数の要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-113">ASP.NET Core apps should be designed to process many requests simultaneously.</span></span> <span data-ttu-id="2fbd1-114">非同期 Api を使用すると、呼び出しをブロックしていない待機して何千もの同時要求を処理するスレッドの小さなプール。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-114">Asynchronous APIs allow a small pool of threads to handle thousands of concurrent requests by not waiting on blocking calls.</span></span> <span data-ttu-id="2fbd1-115">実行時間の長い同期タスクが完了するを待機しているのではなく、スレッドは、別の要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-115">Rather than waiting on a long-running synchronous task to complete, the thread can work on another request.</span></span>

<span data-ttu-id="2fbd1-116">ASP.NET Core アプリで一般的なパフォーマンスの問題は、非同期可能性がある呼び出しをブロックしています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-116">A common performance problem in ASP.NET Core apps is blocking calls that could be asynchronous.</span></span> <span data-ttu-id="2fbd1-117">多くの同期ブロッキング呼び出しは、[スレッドプールの枯渇](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)と応答時間の低下につながります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-117">Many synchronous blocking calls lead to [Thread Pool starvation](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/) and degraded response times.</span></span>

<span data-ttu-id="2fbd1-118">**しない**:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-118">**Do not**:</span></span>

* <span data-ttu-id="2fbd1-119">呼び出すことによって、非同期実行をブロック[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)または[Task.Result](/dotnet/api/system.threading.tasks.task-1.result)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-119">Block asynchronous execution by calling [Task.Wait](/dotnet/api/system.threading.tasks.task.wait) or [Task.Result](/dotnet/api/system.threading.tasks.task-1.result).</span></span>
* <span data-ttu-id="2fbd1-120">一般的なコード パスでロックを取得します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-120">Acquire locks in common code paths.</span></span> <span data-ttu-id="2fbd1-121">ASP.NET Core アプリでは、最も効率的なコードを並列で実行するように構築する場合です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-121">ASP.NET Core apps are most performant when architected to run code in parallel.</span></span>

<span data-ttu-id="2fbd1-122">**行う**</span><span class="sxs-lookup"><span data-stu-id="2fbd1-122">**Do**:</span></span>

* <span data-ttu-id="2fbd1-123">ように[ホット コード パス](#understand-hot-code-paths)非同期です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-123">Make [hot code paths](#understand-hot-code-paths) asynchronous.</span></span>
* <span data-ttu-id="2fbd1-124">データ アクセスと実行時間の長い操作の Api を非同期的に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-124">Call data access and long-running operations APIs asynchronously.</span></span>
* <span data-ttu-id="2fbd1-125">コント ローラー/Razor ページのアクションを非同期にします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-125">Make controller/Razor Page actions asynchronous.</span></span> <span data-ttu-id="2fbd1-126">非同期[/await](/dotnet/csharp/programming-guide/concepts/async/)パターンを活用するために、呼び出し履歴全体が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-126">The entire call stack is asynchronous in order to benefit from [async/await](/dotnet/csharp/programming-guide/concepts/async/) patterns.</span></span>

<span data-ttu-id="2fbd1-127">[Perfview](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、[スレッドプール](/windows/desktop/procthread/thread-pools)に頻繁に追加されるスレッドを見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-127">A profiler, such as [PerfView](https://github.com/Microsoft/perfview), can be used to find threads frequently added to the [Thread Pool](/windows/desktop/procthread/thread-pools).</span></span> <span data-ttu-id="2fbd1-128">@No__t-0 イベントは、スレッドプールに追加されたスレッドを示します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-128">The `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` event indicates a thread added to the thread pool.</span></span> <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a><span data-ttu-id="2fbd1-129">ラージ オブジェクトの割り当てを最小限に抑える</span><span class="sxs-lookup"><span data-stu-id="2fbd1-129">Minimize large object allocations</span></span>

<span data-ttu-id="2fbd1-130">[.Net Core ガベージコレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリで自動的にメモリの割り当てと解放を管理します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-130">The [.NET Core garbage collector](/dotnet/standard/garbage-collection/) manages allocation and release of memory automatically in ASP.NET Core apps.</span></span> <span data-ttu-id="2fbd1-131">一般に、自動ガベージ コレクションは、開発者がメモリを解放する方法やタイミングについて心配する必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-131">Automatic garbage collection generally means that developers don't need to worry about how or when memory is freed.</span></span> <span data-ttu-id="2fbd1-132">ただし、未参照のオブジェクトのクリーンアップ時間がかかる CPU のため開発者は内のオブジェクトの割り当てを最小限に抑えて[ホット コード パス](#understand-hot-code-paths)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-132">However, cleaning up unreferenced objects takes CPU time, so developers should minimize allocating objects in [hot code paths](#understand-hot-code-paths).</span></span> <span data-ttu-id="2fbd1-133">ガベージ コレクションは、ラージ オブジェクト (> 85 K バイト) では特に高価です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-133">Garbage collection is especially expensive on large objects (> 85 K bytes).</span></span> <span data-ttu-id="2fbd1-134">大きなオブジェクトが格納されている、[大きなオブジェクト ヒープ](/dotnet/standard/garbage-collection/large-object-heap)完全 (第 2 世代) を必要とガベージ コレクションをクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-134">Large objects are stored on the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) and require a full (generation 2) garbage collection to clean up.</span></span> <span data-ttu-id="2fbd1-135">ジェネレーション0およびジェネレーション1のコレクションとは異なり、ジェネレーション2のコレクションでは、アプリの実行を一時的に中断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-135">Unlike generation 0 and generation 1 collections, a generation 2 collection requires a temporary suspension of app execution.</span></span> <span data-ttu-id="2fbd1-136">頻繁に割り当てと大きなオブジェクトの割り当てを解除することによってパフォーマンスの一貫性のない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-136">Frequent allocation and de-allocation of large objects can cause inconsistent performance.</span></span>

<span data-ttu-id="2fbd1-137">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-137">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-138">**行う** よく使われる大規模なオブジェクトのキャッシュを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-138">**Do** consider caching large objects that are frequently used.</span></span> <span data-ttu-id="2fbd1-139">ラージ オブジェクトをキャッシュには、高価な割り当てができないようにします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-139">Caching large objects prevents expensive allocations.</span></span>
* <span data-ttu-id="2fbd1-140">**行う** を使用してバッファーをプールする[ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1)大きな配列を格納します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-140">**Do** pool buffers by using an [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1) to store large arrays.</span></span>
* <span data-ttu-id="2fbd1-141">**しない**に割り当てる大きなオブジェクトの有効期間が短い、[ホット コード パス](#understand-hot-code-paths)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-141">**Do not** allocate many, short-lived large objects on [hot code paths](#understand-hot-code-paths).</span></span>

<span data-ttu-id="2fbd1-142">前述のようなメモリの問題は、 [Perfview](https://github.com/Microsoft/perfview)でガベージコレクション (GC) の統計を確認して調べることによって診断できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-142">Memory issues, such as the preceding, can be diagnosed by reviewing garbage collection (GC) stats in [PerfView](https://github.com/Microsoft/perfview) and examining:</span></span>

* <span data-ttu-id="2fbd1-143">ガベージ コレクションの一時停止時間。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-143">Garbage collection pause time.</span></span>
* <span data-ttu-id="2fbd1-144">プロセッサ時間の割合は、ガベージ コレクションに費やされました。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-144">What percentage of the processor time is spent in garbage collection.</span></span>
* <span data-ttu-id="2fbd1-145">ガベージ コレクションの数とは、世代 0、1、および 2 です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-145">How many garbage collections are generation 0, 1, and 2.</span></span>

<span data-ttu-id="2fbd1-146">詳細については、次を参照してください。[ガベージ コレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-146">For more information, see [Garbage Collection and Performance](/dotnet/standard/garbage-collection/performance).</span></span>

## <a name="optimize-data-access"></a><span data-ttu-id="2fbd1-147">データ アクセスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-147">Optimize Data Access</span></span>

<span data-ttu-id="2fbd1-148">多くの場合、データストアやその他のリモートサービスとのやり取りは、ASP.NET Core アプリの最も低速な部分です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-148">Interactions with a data store and other remote services are often the slowest parts of an ASP.NET Core app.</span></span> <span data-ttu-id="2fbd1-149">データの読み書きを効率的には、良好なパフォーマンスにとって重要です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-149">Reading and writing data efficiently is critical for good performance.</span></span>

<span data-ttu-id="2fbd1-150">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-150">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-151">**行う** すべてのデータ アクセス Api を非同期的に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-151">**Do** call all data access APIs asynchronously.</span></span>
* <span data-ttu-id="2fbd1-152">**しない**は必要以上のデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-152">**Do not** retrieve more data than is necessary.</span></span> <span data-ttu-id="2fbd1-153">現在の HTTP 要求に必要なデータだけを返すクエリを記述します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-153">Write queries to return just the data that's necessary for the current HTTP request.</span></span>
* <span data-ttu-id="2fbd1-154">**行う**若干古いデータが許容される場合は、データベースやリモート サービスから取得されたデータをアクセス頻繁にキャッシュを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-154">**Do** consider caching frequently accessed data retrieved from a database or remote service if slightly out-of-date data is acceptable.</span></span> <span data-ttu-id="2fbd1-155">シナリオに応じて、 [Memorycache](xref:performance/caching/memory)または[microsoft.web.distributedcache](xref:performance/caching/distributed)を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-155">Depending on the scenario, use a [MemoryCache](xref:performance/caching/memory) or a [DistributedCache](xref:performance/caching/distributed).</span></span> <span data-ttu-id="2fbd1-156">詳細については、「 <xref:performance/caching/response> 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-156">For more information, see <xref:performance/caching/response>.</span></span>
* <span data-ttu-id="2fbd1-157">**行う**を最小限に抑えるネットワーク ラウンド トリップします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-157">**Do** minimize network round trips.</span></span> <span data-ttu-id="2fbd1-158">目的は、複数の呼び出しではなく、1回の呼び出しで必要なデータを取得することです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-158">The goal is to retrieve the required data in a single call rather than several calls.</span></span>
* <span data-ttu-id="2fbd1-159">**行う** 使用[追跡なしのクエリ](/ef/core/querying/tracking#no-tracking-queries)読み取り専用の目的でデータにアクセスするときに、Entity Framework Core でします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-159">**Do** use [no-tracking queries](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core when accessing data for read-only purposes.</span></span> <span data-ttu-id="2fbd1-160">EF Core より効率的に追跡なしのクエリの結果を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-160">EF Core can return the results of no-tracking queries more efficiently.</span></span>
* <span data-ttu-id="2fbd1-161">**行う**フィルターと集計の LINQ クエリ (で`.Where`、 `.Select`、または`.Sum`ステートメントなどの)、フィルター処理がデータベースで実行できるようにします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-161">**Do** filter and aggregate LINQ queries (with `.Where`, `.Select`, or `.Sum` statements, for example) so that the filtering is performed by the database.</span></span>
* <span data-ttu-id="2fbd1-162">**行う** EF Core に、クライアントは、非効率的なクエリの実行につながる可能性のいくつかのクエリ演算子が解決されることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-162">**Do** consider that EF Core resolves some query operators on the client, which may lead to inefficient query execution.</span></span> <span data-ttu-id="2fbd1-163">詳細については、「[クライアント評価のパフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-163">For more information, see [Client evaluation performance issues](/ef/core/querying/client-eval#client-evaluation-performance-issues).</span></span>
* <span data-ttu-id="2fbd1-164">**しない**プロジェクション クエリを使用して、"n+1"を実行するがこのコレクションは、上の SQL クエリ。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-164">**Do not** use projection queries on collections, which can result in executing "N + 1" SQL queries.</span></span> <span data-ttu-id="2fbd1-165">詳細については、次を参照してください。[相関サブクエリの最適化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-165">For more information, see [Optimization of correlated subqueries](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).</span></span>

<span data-ttu-id="2fbd1-166">参照してください[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)のための手法を高スケールのアプリでのパフォーマンスを向上させる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-166">See [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) for approaches that may improve performance in high-scale apps:</span></span>

* [<span data-ttu-id="2fbd1-167">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="2fbd1-167">DbContext pooling</span></span>](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [<span data-ttu-id="2fbd1-168">明示的にコンパイルされたクエリ</span><span class="sxs-lookup"><span data-stu-id="2fbd1-168">Explicitly compiled queries</span></span>](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

<span data-ttu-id="2fbd1-169">コードベースをコミットする前に、上記の高パフォーマンスアプローチの影響を測定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-169">We recommend measuring the impact of the preceding high-performance approaches before committing the code base.</span></span> <span data-ttu-id="2fbd1-170">コンパイル済みクエリの複雑さを増加は、パフォーマンスの向上を見合わない場合があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-170">The additional complexity of compiled queries may not justify the performance improvement.</span></span>

<span data-ttu-id="2fbd1-171">[Application Insights](/azure/application-insights/app-insights-overview)またはプロファイリングツールでデータにアクセスするために費やされた時間を確認することで、クエリの問題を検出できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-171">Query issues can be detected by reviewing the time spent accessing data with [Application Insights](/azure/application-insights/app-insights-overview) or with profiling tools.</span></span> <span data-ttu-id="2fbd1-172">ほとんどのデータベースも利用できる統計をに関する頻繁に実行されるクエリ。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-172">Most databases also make statistics available concerning frequently executed queries.</span></span>

## <a name="pool-http-connections-with-httpclientfactory"></a><span data-ttu-id="2fbd1-173">HttpClientFactory との接続をプール HTTP</span><span class="sxs-lookup"><span data-stu-id="2fbd1-173">Pool HTTP connections with HttpClientFactory</span></span>

<span data-ttu-id="2fbd1-174">[Httpclient](/dotnet/api/system.net.http.httpclient)は `IDisposable` インターフェイスを実装していますが、再利用できるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-174">Although [HttpClient](/dotnet/api/system.net.http.httpclient) implements the `IDisposable` interface, it's designed for reuse.</span></span> <span data-ttu-id="2fbd1-175">閉じられた`HttpClient`ソケットのままで開いているインスタンス、`TIME_WAIT`短時間の状態。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-175">Closed `HttpClient` instances leave sockets open in the `TIME_WAIT` state for a short period of time.</span></span> <span data-ttu-id="2fbd1-176">@No__t 0 オブジェクトを作成および破棄するコードパスが頻繁に使用される場合、アプリは使用可能なソケットを消費する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-176">If a code path that creates and disposes of `HttpClient` objects is frequently used, the app may exhaust available sockets.</span></span> <span data-ttu-id="2fbd1-177">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)この問題をソリューションとしての ASP.NET Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-177">[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) was introduced in ASP.NET Core 2.1 as a solution to this problem.</span></span> <span data-ttu-id="2fbd1-178">パフォーマンスと信頼性を最適化するためにプールの HTTP 接続を処理します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-178">It handles pooling HTTP connections to optimize performance and reliability.</span></span>

<span data-ttu-id="2fbd1-179">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-179">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-180">**行う**の作成し、破棄の`HttpClient`直接インスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-180">**Do not** create and dispose of `HttpClient` instances directly.</span></span>
* <span data-ttu-id="2fbd1-181">**行う** 使用[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を取得する`HttpClient`インスタンス。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-181">**Do** use [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) to retrieve `HttpClient` instances.</span></span> <span data-ttu-id="2fbd1-182">詳細については、次を参照してください。[回復力のある HTTP 要求を実装するために使用 HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-182">For more information, see [Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).</span></span>

## <a name="keep-common-code-paths-fast"></a><span data-ttu-id="2fbd1-183">高速の一般的なコード パスを維持します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-183">Keep common code paths fast</span></span>

<span data-ttu-id="2fbd1-184">すべてのコードが高速になるようにするには、コードパスと呼ばれることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-184">You want all of your code to be fast, frequently called code paths are the most critical to optimize:</span></span>

* <span data-ttu-id="2fbd1-185">アプリの要求処理パイプラインでミドルウェア コンポーネント、特にミドルウェアはパイプラインの早い段階で実行します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-185">Middleware components in the app's request processing pipeline, especially middleware run early in the pipeline.</span></span> <span data-ttu-id="2fbd1-186">これらのコンポーネントは、パフォーマンスに大きな影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-186">These components have a large impact on performance.</span></span>
* <span data-ttu-id="2fbd1-187">要求ごとに、または要求ごとに複数回実行されるコード。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-187">Code that's executed for every request or multiple times per request.</span></span> <span data-ttu-id="2fbd1-188">たとえば、カスタム ログ、承認ハンドラー、または一時的なサービスの初期化にします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-188">For example, custom logging, authorization handlers, or initialization of transient services.</span></span>

<span data-ttu-id="2fbd1-189">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-189">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-190">**しない**実行時間の長いタスクでカスタムのミドルウェア コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-190">**Do not** use custom middleware components with long-running tasks.</span></span>
* <span data-ttu-id="2fbd1-191">**行う**パフォーマンス プロファイリング ツールなどを使用して、 [Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)または[PerfView](https://github.com/Microsoft/perfview)) を識別[ホット コード パス](#understand-hot-code-paths)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-191">**Do** use performance profiling tools, such as [Visual Studio Diagnostic Tools](/visualstudio/profiling/profiling-feature-tour) or [PerfView](https://github.com/Microsoft/perfview)), to identify [hot code paths](#understand-hot-code-paths).</span></span>

## <a name="complete-long-running-tasks-outside-of-http-requests"></a><span data-ttu-id="2fbd1-192">HTTP 要求の外部で長時間タスクを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-192">Complete long-running Tasks outside of HTTP requests</span></span>

<span data-ttu-id="2fbd1-193">コント ローラーまたはページ モデルに必要なサービスを呼び出すと、HTTP 応答を返すことによって、ASP.NET Core アプリにほとんどの要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-193">Most requests to an ASP.NET Core app can be handled by a controller or page model calling necessary services and returning an HTTP response.</span></span> <span data-ttu-id="2fbd1-194">要求実行時間の長いタスクに関連するいくつかの場合は、全体の要求-応答プロセスを非同期にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-194">For some requests that involve long-running tasks, it's better to make the entire request-response process asynchronous.</span></span>

<span data-ttu-id="2fbd1-195">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-195">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-196">**しない**実行時間の長いタスクが通常の HTTP 要求の処理の一部として完了するまで待ちます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-196">**Do not** wait for long-running tasks to complete as part of ordinary HTTP request processing.</span></span>
* <span data-ttu-id="2fbd1-197">**行う** での実行時間の長い要求の処理を検討してください[バック グラウンド サービス](xref:fundamentals/host/hosted-services)またはアウト プロセスで、 [Azure 関数](/azure/azure-functions/)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-197">**Do** consider handling long-running requests with [background services](xref:fundamentals/host/hosted-services) or out of process with an [Azure Function](/azure/azure-functions/).</span></span> <span data-ttu-id="2fbd1-198">作業のアウト プロセスの完了は、CPU を消費するタスクに特に有益です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-198">Completing work out-of-process is especially beneficial for CPU-intensive tasks.</span></span>
* <span data-ttu-id="2fbd1-199">**行う**などのリアルタイム通信のオプションを使用して[SignalR](xref:signalr/introduction)クライアントと非同期的に通信します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-199">**Do** use real-time communication options, such as [SignalR](xref:signalr/introduction), to communicate with clients asynchronously.</span></span>

## <a name="minify-client-assets"></a><span data-ttu-id="2fbd1-200">クライアントの資産を縮小します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-200">Minify client assets</span></span>

<span data-ttu-id="2fbd1-201">複雑なフロント エンドを使用した ASP.NET Core アプリは、多くの JavaScript、CSS、またはイメージ ファイルを頻繁に機能します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-201">ASP.NET Core apps with complex front-ends frequently serve many JavaScript, CSS, or image files.</span></span> <span data-ttu-id="2fbd1-202">により、初期読み込み要求のパフォーマンスを向上できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-202">Performance of initial load requests can be improved by:</span></span>

* <span data-ttu-id="2fbd1-203">1 つに複数のファイルを結合するバンドル化します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-203">Bundling, which combines multiple files into one.</span></span>
* <span data-ttu-id="2fbd1-204">縮小。空白とコメントを削除することによってファイルのサイズを縮小します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-204">Minifying, which reduces the size of files by removing whitespace and comments.</span></span>

<span data-ttu-id="2fbd1-205">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-205">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-206">**行う** で ASP.NET Core の使用[組み込みサポート](xref:client-side/bundling-and-minification)バンドルと縮小クライアント資産。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-206">**Do** use ASP.NET Core's [built-in support](xref:client-side/bundling-and-minification) for bundling and minifying client assets.</span></span>
* <span data-ttu-id="2fbd1-207">**行う**などその他のサード パーティ製ツールを検討してください[Webpack](https://webpack.js.org/)、複雑なクライアント資産管理のためです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-207">**Do** consider other third-party tools, such as [Webpack](https://webpack.js.org/), for complex client asset management.</span></span>

## <a name="compress-responses"></a><span data-ttu-id="2fbd1-208">応答を圧縮する</span><span class="sxs-lookup"><span data-stu-id="2fbd1-208">Compress responses</span></span>

 <span data-ttu-id="2fbd1-209">通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-209">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="2fbd1-210">ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-210">One way to reduce payload sizes is to compress an app's responses.</span></span> <span data-ttu-id="2fbd1-211">詳細については、「[応答の圧縮](xref:performance/response-compression)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-211">For more information, see [Response compression](xref:performance/response-compression).</span></span>

## <a name="use-the-latest-aspnet-core-release"></a><span data-ttu-id="2fbd1-212">ASP.NET Core の最新のリリースを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-212">Use the latest ASP.NET Core release</span></span>

<span data-ttu-id="2fbd1-213">ASP.NET Core の新しいリリースにはそれぞれ、パフォーマンスが向上しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-213">Each new release of ASP.NET Core includes performance improvements.</span></span> <span data-ttu-id="2fbd1-214">.NET Core と ASP.NET Core での最適化により、新しいバージョンの方が以前のバージョンより高い値になります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-214">Optimizations in .NET Core and ASP.NET Core mean that newer versions generally outperform older versions.</span></span> <span data-ttu-id="2fbd1-215">.NET Core 2.1 にサポートが追加されてコンパイルされる正規表現との恩恵をたとえば、 [ `Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-215">For example, .NET Core 2.1 added support for compiled regular expressions and benefitted from [`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx).</span></span> <span data-ttu-id="2fbd1-216">Http/2 のサポートを ASP.NET Core 2.2 を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-216">ASP.NET Core 2.2 added support for HTTP/2.</span></span> <span data-ttu-id="2fbd1-217">[ASP.NET Core 3.0](xref:aspnetcore-3.0)では、メモリ使用量を減らし、スループットを向上させる多くの機能強化が加えられています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-217">[ASP.NET Core 3.0 adds many improvements](xref:aspnetcore-3.0) that reduce memory usage and improve throughput.</span></span> <span data-ttu-id="2fbd1-218">パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-218">If performance is a priority, consider upgrading to the current version of ASP.NET Core.</span></span>

## <a name="minimize-exceptions"></a><span data-ttu-id="2fbd1-219">例外を最小限に抑える</span><span class="sxs-lookup"><span data-stu-id="2fbd1-219">Minimize exceptions</span></span>

<span data-ttu-id="2fbd1-220">例外は、まれである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-220">Exceptions should be rare.</span></span> <span data-ttu-id="2fbd1-221">スローして、例外のキャッチは、他のコード フロー パターンを基準と遅いです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-221">Throwing and catching exceptions is slow relative to other code flow patterns.</span></span> <span data-ttu-id="2fbd1-222">このため、通常のプログラムフローを制御するために例外を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-222">Because of this, exceptions shouldn't be used to control normal program flow.</span></span>

<span data-ttu-id="2fbd1-223">推奨事項:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-223">Recommendations:</span></span>

* <span data-ttu-id="2fbd1-224">特に[ホットコードパス](#understand-hot-code-paths)では、通常のプログラムフローの手段として例外をスローまたはキャッチし**ない**ようにします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-224">**Do not** use throwing or catching exceptions as a means of normal program flow, especially in [hot code paths](#understand-hot-code-paths).</span></span>
* <span data-ttu-id="2fbd1-225">**行う** ロジックを検出して例外の原因となる条件を処理するアプリケーションに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-225">**Do** include logic in the app to detect and handle conditions that would cause an exception.</span></span>
* <span data-ttu-id="2fbd1-226">**行う** スローまたは異常なまたは予期しない条件の例外をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-226">**Do** throw or catch exceptions for unusual or unexpected conditions.</span></span>

<span data-ttu-id="2fbd1-227">Application Insights などのアプリ診断ツールを使用すると、アプリケーションでのパフォーマンスに影響する可能性のある一般的な例外を識別できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-227">App diagnostic tools, such as Application Insights, can help to identify common exceptions in an app that may affect performance.</span></span>

## <a name="performance-and-reliability"></a><span data-ttu-id="2fbd1-228">パフォーマンスと信頼性</span><span class="sxs-lookup"><span data-stu-id="2fbd1-228">Performance and reliability</span></span>

<span data-ttu-id="2fbd1-229">次のセクションでは、パフォーマンスのヒントと既知の信頼性の問題と解決策について説明します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-229">The following sections provide performance tips and known reliability problems and solutions.</span></span>

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a><span data-ttu-id="2fbd1-230">HttpRequest/Httpresponse.cache 本体で同期読み取りまたは書き込みを回避する</span><span class="sxs-lookup"><span data-stu-id="2fbd1-230">Avoid synchronous read or write on HttpRequest/HttpResponse body</span></span>

<span data-ttu-id="2fbd1-231">ASP.NET Core の IO はすべて非同期です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-231">All IO in ASP.NET Core is asynchronous.</span></span> <span data-ttu-id="2fbd1-232">サーバーは、同期と非同期の両方のオーバーロードを持つ @no__t 0 インターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-232">Servers implement the `Stream` interface, which has both synchronous and asynchronous overloads.</span></span> <span data-ttu-id="2fbd1-233">スレッドプールのスレッドがブロックされないようにするために、非同期のものを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-233">The asynchronous ones should be preferred to avoid blocking thread pool threads.</span></span> <span data-ttu-id="2fbd1-234">ブロックしているスレッドは、スレッドプールの枯渇につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-234">Blocking threads can lead to thread pool starvation.</span></span>

<span data-ttu-id="2fbd1-235">**この操作は避けてください。** <xref:System.IO.StreamReader.ReadToEnd*> を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-235">**Do not do this:** The following example uses the <xref:System.IO.StreamReader.ReadToEnd*>.</span></span> <span data-ttu-id="2fbd1-236">このメソッドは、現在のスレッドが結果を待機するのをブロックします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-236">It blocks the current thread to wait for the result.</span></span> <span data-ttu-id="2fbd1-237">これは、async @ no__t 経由の [sync の例です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-237">This is an example of [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

<span data-ttu-id="2fbd1-238">前のコードでは、`Get` は、HTTP 要求の本体全体をメモリに同期的に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-238">In the preceding code, `Get` synchronously reads the entire HTTP request body into memory.</span></span> <span data-ttu-id="2fbd1-239">クライアントが低速でアップロードしている場合、アプリは非同期で同期を実行しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-239">If the client is slowly uploading, the app is doing sync over async.</span></span> <span data-ttu-id="2fbd1-240">Kestrel では同期読み取りがサポート**されてい**ないため、アプリは非同期経由で同期します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-240">The app does sync over async because Kestrel does **NOT** support synchronous reads.</span></span>

<span data-ttu-id="2fbd1-241">**次の手順を実行します。** 次の例では <xref:System.IO.StreamReader.ReadToEndAsync*> を使用し、読み取り中にスレッドをブロックしません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-241">**Do this:** The following example uses <xref:System.IO.StreamReader.ReadToEndAsync*> and does not block the thread while reading.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

<span data-ttu-id="2fbd1-242">上記のコードは、HTTP 要求本文全体を非同期的にメモリに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-242">The preceding code asynchronously reads the entire HTTP request body into memory.</span></span>

> [!WARNING]
> <span data-ttu-id="2fbd1-243">要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-243">If the request is large, reading the entire HTTP request body into memory could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="2fbd1-244">OOM を使用すると、サービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-244">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="2fbd1-245">詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-245">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="2fbd1-246">**次の手順を実行します。** 次の例は、バッファーされていない要求本文を使用する完全な非同期です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-246">**Do this:** The following example is fully asynchronous using a non buffered request body:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

<span data-ttu-id="2fbd1-247">上のコードは、要求本文を非同期的にC#オブジェクトにシリアル化解除します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-247">The preceding code asynchronously de-serializes the request body into a C# object.</span></span>

## <a name="prefer-readformasync-over-requestform"></a><span data-ttu-id="2fbd1-248">要求で ReadFormAsync を優先します。フォーム</span><span class="sxs-lookup"><span data-stu-id="2fbd1-248">Prefer ReadFormAsync over Request.Form</span></span>

<span data-ttu-id="2fbd1-249">`HttpContext.Request.ReadFormAsync` の代わりに `HttpContext.Request.Form`を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-249">Use `HttpContext.Request.ReadFormAsync` instead of `HttpContext.Request.Form`.</span></span>
<span data-ttu-id="2fbd1-250">`HttpContext.Request.Form` は、次の条件で安全に読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-250">`HttpContext.Request.Form` can be safely read only with the following conditions:</span></span>

* <span data-ttu-id="2fbd1-251">フォームは `ReadFormAsync` への呼び出しによって読み取られました。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-251">The form has been read by a call to `ReadFormAsync`, and</span></span>
* <span data-ttu-id="2fbd1-252">キャッシュされたフォーム値は `HttpContext.Request.Form` を使用して読み取られています</span><span class="sxs-lookup"><span data-stu-id="2fbd1-252">The cached form value is being read using `HttpContext.Request.Form`</span></span>

<span data-ttu-id="2fbd1-253">**この操作は避けてください。** 次の例では `HttpContext.Request.Form` を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-253">**Do not do this:** The following example uses `HttpContext.Request.Form`.</span></span>  <span data-ttu-id="2fbd1-254">`HttpContext.Request.Form` は、async @ no__t で [sync を使用します。これにより、スレッドプールが枯渇する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-254">`HttpContext.Request.Form` uses [sync over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) and can lead to thread pool starvation.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

<span data-ttu-id="2fbd1-255">**次の手順を実行します。** 次の例では、`HttpContext.Request.ReadFormAsync` を使用してフォーム本文を非同期に読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-255">**Do this:** The following example uses `HttpContext.Request.ReadFormAsync` to read the form body asynchronously.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a><span data-ttu-id="2fbd1-256">大きな要求本文または応答本文をメモリに読み込むことは避けてください</span><span class="sxs-lookup"><span data-stu-id="2fbd1-256">Avoid reading large request bodies or response bodies into memory</span></span>

<span data-ttu-id="2fbd1-257">.NET では、85 KB を超えるすべてのオブジェクト割り当ては、大きなオブジェクトヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) で終了します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-257">In .NET, every object allocation greater than 85 KB ends up in the large object heap ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)).</span></span> <span data-ttu-id="2fbd1-258">ラージオブジェクトは、次の2つの方法でコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-258">Large objects are expensive in two ways:</span></span>

* <span data-ttu-id="2fbd1-259">新しく割り当てられたラージオブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-259">The allocation cost is high because the memory for a newly allocated large object has to be cleared.</span></span> <span data-ttu-id="2fbd1-260">CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-260">The CLR guarantees that memory for all newly allocated objects is cleared.</span></span>
* <span data-ttu-id="2fbd1-261">LOH は、ヒープの残りの部分で収集されます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-261">LOH is collected with the rest of the heap.</span></span> <span data-ttu-id="2fbd1-262">LOH には、フル[ガベージコレクション](/dotnet/standard/garbage-collection/fundamentals)または[Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-262">LOH requires a full [garbage collection](/dotnet/standard/garbage-collection/fundamentals) or [Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations).</span></span>

<span data-ttu-id="2fbd1-263">この[ブログ投稿](https://adamsitnik.com/Array-Pool/#the-problem)では、問題について簡潔に説明しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-263">This [blog post](https://adamsitnik.com/Array-Pool/#the-problem) describes the problem succinctly:</span></span>

> <span data-ttu-id="2fbd1-264">ラージオブジェクトが割り当てられると、ジェネレーション2のオブジェクトとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-264">When a large object is allocated, it’s marked as Gen 2 object.</span></span> <span data-ttu-id="2fbd1-265">小さいオブジェクトの場合は Gen 0 として生成されません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-265">Not Gen 0 as for small objects.</span></span> <span data-ttu-id="2fbd1-266">結果として、LOH でメモリが不足していると、LOH だけでなく、マネージヒープ全体が GC によってクリーンアップされます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-266">The consequences are that if you run out of memory in LOH, GC cleans up the whole managed heap, not only LOH.</span></span> <span data-ttu-id="2fbd1-267">そのため、LOH を含む Gen 0、Gen 1、Gen 2 をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-267">So it cleans up Gen 0, Gen 1 and Gen 2 including LOH.</span></span> <span data-ttu-id="2fbd1-268">これはフルガベージコレクションと呼ばれ、最も時間のかかるガベージコレクションです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-268">This is called full garbage collection and is the most time-consuming garbage collection.</span></span> <span data-ttu-id="2fbd1-269">多くのアプリケーションでは、許容される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-269">For many applications, it can be acceptable.</span></span> <span data-ttu-id="2fbd1-270">しかし、高パフォーマンスの web サーバーでは、平均的な web 要求を処理するために大量のメモリバッファーが必要になることはありません (ソケットからの読み取り、圧縮解除、JSON & のデコードなど)。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-270">But definitely not for high-performance web servers, where few big memory buffers are needed to handle an average web request (read from a socket, decompress, decode JSON & more).</span></span>

<span data-ttu-id="2fbd1-271">ネイティブは、大規模な要求または応答の本文を1つの `byte[]` または `string` に格納します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-271">Naively storing a large request or response body into a single `byte[]` or `string`:</span></span>

* <span data-ttu-id="2fbd1-272">LOH の領域がすぐに不足する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-272">May result in quickly running out of space in the LOH.</span></span>
* <span data-ttu-id="2fbd1-273">完全な Gc が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-273">May cause performance issues for the app because of full GCs running.</span></span>

## <a name="working-with-a-synchronous-data-processing-api"></a><span data-ttu-id="2fbd1-274">同期データ処理 API を使用した作業</span><span class="sxs-lookup"><span data-stu-id="2fbd1-274">Working with a synchronous data processing API</span></span>

<span data-ttu-id="2fbd1-275">同期読み取りと書き込みのみをサポートするシリアライザー/デシリアライザーを使用する場合 (たとえば、 [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span><span class="sxs-lookup"><span data-stu-id="2fbd1-275">When using a serializer/de-serializer that only supports synchronous reads and writes (for example,  [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):</span></span>

* <span data-ttu-id="2fbd1-276">シリアライザー/デシリアライザーに渡す前に、データをメモリに非同期的にバッファーします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-276">Buffer the data into memory asynchronously before passing it into the serializer/de-serializer.</span></span>

> [!WARNING]
> <span data-ttu-id="2fbd1-277">要求が大きい場合、メモリ不足 (OOM) 状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-277">If the request is large, it could lead to an out of memory (OOM) condition.</span></span> <span data-ttu-id="2fbd1-278">OOM を使用すると、サービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-278">OOM can result in a Denial Of Service.</span></span>  <span data-ttu-id="2fbd1-279">詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-279">For more information, see [Avoid reading large request bodies or response bodies into memory](#arlb) in this document.</span></span>

<span data-ttu-id="2fbd1-280">ASP.NET Core 3.0 では、JSON シリアル化に対して既定で <xref:System.Text.Json> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-280">ASP.NET Core 3.0 uses <xref:System.Text.Json> by default for JSON serialization.</span></span> <span data-ttu-id="2fbd1-281"><xref:System.Text.Json>:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-281"><xref:System.Text.Json>:</span></span>

* <span data-ttu-id="2fbd1-282">非同期で JSON の読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-282">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="2fbd1-283">UTF-8 テキスト用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-283">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="2fbd1-284">通常、`Newtonsoft.Json` よりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-284">Typically higher performance than `Newtonsoft.Json`.</span></span>

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a><span data-ttu-id="2fbd1-285">フィールドに IHttpContextAccessor を格納しない</span><span class="sxs-lookup"><span data-stu-id="2fbd1-285">Do not store IHttpContextAccessor.HttpContext in a field</span></span>

<span data-ttu-id="2fbd1-286">[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)は、要求スレッドからアクセスされるときに、アクティブな要求の `HttpContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-286">The [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) returns the `HttpContext` of the active request when accessed from the request thread.</span></span> <span data-ttu-id="2fbd1-287">@No__t-0 は、フィールドまたは変数には格納でき**ません**。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-287">The `IHttpContextAccessor.HttpContext` should **not** be stored in a field or variable.</span></span>

<span data-ttu-id="2fbd1-288">**この操作は避けてください。** 次の例では、フィールドに `HttpContext` を格納し、後でそれを使用しようとします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-288">**Do not do this:** The following example stores the `HttpContext` in a field, and then attempts to use it later.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

<span data-ttu-id="2fbd1-289">上記のコードでは、コンストラクターで null または正しくない @no__t 0 がキャプチャされることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-289">The preceding code frequently captures a null or incorrect `HttpContext` in the constructor.</span></span>

<span data-ttu-id="2fbd1-290">**次の手順を実行します。** 次のような例です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-290">**Do this:** The following example:</span></span>

* <span data-ttu-id="2fbd1-291">フィールドに @no__t 0 を格納します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-291">Stores the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in a field.</span></span>
* <span data-ttu-id="2fbd1-292">は、正しい時刻に `HttpContext` フィールドを使用し、`null` を確認します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-292">Uses the `HttpContext` field at the correct time and checks for `null`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a><span data-ttu-id="2fbd1-293">複数のスレッドから HttpContext にアクセスしない</span><span class="sxs-lookup"><span data-stu-id="2fbd1-293">Do not access HttpContext from multiple threads</span></span>

<span data-ttu-id="2fbd1-294">`HttpContext` はスレッドセーフでは*ありません*。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-294">`HttpContext` is *NOT* thread-safe.</span></span> <span data-ttu-id="2fbd1-295">複数のスレッドから `HttpContext` に同時にアクセスすると、ハング、クラッシュ、データ破損などの未定義の動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-295">Accessing `HttpContext` from multiple threads in parallel can result in undefined behavior such as hangs, crashes, and data corruption.</span></span>

<span data-ttu-id="2fbd1-296">**この操作は避けてください。** 次の例では、3つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-296">**Do not do this:** The following example makes three parallel requests and logs the incoming request path before and after the outgoing HTTP request.</span></span> <span data-ttu-id="2fbd1-297">要求パスは、複数のスレッドから同時にアクセスされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-297">The request path is accessed from multiple threads, potentially in parallel.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

<span data-ttu-id="2fbd1-298">**次の手順を実行します。** 次の例では、3つの並列要求を行う前に、受信要求からすべてのデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-298">**Do this:** The following example copies all data from the incoming request before making the three parallel requests.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a><span data-ttu-id="2fbd1-299">要求の完了後に HttpContext を使用しない</span><span class="sxs-lookup"><span data-stu-id="2fbd1-299">Do not use the HttpContext after the request is complete</span></span>

<span data-ttu-id="2fbd1-300">`HttpContext` は、ASP.NET Core パイプラインにアクティブな HTTP 要求がある限り有効です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-300">`HttpContext` is only valid as long as there is an active HTTP request in the ASP.NET Core pipeline.</span></span> <span data-ttu-id="2fbd1-301">ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-301">The entire ASP.NET Core pipeline is an asynchronous chain of delegates that executes every request.</span></span> <span data-ttu-id="2fbd1-302">このチェーンから返された @no__t 0 が完了すると、`HttpContext` がリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-302">When the `Task` returned from this chain completes, the `HttpContext` is recycled.</span></span>

<span data-ttu-id="2fbd1-303">**この操作は避けてください。** 次の例では、最初の @no__t に到達したときに HTTP 要求が完了するように、`async void` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-303">**Do not do this:** The following example uses `async void` which makes the HTTP request complete when the first `await` is reached:</span></span>

* <span data-ttu-id="2fbd1-304">これは、ASP.NET Core アプリでは**常**に不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-304">Which is **ALWAYS** a bad practice in ASP.NET Core apps.</span></span>
* <span data-ttu-id="2fbd1-305">HTTP 要求が完了した後に `HttpResponse` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-305">Accesses the `HttpResponse` after the HTTP request is complete.</span></span>
* <span data-ttu-id="2fbd1-306">プロセスをクラッシュさせる。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-306">Crashes the process.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

<span data-ttu-id="2fbd1-307">**次の手順を実行します。** 次の例では、アクションが完了するまで HTTP 要求が完了しないように、フレームワークに `Task` が返されます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-307">**Do this:** The following example returns a `Task` to the framework so the HTTP request doesn't complete until the action completes.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a><span data-ttu-id="2fbd1-308">バックグラウンドスレッドで HttpContext をキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="2fbd1-308">Do not capture the HttpContext in background threads</span></span>

<span data-ttu-id="2fbd1-309">**この操作は避けてください。** 次の例は、`Controller` プロパティから `HttpContext` をキャプチャしていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-309">**Do not do this:** The following example shows a closure is capturing the `HttpContext` from the `Controller` property.</span></span> <span data-ttu-id="2fbd1-310">作業項目は次のようになる可能性があるため、この方法は不適切です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-310">This is a bad practice because the work item could:</span></span>

* <span data-ttu-id="2fbd1-311">要求スコープの外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-311">Run outside of the request scope.</span></span>
* <span data-ttu-id="2fbd1-312">間違った @no__t を読み取ろうとしました-0。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-312">Attempt to read the wrong `HttpContext`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

<span data-ttu-id="2fbd1-313">**次の手順を実行します。** 次のような例です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-313">**Do this:** The following example:</span></span>

* <span data-ttu-id="2fbd1-314">要求中にバックグラウンドタスクで必要なデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-314">Copies the data required in the background task during the request.</span></span>
* <span data-ttu-id="2fbd1-315">コントローラーから何も参照していません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-315">Doesn't reference anything from the controller.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a><span data-ttu-id="2fbd1-316">バックグラウンドスレッドでコントローラーに挿入されたサービスをキャプチャしない</span><span class="sxs-lookup"><span data-stu-id="2fbd1-316">Do not capture services injected into the controllers on background threads</span></span>

<span data-ttu-id="2fbd1-317">**この操作は避けてください。** 次の例は、`Controller` アクションパラメーターから `DbContext` をキャプチャすることを示しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-317">**Do not do this:** The following example shows a closure is capturing the `DbContext` from the `Controller` action parameter.</span></span> <span data-ttu-id="2fbd1-318">これは不適切な方法です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-318">This is a bad practice.</span></span>  <span data-ttu-id="2fbd1-319">この作業項目は、要求スコープの外部で実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-319">The work item could run outside of the request scope.</span></span> <span data-ttu-id="2fbd1-320">@No__t-0 は要求に対してスコープが設定され、その結果、`ObjectDisposedException` になります。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-320">The `ContosoDbContext` is scoped to the request, resulting in an `ObjectDisposedException`.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

<span data-ttu-id="2fbd1-321">**次の手順を実行します。** 次のような例です。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-321">**Do this:** The following example:</span></span>

* <span data-ttu-id="2fbd1-322">バックグラウンド作業項目のスコープを作成するために @no__t 0 を挿入します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-322">Injects an <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> in order to create a scope in the background work item.</span></span> <span data-ttu-id="2fbd1-323">`IServiceScopeFactory` はシングルトンです。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-323">`IServiceScopeFactory` is a singleton.</span></span>
* <span data-ttu-id="2fbd1-324">バックグラウンドスレッドで新しい依存関係挿入スコープを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-324">Creates a new dependency injection scope in the background thread.</span></span>
* <span data-ttu-id="2fbd1-325">コントローラーから何も参照していません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-325">Doesn't reference anything from the controller.</span></span>
* <span data-ttu-id="2fbd1-326">は、受信要求から `ContosoDbContext` をキャプチャしません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-326">Doesn't capture the `ContosoDbContext` from the incoming request.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

<span data-ttu-id="2fbd1-327">次の強調表示されたコード。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-327">The following highlighted code:</span></span>

* <span data-ttu-id="2fbd1-328">バックグラウンド操作の有効期間のスコープを作成し、そこからサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-328">Creates a scope for the lifetime of the background operation and resolves services from it.</span></span>
* <span data-ttu-id="2fbd1-329">は、正しいスコープの `ContosoDbContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-329">Uses `ContosoDbContext` from the correct scope.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a><span data-ttu-id="2fbd1-330">応答本文が開始された後に状態コードまたはヘッダーを変更しない</span><span class="sxs-lookup"><span data-stu-id="2fbd1-330">Do not modify the status code or headers after the response body has started</span></span>

<span data-ttu-id="2fbd1-331">ASP.NET Core は、HTTP 応答の本文をバッファーしません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-331">ASP.NET Core does not buffer the HTTP response body.</span></span> <span data-ttu-id="2fbd1-332">最初に応答が書き込まれたとき:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-332">The first time the response is written:</span></span>

* <span data-ttu-id="2fbd1-333">ヘッダーは、本文のそのチャンクと共にクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-333">The headers are sent along with that chunk of the body to the client.</span></span>
* <span data-ttu-id="2fbd1-334">応答ヘッダーを変更することはできなくなりました。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-334">It's no longer possible to change response headers.</span></span>

<span data-ttu-id="2fbd1-335">**この操作は避けてください。** 次のコードでは、応答が既に開始された後に応答ヘッダーを追加しようとしています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-335">**Do not do this:** The following code tries to add response headers after the response has already started:</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

<span data-ttu-id="2fbd1-336">前のコードでは、`next()` が応答に書き込まれた場合、`context.Response.Headers["test"] = "test value";` は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-336">In the preceding code, `context.Response.Headers["test"] = "test value";` will throw an exception if `next()` has written to the response.</span></span>

<span data-ttu-id="2fbd1-337">**次の手順を実行します。** 次の例では、ヘッダーを変更する前に、HTTP 応答が開始されたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-337">**Do this:** The following example checks if the HTTP response has started before modifying the headers.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

<span data-ttu-id="2fbd1-338">**次の手順を実行します。** 次の例では、`HttpResponse.OnStarting` を使用して、応答ヘッダーをクライアントにフラッシュする前にヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-338">**Do this:** The following example uses `HttpResponse.OnStarting` to set the headers before the response headers are flushed to the client.</span></span>

<span data-ttu-id="2fbd1-339">応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-339">Checking if the response has not started allows registering a callback that will be invoked just before response headers are written.</span></span> <span data-ttu-id="2fbd1-340">応答が開始されていないかどうかを確認しています:</span><span class="sxs-lookup"><span data-stu-id="2fbd1-340">Checking if the response has not started:</span></span>

* <span data-ttu-id="2fbd1-341">ヘッダーをジャストインタイムで追加またはオーバーライドする機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-341">Provides the ability to append or override headers just in time.</span></span>
* <span data-ttu-id="2fbd1-342">では、パイプライン内の次のミドルウェアに関する知識は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-342">Doesn't require knowledge of the next middleware in the pipeline.</span></span>

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a><span data-ttu-id="2fbd1-343">応答本文への書き込みを既に開始している場合は、next () を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-343">Do not call next() if you have already started writing to the response body</span></span>

<span data-ttu-id="2fbd1-344">コンポーネントは、応答を処理および操作できる場合にのみ呼び出されることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="2fbd1-344">Components only expect to be called if it's possible for them to handle and manipulate the response.</span></span>
