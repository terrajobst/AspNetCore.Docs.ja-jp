---
title: ASP.NET Core のメモリ管理とパターン
author: rick-anderson
description: ASP.NET Core でのメモリの管理方法とガベージコレクター (GC) の動作について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2019
uid: performance/memory
ms.openlocfilehash: 8f6b47ecde6f265bfb9437234b89f11f7d235869
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660014"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="1f879-103">ASP.NET Core のメモリ管理とガベージコレクション (GC)</span><span class="sxs-lookup"><span data-stu-id="1f879-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="1f879-104">[Sébastien Ros](https://github.com/sebastienros)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f879-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1f879-105">メモリ管理は、.NET などのマネージフレームワークでも複雑です。</span><span class="sxs-lookup"><span data-stu-id="1f879-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="1f879-106">メモリの問題を分析して理解することは困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="1f879-107">この記事は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1f879-107">This article:</span></span>

* <span data-ttu-id="1f879-108">多くの*メモリリークが発生*し、GC が動作して*いない*問題が発生しました。</span><span class="sxs-lookup"><span data-stu-id="1f879-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="1f879-109">これらの問題のほとんどは、.NET Core でのメモリ消費のしくみを理解していないか、測定方法を理解していないことによって発生しました。</span><span class="sxs-lookup"><span data-stu-id="1f879-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="1f879-110">問題のあるメモリ使用方法を示し、別のアプローチを提案します。</span><span class="sxs-lookup"><span data-stu-id="1f879-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="1f879-111">.NET Core でのガベージコレクション (GC) のしくみ</span><span class="sxs-lookup"><span data-stu-id="1f879-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="1f879-112">GC は、各セグメントが連続するメモリ範囲であるヒープセグメントを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1f879-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="1f879-113">ヒープに配置されたオブジェクトは、0、1、または2の3つのジェネレーションに分類されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="1f879-114">生成によって、アプリケーションによって参照されなくなったマネージオブジェクトのメモリを GC が解放する頻度が決まります。</span><span class="sxs-lookup"><span data-stu-id="1f879-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="1f879-115">下位の番号付きジェネレーションは、GC の方が頻繁に行われます。</span><span class="sxs-lookup"><span data-stu-id="1f879-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="1f879-116">オブジェクトは、有効期間に基づいて、ある世代から別の世代に移動されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="1f879-117">オブジェクトが長くなると、オブジェクトは上位世代に移動されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="1f879-118">既に説明したように、より高い世代は GC の方が頻繁に発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="1f879-119">短期有効期間オブジェクトは常にジェネレーション0に残ります。</span><span class="sxs-lookup"><span data-stu-id="1f879-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="1f879-120">たとえば、web 要求の有効期間中に参照されるオブジェクトは、短時間で終了します。</span><span class="sxs-lookup"><span data-stu-id="1f879-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="1f879-121">一般に、アプリケーションレベル[シングルトン](xref:fundamentals/dependency-injection#service-lifetimes)は第2世代に移行します。</span><span class="sxs-lookup"><span data-stu-id="1f879-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="1f879-122">ASP.NET Core アプリが開始されると、GC は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f879-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="1f879-123">初期ヒープセグメント用にメモリを予約します。</span><span class="sxs-lookup"><span data-stu-id="1f879-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="1f879-124">ランタイムが読み込まれるときに、メモリの一部をコミットします。</span><span class="sxs-lookup"><span data-stu-id="1f879-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="1f879-125">前のメモリ割り当ては、パフォーマンス上の理由から実行されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="1f879-126">パフォーマンス上の利点は、連続したメモリのヒープセグメントから取得されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="1f879-127">GC を呼び出します。収集</span><span class="sxs-lookup"><span data-stu-id="1f879-127">Call GC.Collect</span></span>

<span data-ttu-id="1f879-128">[GC を呼び出しています。](xref:System.GC.Collect*)明示的に収集:</span><span class="sxs-lookup"><span data-stu-id="1f879-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="1f879-129">運用 ASP.NET Core アプリでは**実行しないでください。**</span><span class="sxs-lookup"><span data-stu-id="1f879-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="1f879-130">は、メモリリークを調査するときに便利です。</span><span class="sxs-lookup"><span data-stu-id="1f879-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="1f879-131">調査時に、GC によってすべての未解決のオブジェクトがメモリから削除されたことを確認し、メモリを測定します。</span><span class="sxs-lookup"><span data-stu-id="1f879-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="1f879-132">アプリのメモリ使用量の分析</span><span class="sxs-lookup"><span data-stu-id="1f879-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="1f879-133">専用ツールは、メモリ使用量の分析に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1f879-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="1f879-134">カウント (オブジェクト参照を)</span><span class="sxs-lookup"><span data-stu-id="1f879-134">Counting object references</span></span>
- <span data-ttu-id="1f879-135">GC が CPU 使用率に与える影響を測定する</span><span class="sxs-lookup"><span data-stu-id="1f879-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="1f879-136">各世代に使用されるメモリ領域の測定</span><span class="sxs-lookup"><span data-stu-id="1f879-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="1f879-137">メモリ使用量を分析するには、次のツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f879-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="1f879-138">[dotnet](/dotnet/core/diagnostics/dotnet-trace): 運用コンピューターで使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="1f879-139">Visual Studio デバッガーを使用せずにメモリ使用量を分析する</span><span class="sxs-lookup"><span data-stu-id="1f879-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="1f879-140">Visual Studio でのメモリ使用のプロファイリング</span><span class="sxs-lookup"><span data-stu-id="1f879-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="1f879-141">メモリの問題の検出</span><span class="sxs-lookup"><span data-stu-id="1f879-141">Detecting memory issues</span></span>

<span data-ttu-id="1f879-142">タスクマネージャーを使用して、ASP.NET アプリが使用しているメモリの量を把握できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="1f879-143">タスクマネージャーのメモリ値:</span><span class="sxs-lookup"><span data-stu-id="1f879-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="1f879-144">ASP.NET プロセスによって使用されるメモリの量を表します。</span><span class="sxs-lookup"><span data-stu-id="1f879-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="1f879-145">アプリの生きたオブジェクトや、ネイティブメモリ使用量などの他のメモリコンシューマーを含みます。</span><span class="sxs-lookup"><span data-stu-id="1f879-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="1f879-146">タスクマネージャーのメモリ値が無制限に増加し、フラット化されない場合、アプリにはメモリリークが発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="1f879-147">次のセクションでは、いくつかのメモリ使用パターンについて説明し、説明します。</span><span class="sxs-lookup"><span data-stu-id="1f879-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="1f879-148">ディスプレイメモリ使用量アプリのサンプル</span><span class="sxs-lookup"><span data-stu-id="1f879-148">Sample display memory usage app</span></span>

<span data-ttu-id="1f879-149">[Memoryleak サンプルアプリ](https://github.com/sebastienros/memoryleak)は GitHub で入手できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="1f879-150">MemoryLeak アプリ:</span><span class="sxs-lookup"><span data-stu-id="1f879-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="1f879-151">には、アプリのリアルタイムメモリおよび GC データを収集する診断コントローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f879-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="1f879-152">には、メモリおよび GC データを表示するインデックスページがあります。</span><span class="sxs-lookup"><span data-stu-id="1f879-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="1f879-153">インデックスページは、1秒ごとに更新されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="1f879-154">には、さまざまなメモリ読み込みパターンを提供する API コントローラーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f879-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="1f879-155">はサポートされているツールではありませんが、ASP.NET Core アプリのメモリ使用量パターンを表示するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="1f879-156">MemoryLeak を実行します。</span><span class="sxs-lookup"><span data-stu-id="1f879-156">Run MemoryLeak.</span></span> <span data-ttu-id="1f879-157">割り当てられたメモリは、GC が発生するまで徐々に増加します。</span><span class="sxs-lookup"><span data-stu-id="1f879-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="1f879-158">データをキャプチャするためのカスタムオブジェクトがツールによって割り当てられるため、メモリが増加します。</span><span class="sxs-lookup"><span data-stu-id="1f879-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="1f879-159">次の図は、Gen 0 GC が発生したときの MemoryLeak インデックスページを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="1f879-160">API コントローラーからの API エンドポイントが呼び出されていないため、グラフには0個の RPS (1 秒あたりの要求数) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![前のグラフ](memory/_static/0RPS.png)

<span data-ttu-id="1f879-162">グラフには、メモリ使用量の2つの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="1f879-163">割り当て済み: マネージオブジェクトによって占有されているメモリの量</span><span class="sxs-lookup"><span data-stu-id="1f879-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="1f879-164">Working set: プロセスによって使用される合計物理メモリ (RAM)。</span><span class="sxs-lookup"><span data-stu-id="1f879-164">Working set: the total physical memory (RAM) used by the process.</span></span> <span data-ttu-id="1f879-165">表示される作業セットは、タスクマネージャーが表示できる値と同じです。</span><span class="sxs-lookup"><span data-stu-id="1f879-165">The working set shown is the same value Task Manager can display.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="1f879-166">一時オブジェクト</span><span class="sxs-lookup"><span data-stu-id="1f879-166">Transient objects</span></span>

<span data-ttu-id="1f879-167">次の API は、10 KB の文字列インスタンスを作成し、それをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="1f879-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="1f879-168">各要求では、新しいオブジェクトがメモリに割り当てられ、応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="1f879-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="1f879-169">文字列は .NET で UTF-16 文字として格納されるため、各文字はメモリ内で2バイトを取ります。</span><span class="sxs-lookup"><span data-stu-id="1f879-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="1f879-170">次のグラフは、の負荷が比較的小さい場合に生成され、メモリ割り当てが GC によってどのように影響されているかを示します。</span><span class="sxs-lookup"><span data-stu-id="1f879-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![前のグラフ](memory/_static/bigstring.png)

<span data-ttu-id="1f879-172">前のグラフは次を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-172">The preceding chart shows:</span></span>

* <span data-ttu-id="1f879-173">4K RPS (1 秒あたりの要求数)。</span><span class="sxs-lookup"><span data-stu-id="1f879-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="1f879-174">ジェネレーション0の GC コレクションは、約2秒ごとに発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="1f879-175">ワーキングセットは約 500 MB に固定されています。</span><span class="sxs-lookup"><span data-stu-id="1f879-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="1f879-176">CPU は12% です。</span><span class="sxs-lookup"><span data-stu-id="1f879-176">CPU is 12%.</span></span>
* <span data-ttu-id="1f879-177">メモリ使用量と解放 (GC 経由) が安定しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="1f879-178">次のグラフは、マシンで処理できる最大スループットで取得されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![前のグラフ](memory/_static/bigstring2.png)

<span data-ttu-id="1f879-180">前のグラフは次を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-180">The preceding chart shows:</span></span>

* <span data-ttu-id="1f879-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="1f879-181">22K RPS</span></span>
* <span data-ttu-id="1f879-182">ジェネレーション0の GC コレクションは1秒間に数回発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="1f879-183">ジェネレーション1のコレクションがトリガーされるのは、アプリによって1秒あたりにかなり多くのメモリが割り当てられたためです。</span><span class="sxs-lookup"><span data-stu-id="1f879-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="1f879-184">ワーキングセットは約 500 MB に固定されています。</span><span class="sxs-lookup"><span data-stu-id="1f879-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="1f879-185">CPU は33% です。</span><span class="sxs-lookup"><span data-stu-id="1f879-185">CPU is 33%.</span></span>
* <span data-ttu-id="1f879-186">メモリ使用量と解放 (GC 経由) が安定しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="1f879-187">CPU (33%)は過剰に使用されていないため、ガベージコレクションは多くの割り当てを保持できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="1f879-188">ワークステーション GC とサーバー GC</span><span class="sxs-lookup"><span data-stu-id="1f879-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="1f879-189">.NET ガベージコレクターには、次の2つの異なるモードがあります。</span><span class="sxs-lookup"><span data-stu-id="1f879-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="1f879-190">**WORKSTATION GC**: デスクトップ用に最適化されています。</span><span class="sxs-lookup"><span data-stu-id="1f879-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="1f879-191">**サーバー GC**。</span><span class="sxs-lookup"><span data-stu-id="1f879-191">**Server GC**.</span></span> <span data-ttu-id="1f879-192">ASP.NET Core アプリの既定の GC。</span><span class="sxs-lookup"><span data-stu-id="1f879-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="1f879-193">サーバーに合わせて最適化されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-193">Optimized for the server.</span></span>

<span data-ttu-id="1f879-194">GC モードは、プロジェクトファイルまたは発行されたアプリの*runtimeconfig. json*ファイルで明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="1f879-195">次のマークアップは、プロジェクトファイル内の `ServerGarbageCollection` の設定を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="1f879-196">プロジェクトファイルの `ServerGarbageCollection` を変更するには、アプリを再構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="1f879-197">**注:** サーバーのガベージコレクションは、コアが1つのマシンでは使用でき**ません**。</span><span class="sxs-lookup"><span data-stu-id="1f879-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="1f879-198">詳細については、「<xref:System.Runtime.GCSettings.IsServerGC>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f879-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="1f879-199">次の図は、ワークステーション GC を使用した 5K RPS のメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![前のグラフ](memory/_static/workstation.png)

<span data-ttu-id="1f879-201">このグラフとサーバーのバージョンの違いは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1f879-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="1f879-202">ワーキングセットは 500 MB から 70 MB になります。</span><span class="sxs-lookup"><span data-stu-id="1f879-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="1f879-203">GC は、2秒ごとではなく、1秒あたり複数回ジェネレーション0のコレクションを行います。</span><span class="sxs-lookup"><span data-stu-id="1f879-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="1f879-204">GC は 300 MB から 10 MB に減少します。</span><span class="sxs-lookup"><span data-stu-id="1f879-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="1f879-205">一般的な web サーバー環境では、CPU 使用率はメモリよりも重要であるため、サーバー GC の方が適しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="1f879-206">メモリ使用率が高く、CPU 使用率が比較的低い場合、ワークステーションの GC のパフォーマンスが向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="1f879-207">たとえば、メモリが不足している複数の web アプリをホストしている高密度です。</span><span class="sxs-lookup"><span data-stu-id="1f879-207">For example, high density hosting several web apps where memory is scarce.</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="1f879-208">永続的なオブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="1f879-208">Persistent object references</span></span>

<span data-ttu-id="1f879-209">GC は参照されているオブジェクトを解放できません。</span><span class="sxs-lookup"><span data-stu-id="1f879-209">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="1f879-210">参照されていて、不要になったオブジェクトは、メモリリークを発生させます。</span><span class="sxs-lookup"><span data-stu-id="1f879-210">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="1f879-211">アプリがオブジェクトを頻繁に割り当てて、不要になった後にオブジェクトを解放できない場合は、メモリ使用量が時間の経過と共に増加します。</span><span class="sxs-lookup"><span data-stu-id="1f879-211">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="1f879-212">次の API は、10 KB の文字列インスタンスを作成し、それをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="1f879-212">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="1f879-213">前の例との違いは、このインスタンスが静的メンバーによって参照されていることです。これは、コレクションでは使用できないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="1f879-213">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="1f879-214">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="1f879-214">The preceding code:</span></span>

* <span data-ttu-id="1f879-215">は、一般的なメモリリークの例です。</span><span class="sxs-lookup"><span data-stu-id="1f879-215">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="1f879-216">頻繁な呼び出しでは、プロセスがクラッシュして `OutOfMemory` 例外が発生するまで、アプリのメモリが増加します。</span><span class="sxs-lookup"><span data-stu-id="1f879-216">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![前のグラフ](memory/_static/eternal.png)

<span data-ttu-id="1f879-218">前の図では、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f879-218">In the preceding image:</span></span>

* <span data-ttu-id="1f879-219">`/api/staticstring` エンドポイントのロードテストを行うと、メモリが直線的に増加します。</span><span class="sxs-lookup"><span data-stu-id="1f879-219">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="1f879-220">GC は、ジェネレーション2のコレクションを呼び出すことによって、メモリの負荷が増加したときにメモリの解放を試みます。</span><span class="sxs-lookup"><span data-stu-id="1f879-220">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="1f879-221">GC では、リークしたメモリを解放できません。</span><span class="sxs-lookup"><span data-stu-id="1f879-221">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="1f879-222">割り当てられたワーキングセットは時間と共に増加します。</span><span class="sxs-lookup"><span data-stu-id="1f879-222">Allocated and working set increase with time.</span></span>

<span data-ttu-id="1f879-223">キャッシュなどの一部のシナリオでは、メモリ不足によってオブジェクト参照が強制的に解放されるまで、オブジェクト参照を保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-223">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="1f879-224"><xref:System.WeakReference> クラスは、この種のキャッシュコードに使用できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-224">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="1f879-225">`WeakReference` オブジェクトは、メモリ負荷の下で収集されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-225">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="1f879-226"><xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> の既定の実装では `WeakReference`が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-226">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="1f879-227">ネイティブメモリ</span><span class="sxs-lookup"><span data-stu-id="1f879-227">Native memory</span></span>

<span data-ttu-id="1f879-228">.NET Core オブジェクトの中には、ネイティブメモリに依存しているものがあります。</span><span class="sxs-lookup"><span data-stu-id="1f879-228">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="1f879-229">GC でネイティブメモリを収集することはでき**ません**。</span><span class="sxs-lookup"><span data-stu-id="1f879-229">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="1f879-230">ネイティブメモリを使用している .NET オブジェクトは、ネイティブコードを使用して解放する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-230">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="1f879-231">.NET には、開発者がネイティブメモリを解放できるようにするための <xref:System.IDisposable> インターフェイスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="1f879-231">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="1f879-232"><xref:System.IDisposable.Dispose*> が呼び出されていない場合でも、[ファイナライザー](/dotnet/csharp/programming-guide/classes-and-structs/destructors)の実行時に、正しく実装されたクラス呼び出し `Dispose` ます。</span><span class="sxs-lookup"><span data-stu-id="1f879-232">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="1f879-233">次のコードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="1f879-233">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="1f879-234">[PhysicaFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0)はマネージクラスであるため、すべてのインスタンスが要求の最後に収集されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-234">[PhysicaFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="1f879-235">次の図は、`fileprovider` API を継続的に呼び出すときのメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-235">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![前のグラフ](memory/_static/fileprovider.png)

<span data-ttu-id="1f879-237">前のグラフは、このクラスの実装に関する明らかな問題を示しています。これは、メモリ使用量が増加し続けるためです。</span><span class="sxs-lookup"><span data-stu-id="1f879-237">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="1f879-238">これは、[この問題](https://github.com/aspnet/Home/issues/3110)で追跡されている既知の問題です。</span><span class="sxs-lookup"><span data-stu-id="1f879-238">This is a known problem that is being tracked in [this issue](https://github.com/aspnet/Home/issues/3110).</span></span>

<span data-ttu-id="1f879-239">次のいずれかの方法で、ユーザーコードで同じリークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-239">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="1f879-240">クラスを正しく解放できません。</span><span class="sxs-lookup"><span data-stu-id="1f879-240">Not releasing the class correctly.</span></span>
* <span data-ttu-id="1f879-241">破棄する必要がある依存オブジェクトの `Dispose`メソッドの呼び出しを忘れています。</span><span class="sxs-lookup"><span data-stu-id="1f879-241">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="1f879-242">ラージオブジェクトヒープ</span><span class="sxs-lookup"><span data-stu-id="1f879-242">Large objects heap</span></span>

<span data-ttu-id="1f879-243">メモリの割り当てや空きサイクルが頻繁に発生する場合は、特にメモリの大量のチャンクを割り当てるときにメモリをフラグメント化できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-243">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="1f879-244">オブジェクトは、連続したメモリブロックで割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="1f879-244">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="1f879-245">断片化を軽減するために、GC によってメモリが解放されると、断片化が解消されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-245">To mitigate fragmentation, when the GC frees memory, it trys to defragment it.</span></span> <span data-ttu-id="1f879-246">このプロセスは、**圧縮**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="1f879-246">This process is called **compaction**.</span></span> <span data-ttu-id="1f879-247">圧縮には、オブジェクトの移動が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1f879-247">Compaction involves moving objects.</span></span> <span data-ttu-id="1f879-248">大きなオブジェクトを移動すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="1f879-248">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="1f879-249">このため、GC は大きなオブジェクト[ヒープ](/dotnet/standard/garbage-collection/large-object-heap)(LOH) と呼ばれる_大きな_オブジェクト用に特別なメモリゾーンを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f879-249">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="1f879-250">85000バイトを超えるオブジェクト (約 83 KB) は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1f879-250">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="1f879-251">LOH に配置されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-251">Placed on the LOH.</span></span>
* <span data-ttu-id="1f879-252">圧縮されていません。</span><span class="sxs-lookup"><span data-stu-id="1f879-252">Not compacted.</span></span>
* <span data-ttu-id="1f879-253">ジェネレーション2の Gc 中に収集されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-253">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="1f879-254">LOH がいっぱいになると、GC はジェネレーション2のコレクションをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="1f879-254">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="1f879-255">ジェネレーション2のコレクション:</span><span class="sxs-lookup"><span data-stu-id="1f879-255">Generation 2 collections:</span></span>

* <span data-ttu-id="1f879-256">は本質的に低速です。</span><span class="sxs-lookup"><span data-stu-id="1f879-256">Are inherently slow.</span></span>
* <span data-ttu-id="1f879-257">さらに、他のすべての世代でコレクションをトリガーするコストも発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-257">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="1f879-258">次のコードは、LOH を直ちに最適化します。</span><span class="sxs-lookup"><span data-stu-id="1f879-258">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="1f879-259">LOH の圧縮の詳細については、「<xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f879-259">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="1f879-260">.NET Core 3.0 以降を使用するコンテナーでは、LOH が自動的に圧縮されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-260">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="1f879-261">次の API は、この動作を示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-261">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="1f879-262">次のグラフは、[最大負荷] の下で `/api/loh/84975` エンドポイントを呼び出すメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-262">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![前のグラフ](memory/_static/loh1.png)

<span data-ttu-id="1f879-264">次のグラフは、`/api/loh/84976` エンドポイントを呼び出し、 *1 バイトだけ*を割り当てるメモリプロファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-264">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![前のグラフ](memory/_static/loh2.png)

<span data-ttu-id="1f879-266">注: `byte[]` 構造体にはオーバーヘッドバイトがあります。</span><span class="sxs-lookup"><span data-stu-id="1f879-266">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="1f879-267">そのため、84976バイトは85000の制限をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="1f879-267">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="1f879-268">前の2つのグラフを比較します。</span><span class="sxs-lookup"><span data-stu-id="1f879-268">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="1f879-269">ワーキングセットは、2つのシナリオ (約 450 MB) で似ています。</span><span class="sxs-lookup"><span data-stu-id="1f879-269">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="1f879-270">[LOH 要求 (84975 バイト)] の下には、ほとんどがジェネレーション0のコレクションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-270">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="1f879-271">LOH を超える要求では、定数ジェネレーション2のコレクションが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-271">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="1f879-272">ジェネレーション2のコレクションはコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="1f879-272">Generation 2 collections are expensive.</span></span> <span data-ttu-id="1f879-273">より多くの CPU が必要であり、スループットは約50% 低下します。</span><span class="sxs-lookup"><span data-stu-id="1f879-273">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="1f879-274">一時的なラージオブジェクトは、gen2 Gc を引き起こすため、特に問題になります。</span><span class="sxs-lookup"><span data-stu-id="1f879-274">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="1f879-275">最大のパフォーマンスを向上させるには、大きなオブジェクトの使用を最小限にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-275">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="1f879-276">可能であれば、大きなオブジェクトを分割します。</span><span class="sxs-lookup"><span data-stu-id="1f879-276">If possible, split up large objects.</span></span> <span data-ttu-id="1f879-277">たとえば、ASP.NET Core の[応答キャッシュ](xref:performance/caching/response)ミドルウェアでは、キャッシュエントリが85000バイト未満のブロックに分割されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-277">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="1f879-278">次のリンクでは、オブジェクトを LOH の制限下に維持するための ASP.NET Core アプローチについて説明します。</span><span class="sxs-lookup"><span data-stu-id="1f879-278">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>
- [<span data-ttu-id="1f879-279">ResponseCaching/Streams/StreamUtilities .cs</span><span class="sxs-lookup"><span data-stu-id="1f879-279">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/aspnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
- [<span data-ttu-id="1f879-280">ResponseCaching/MemoryResponseCache</span><span class="sxs-lookup"><span data-stu-id="1f879-280">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="1f879-281">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="1f879-281">For more information, see:</span></span>

* [<span data-ttu-id="1f879-282">大きなオブジェクトヒープが漏れています</span><span class="sxs-lookup"><span data-stu-id="1f879-282">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="1f879-283">大きなオブジェクトヒープ</span><span class="sxs-lookup"><span data-stu-id="1f879-283">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="1f879-284">HttpClient</span><span class="sxs-lookup"><span data-stu-id="1f879-284">HttpClient</span></span>

<span data-ttu-id="1f879-285"><xref:System.Net.Http.HttpClient> を誤って使用すると、リソースリークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-285">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="1f879-286">データベース接続、ソケット、ファイルハンドルなどのシステムリソース:</span><span class="sxs-lookup"><span data-stu-id="1f879-286">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="1f879-287">はメモリよりも不足しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-287">Are more scarce than memory.</span></span>
* <span data-ttu-id="1f879-288">メモリよりもリークが発生した場合、より問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-288">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="1f879-289">経験豊富な .NET 開発者は、<xref:System.IDisposable>を実装するオブジェクトで <xref:System.IDisposable.Dispose*> を呼び出すことを理解しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-289">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="1f879-290">`IDisposable` を実装するオブジェクトを破棄しないと、通常、メモリリークやシステムリソースのリークが発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-290">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="1f879-291">`HttpClient` は `IDisposable`を実装しますが、すべての呼び出しで破棄することはでき**ません**。</span><span class="sxs-lookup"><span data-stu-id="1f879-291">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="1f879-292">代わりに、`HttpClient` を再利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f879-292">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="1f879-293">次のエンドポイントは、すべての要求で新しい `HttpClient` インスタンスを作成し、破棄します。</span><span class="sxs-lookup"><span data-stu-id="1f879-293">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="1f879-294">負荷の下で、次のエラーメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-294">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="1f879-295">`HttpClient` インスタンスが破棄されている場合でも、オペレーティングシステムによって実際のネットワーク接続が解放されるまでに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="1f879-295">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="1f879-296">新しい接続を継続的に作成することで、_ポートの枯渇_が発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-296">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="1f879-297">各クライアント接続には、独自のクライアントポートが必要です。</span><span class="sxs-lookup"><span data-stu-id="1f879-297">Each client connection requires its own client port.</span></span>

<span data-ttu-id="1f879-298">ポートの枯渇を防ぐ方法の1つは、同じ `HttpClient` インスタンスを再利用することです。</span><span class="sxs-lookup"><span data-stu-id="1f879-298">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="1f879-299">`HttpClient` インスタンスは、アプリが停止したときに解放されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-299">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="1f879-300">この例では、すべての破棄可能なリソースを使用後に破棄する必要がないことを示しています。</span><span class="sxs-lookup"><span data-stu-id="1f879-300">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="1f879-301">`HttpClient` インスタンスの有効期間をより適切に処理する方法については、次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f879-301">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="1f879-302">HttpClient と有効期間の管理</span><span class="sxs-lookup"><span data-stu-id="1f879-302">HttpClient and lifetime management</span></span>](/aspnet/core/fundamentals/http-requests#httpclient-and-lifetime-management)
* [<span data-ttu-id="1f879-303">HTTPClient ファクトリのブログ</span><span class="sxs-lookup"><span data-stu-id="1f879-303">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="1f879-304">オブジェクトプール</span><span class="sxs-lookup"><span data-stu-id="1f879-304">Object pooling</span></span>

<span data-ttu-id="1f879-305">前の例では、`HttpClient` インスタンスを静的にし、すべての要求で再利用する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="1f879-305">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="1f879-306">再利用すると、リソースが不足するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="1f879-306">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="1f879-307">オブジェクトプール:</span><span class="sxs-lookup"><span data-stu-id="1f879-307">Object pooling:</span></span>

* <span data-ttu-id="1f879-308">再利用パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="1f879-308">Uses the reuse pattern.</span></span>
* <span data-ttu-id="1f879-309">は、作成に負荷がかかるオブジェクト向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="1f879-309">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="1f879-310">プールは、スレッド間で予約および解放できる事前に初期化されたオブジェクトのコレクションです。</span><span class="sxs-lookup"><span data-stu-id="1f879-310">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="1f879-311">プールでは、制限、事前定義されたサイズ、増加率などの割り当てルールを定義できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-311">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="1f879-312">NuGet パッケージの[Microsoft extension. ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/)には、このようなプールの管理に役立つクラスが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f879-312">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="1f879-313">次の API エンドポイントは、各要求に対してランダムな数値を格納する `byte` バッファーをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="1f879-313">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="1f879-314">次のグラフは、中程度の負荷で前の API を呼び出したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-314">The following chart display calling the preceding API with moderate load:</span></span>

![前のグラフ](memory/_static/array.png)

<span data-ttu-id="1f879-316">前のグラフでは、ジェネレーション0のコレクションは1秒間に約1回発生します。</span><span class="sxs-lookup"><span data-stu-id="1f879-316">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="1f879-317">前のコードは、 [`ArrayPool<T>`](xref:System.Buffers.ArrayPool`1)を使用して `byte` バッファーをプールすることによって最適化できます。</span><span class="sxs-lookup"><span data-stu-id="1f879-317">The preceding code can be optimized by pooling the `byte` buffer by using [`ArrayPool<T>`](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="1f879-318">静的インスタンスは、要求間で再利用されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-318">A static instance is reused across requests.</span></span>

<span data-ttu-id="1f879-319">この方法の違いは、プールされたオブジェクトが API から返されることです。</span><span class="sxs-lookup"><span data-stu-id="1f879-319">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="1f879-320">ということは：</span><span class="sxs-lookup"><span data-stu-id="1f879-320">That means:</span></span>

* <span data-ttu-id="1f879-321">オブジェクトは、メソッドから戻るとすぐにコントロールから除外されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-321">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="1f879-322">オブジェクトを解放することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f879-322">You can't release the object.</span></span>

<span data-ttu-id="1f879-323">オブジェクトの破棄を設定するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1f879-323">To set up disposal of the object:</span></span>

* <span data-ttu-id="1f879-324">プールされた配列を破棄可能なオブジェクトにカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="1f879-324">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="1f879-325">プールされたオブジェクトを[httpcontext.current](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*)に登録します。</span><span class="sxs-lookup"><span data-stu-id="1f879-325">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="1f879-326">`RegisterForDispose` は、HTTP 要求が完了したときにのみ解放されるように、ターゲットオブジェクトで `Dispose`を呼び出すことを処理します。</span><span class="sxs-lookup"><span data-stu-id="1f879-326">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="1f879-327">プールされていないバージョンと同じ負荷を適用すると、次のグラフが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f879-327">Applying the same load as the non-pooled version results in the following chart:</span></span>

![前のグラフ](memory/_static/pooledarray.png)

<span data-ttu-id="1f879-329">主な違いは、バイトが割り当てられ、その結果、ジェネレーション0のコレクションがはるかに少ないことです。</span><span class="sxs-lookup"><span data-stu-id="1f879-329">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f879-330">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1f879-330">Additional resources</span></span>

* [<span data-ttu-id="1f879-331">ガベージ コレクション</span><span class="sxs-lookup"><span data-stu-id="1f879-331">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="1f879-332">同時実行ビジュアライザーを使用したさまざまな GC モードについて</span><span class="sxs-lookup"><span data-stu-id="1f879-332">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="1f879-333">大きなオブジェクトヒープが漏れています</span><span class="sxs-lookup"><span data-stu-id="1f879-333">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="1f879-334">大きなオブジェクトヒープ</span><span class="sxs-lookup"><span data-stu-id="1f879-334">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)
