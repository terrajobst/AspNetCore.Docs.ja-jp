---
title: ASP.NET Core 内のメモリ内のキャッシュ
author: rick-anderson
description: ASP.NET Core でメモリにデータをキャッシュする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 8/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 474f71225371ea89b023ee077d4ecc03e9751681
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030313"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="4b40a-103">ASP.NET Core 内のメモリ内のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="4b40a-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="4b40a-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [John luo](https://github.com/JunTaoLuo)、および[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4b40a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4b40a-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4b40a-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="4b40a-106">キャッシュの基本</span><span class="sxs-lookup"><span data-stu-id="4b40a-106">Caching basics</span></span>

<span data-ttu-id="4b40a-107">キャッシュを使用すると、コンテンツを生成するために必要な作業量を減らすことで、アプリのパフォーマンスとスケーラビリティを大幅に向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="4b40a-108">キャッシュは、あまり変更されないデータで最適に機能します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="4b40a-109">キャッシュを使用すると、元のソースよりもはるかに高速に返されるデータのコピーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="4b40a-110">アプリは、キャッシュされたデータに依存し**ない**ように作成およびテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="4b40a-111">ASP.NET Core は、いくつかの異なるキャッシュをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4b40a-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="4b40a-112">最も単純なキャッシュは、web サーバーのメモリに格納されているキャッシュを表す[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)に基づいています。</span><span class="sxs-lookup"><span data-stu-id="4b40a-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="4b40a-113">複数のサーバーのサーバーファームで実行されるアプリでは、メモリ内キャッシュを使用するときにセッションが固定されていることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-113">Apps that run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="4b40a-114">固定セッションでは、クライアントからの後続の要求がすべて同じサーバーに送られるようにします。</span><span class="sxs-lookup"><span data-stu-id="4b40a-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="4b40a-115">たとえば、Azure Web apps では、[アプリケーション要求ルーティング](https://www.iis.net/learn/extensions/planning-for-arr)処理 (ARR) を使用して、後続のすべての要求を同じサーバーにルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4b40a-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="4b40a-116">Web ファームの固定されていないセッションでは、キャッシュ整合性の問題を回避するために[分散キャッシュ](distributed.md)が必要です。</span><span class="sxs-lookup"><span data-stu-id="4b40a-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="4b40a-117">アプリによっては、分散キャッシュがメモリ内キャッシュよりも高いスケールアウトをサポートする場合があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="4b40a-118">分散キャッシュを使用すると、キャッシュメモリが外部プロセスにオフロードされます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4b40a-119">キャッシュ`IMemoryCache`の[優先順位](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)がに設定されていない限り、キャッシュは`CacheItemPriority.NeverRemove`メモリ不足のときにキャッシュエントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="4b40a-120">を設定して`CacheItemPriority` 、キャッシュがメモリ不足の状態にある見つけ項目の優先度を調整できます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="4b40a-121">メモリ内キャッシュには、任意のオブジェクトを格納できます。分散キャッシュインターフェイスはに`byte[]`制限されています。</span><span class="sxs-lookup"><span data-stu-id="4b40a-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="4b40a-122">メモリ内および分散キャッシュストアは、キーと値のペアとしてキャッシュ項目を格納します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="4b40a-123">System.string. キャッシュ/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="4b40a-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="4b40a-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>([NuGet パッケージ](https://www.nuget.org/packages/System.Runtime.Caching/)) は、次のものと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="4b40a-125">.NET Standard 2.0 以降。</span><span class="sxs-lookup"><span data-stu-id="4b40a-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="4b40a-126">.NET Standard 2.0 以降を対象とするすべての[.net 実装](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="4b40a-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="4b40a-127">たとえば、2.0 以降の ASP.NET Core ます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="4b40a-128">.NET Framework 4.5 以降。</span><span class="sxs-lookup"><span data-stu-id="4b40a-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="4b40a-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` ASP.NET Core に統合することをお勧めします。そのため、この記事で説明されているように、このキャッシュはお勧めします`System.Runtime.Caching`/`MemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="4b40a-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="4b40a-130">たとえば、 `IMemoryCache`は ASP.NET Core[依存関係の挿入](xref:fundamentals/dependency-injection)とネイティブに連携します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="4b40a-131">ASP.NET `System.Runtime.Caching` 4.x から ASP.NET Core にコードを移植するときは、互換性ブリッジとして使用/ `MemoryCache`します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="4b40a-132">キャッシュのガイドライン</span><span class="sxs-lookup"><span data-stu-id="4b40a-132">Cache guidelines</span></span>

* <span data-ttu-id="4b40a-133">コードには常に、データをフェッチするためのフォールバックオプションがあり、使用可能なキャッシュ値に依存し**ない**ようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="4b40a-134">キャッシュは、不足しているリソース (メモリ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="4b40a-135">キャッシュ拡張の制限:</span><span class="sxs-lookup"><span data-stu-id="4b40a-135">Limit cache growth:</span></span>
  * <span data-ttu-id="4b40a-136">外部入力をキャッシュキーとして使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4b40a-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="4b40a-137">キャッシュの拡張を制限するには、有効期限を使用します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="4b40a-138">[キャッシュサイズを制限するには、SetSize、Size、および SizeLimit を使用](#use-setsize-size-and-sizelimit-to-limit-cache-size)します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="4b40a-139">ASP.NET Core ランタイムでは、メモリ負荷に基づいてキャッシュサイズが制限されません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-139">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="4b40a-140">キャッシュサイズを制限するのは開発者だけです。</span><span class="sxs-lookup"><span data-stu-id="4b40a-140">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="4b40a-141">IMemoryCache の使用</span><span class="sxs-lookup"><span data-stu-id="4b40a-141">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="4b40a-142">[依存関係の挿入](xref:fundamentals/dependency-injection)から*共有*メモリキャッシュを使用し`Size`、、 `SizeLimit` 、またはを呼び出し`SetSize`てキャッシュサイズを制限すると、アプリが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="4b40a-143">キャッシュにサイズ制限が設定されている場合、すべてのエントリは追加時にサイズを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="4b40a-144">これにより、開発者は共有キャッシュを使用する内容を完全に制御できない場合があるため、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="4b40a-145">たとえば、Entity Framework Core は共有キャッシュを使用し、サイズを指定しません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="4b40a-146">アプリがキャッシュサイズの制限を設定し、EF Core を使用する場合、 `InvalidOperationException`アプリはをスローします。</span><span class="sxs-lookup"><span data-stu-id="4b40a-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="4b40a-147">、 `SetSize` `SizeLimit` 、またはを使用してキャッシュを制限する場合は、キャッシュ用のキャッシュシングルトンを作成します。 `Size`</span><span class="sxs-lookup"><span data-stu-id="4b40a-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="4b40a-148">詳細と例については、「 [SetSize、サイズ、および SizeLimit を使用してキャッシュサイズを制限する](#use-setsize-size-and-sizelimit-to-limit-cache-size)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4b40a-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="4b40a-149">インメモリキャッシュは、[依存関係の挿入](xref:fundamentals/dependency-injection)を使用してアプリから参照される*サービス*です。</span><span class="sxs-lookup"><span data-stu-id="4b40a-149">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4b40a-150">で`AddMemoryCache` の`ConfigureServices`呼び出し:</span><span class="sxs-lookup"><span data-stu-id="4b40a-150">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="4b40a-151">コンストラクターに`IMemoryCache`インスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-151">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="4b40a-152">`IMemoryCache`NuGet パッケージの[拡張](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)が必要です。</span><span class="sxs-lookup"><span data-stu-id="4b40a-152">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="4b40a-153">`IMemoryCache`では、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage)で使用できる NuGet パッケージ[microsoft. extension. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)が必要です。</span><span class="sxs-lookup"><span data-stu-id="4b40a-153">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="4b40a-154">`IMemoryCache`では、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)で入手できる NuGet パッケージ[microsoft. extension. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)が必要です。</span><span class="sxs-lookup"><span data-stu-id="4b40a-154">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="4b40a-155">次のコードでは、 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)を使用して、時間がキャッシュ内にあるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-155">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="4b40a-156">時間がキャッシュされていない場合は、新しいエントリが作成され、が[設定](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)されたキャッシュに追加されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-156">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="4b40a-157">現在の時刻とキャッシュされた時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-157">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="4b40a-158">キャッシュされた値は、タイムアウト期間内に要求があってもキャッシュに残ります。`DateTime`</span><span class="sxs-lookup"><span data-stu-id="4b40a-158">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="4b40a-159">次の図は、現在の時刻と、キャッシュから取得された古い時間を示しています。</span><span class="sxs-lookup"><span data-stu-id="4b40a-159">The following image shows the current time and an older time retrieved from the cache:</span></span>

![2つの異なる時刻が表示されたインデックスビュー](memory/_static/time.png)

<span data-ttu-id="4b40a-161">次のコードでは、 [Getorcreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)と[Getorcreateasync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)を使用してデータをキャッシュしています。</span><span class="sxs-lookup"><span data-stu-id="4b40a-161">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="4b40a-162">次のコードは、 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)を呼び出して、キャッシュされた時間を取得します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-162">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="4b40a-163"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>、および[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)は、の<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>機能を拡張する[cacheextensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)クラスの拡張メソッドです。</span><span class="sxs-lookup"><span data-stu-id="4b40a-163"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="4b40a-164">他のキャッシュメソッドの詳細については、「 [IMemoryCache メソッド](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)と[cacheextensions メソッド](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4b40a-164">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="4b40a-165">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="4b40a-165">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="4b40a-166">次の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-166">The following sample:</span></span>

* <span data-ttu-id="4b40a-167">絶対有効期限を設定します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-167">Sets the absolute expiration time.</span></span> <span data-ttu-id="4b40a-168">これは、エントリをキャッシュできる最大時間であり、スライド式有効期限が継続的に更新されるときに、項目が古くなるのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-168">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
* <span data-ttu-id="4b40a-169">スライド式有効期限を設定します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-169">Sets a sliding expiration time.</span></span> <span data-ttu-id="4b40a-170">このキャッシュされた項目にアクセスする要求は、スライド式有効期限をリセットします。</span><span class="sxs-lookup"><span data-stu-id="4b40a-170">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="4b40a-171">キャッシュの優先順位を`CacheItemPriority.NeverRemove`に設定します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-171">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="4b40a-172">エントリがキャッシュから削除された後に呼び出される[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)を設定します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-172">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="4b40a-173">コールバックは、キャッシュから項目を削除するコードとは異なるスレッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-173">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="4b40a-174">SetSize、サイズ、および SizeLimit を使用してキャッシュサイズを制限する</span><span class="sxs-lookup"><span data-stu-id="4b40a-174">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="4b40a-175">インスタンス`MemoryCache`では、必要に応じてサイズ制限を指定して適用できます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-175">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="4b40a-176">キャッシュにはエントリのサイズを測定する機構がないため、メモリサイズの制限には定義された測定単位がありません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-176">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="4b40a-177">キャッシュメモリサイズの制限が設定されている場合、すべてのエントリでサイズを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-177">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="4b40a-178">ASP.NET Core ランタイムでは、メモリ負荷に基づいてキャッシュサイズが制限されません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-178">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="4b40a-179">キャッシュサイズを制限するのは開発者だけです。</span><span class="sxs-lookup"><span data-stu-id="4b40a-179">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="4b40a-180">指定されたサイズは、開発者が選択した単位で示されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-180">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="4b40a-181">例:</span><span class="sxs-lookup"><span data-stu-id="4b40a-181">For example:</span></span>

* <span data-ttu-id="4b40a-182">Web アプリが主に文字列をキャッシュしている場合は、各キャッシュエントリのサイズを文字列の長さにすることができます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-182">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="4b40a-183">アプリでは、すべてのエントリのサイズを1と指定することができ、サイズ制限はエントリの数です。</span><span class="sxs-lookup"><span data-stu-id="4b40a-183">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="4b40a-184">次のコードでは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアクセス可能な、単位固定サイズの[memorycache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1)が作成されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-184">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="4b40a-185">[Sizelimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)に単位がありません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-185">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="4b40a-186">キャッシュメモリサイズが設定されている場合、キャッシュされたエントリは、最適と思われる任意の単位でサイズを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-186">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="4b40a-187">キャッシュインスタンスのすべてのユーザーは、同じ単体システムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-187">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="4b40a-188">キャッシュされたエントリサイズの合計がで`SizeLimit`指定された値を超えた場合、エントリはキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-188">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="4b40a-189">キャッシュサイズの制限が設定されていない場合、エントリに設定されているキャッシュサイズは無視されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-189">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="4b40a-190">次のコードは`MyMemoryCache` 、[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-190">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="4b40a-191">`MyMemoryCache`は、このサイズ制限付きキャッシュを認識し、キャッシュエントリサイズを適切に設定する方法を理解しているコンポーネントの独立したメモリキャッシュとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-191">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="4b40a-192">次のコードで`MyMemoryCache`は、を使用します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-192">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="4b40a-193">キャッシュエントリのサイズは、 [size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)または[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)拡張メソッドを使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-193">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="4b40a-194">キャッシュの依存関係</span><span class="sxs-lookup"><span data-stu-id="4b40a-194">Cache dependencies</span></span>

<span data-ttu-id="4b40a-195">次のサンプルは、依存エントリの有効期限が切れた場合に、キャッシュエントリを期限切れにする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4b40a-195">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="4b40a-196">`CancellationChangeToken`がキャッシュされた項目に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-196">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="4b40a-197">でが呼び出される`Cancel`と、両方のキャッシュエントリが削除されます。`CancellationTokenSource`</span><span class="sxs-lookup"><span data-stu-id="4b40a-197">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="4b40a-198">を使用すると、複数のキャッシュエントリを1つのグループとして削除できます。`CancellationTokenSource`</span><span class="sxs-lookup"><span data-stu-id="4b40a-198">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="4b40a-199">上記のコードの`using` パターンでは、ブロック内に作成されたキャッシュエントリによって、トリガーと有効期限の設定が継承されます。`using`</span><span class="sxs-lookup"><span data-stu-id="4b40a-199">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="4b40a-200">補足メモ</span><span class="sxs-lookup"><span data-stu-id="4b40a-200">Additional notes</span></span>

* <span data-ttu-id="4b40a-201">コールバックを使用してキャッシュ項目を再作成する場合:</span><span class="sxs-lookup"><span data-stu-id="4b40a-201">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="4b40a-202">コールバックが完了していないため、複数の要求でキャッシュされたキー値を空にすることができます。</span><span class="sxs-lookup"><span data-stu-id="4b40a-202">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="4b40a-203">これにより、複数のスレッドがキャッシュされた項目を再作成する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4b40a-203">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="4b40a-204">あるキャッシュエントリを使用して別のキャッシュエントリを作成すると、子は親エントリの有効期限トークンと時間ベースの有効期限の設定をコピーします。</span><span class="sxs-lookup"><span data-stu-id="4b40a-204">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="4b40a-205">親エントリを手動で削除または更新しても、子の有効期限は切れません。</span><span class="sxs-lookup"><span data-stu-id="4b40a-205">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="4b40a-206">キャッシュからキャッシュエントリが削除された後に起動されるコールバックを設定するには、 [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)を使用します。</span><span class="sxs-lookup"><span data-stu-id="4b40a-206">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b40a-207">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4b40a-207">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
