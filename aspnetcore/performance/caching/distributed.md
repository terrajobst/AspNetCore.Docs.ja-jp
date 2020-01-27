---
title: ASP.NET Core での分散キャッシュ
author: guardrex
description: 特にクラウドまたはサーバーファーム環境で、ASP.NET Core 分散キャッシュを使用してアプリのパフォーマンスとスケーラビリティを向上させる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/caching/distributed
ms.openlocfilehash: 3e039a26505aed1bcc0299880039760fef19fd67
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727234"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="37306-103">ASP.NET Core での分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="37306-104">[Luke Latham](https://github.com/guardrex)、 [Mohsin nasir](https://github.com/mohsinnasir)、および[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="37306-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="37306-105">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="37306-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="37306-106">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="37306-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="37306-107">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="37306-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="37306-108">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="37306-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="37306-109">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="37306-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="37306-110">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="37306-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="37306-111">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="37306-111">Doesn't use local memory.</span></span>

<span data-ttu-id="37306-112">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="37306-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="37306-113">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="37306-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="37306-114">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="37306-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="37306-115">どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="37306-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="37306-116">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="37306-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="37306-117">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="37306-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37306-118">SQL Server 分散キャッシュを使用するには、[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="37306-119">Redis 分散キャッシュを使用するには、 [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="37306-120">NCache 分散キャッシュを使用するには、パッケージ参照を[Ncache. Microsoft. Extensions. cache-control. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="37306-121">SQL Server 分散キャッシュを使用するには、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="37306-122">Redis 分散キャッシュを使用するには、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照し、 [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="37306-123">Redis パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイルで Redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37306-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="37306-124">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-124">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="37306-125">NCache パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37306-125">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="37306-126">SQL Server 分散キャッシュを使用するには、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-126">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="37306-127">Redis 分散キャッシュを使用するには、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照し[Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)参照をパッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-127">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="37306-128">Redis パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイルで Redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37306-128">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="37306-129">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-129">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="37306-130">NCache パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37306-130">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="37306-131">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="37306-131">IDistributedCache interface</span></span>

<span data-ttu-id="37306-132"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="37306-132">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="37306-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="37306-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="37306-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="37306-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="37306-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="37306-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="37306-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="37306-137">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="37306-137">Establish distributed caching services</span></span>

<span data-ttu-id="37306-138">`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="37306-138">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="37306-139">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="37306-139">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="37306-140">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-140">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="37306-141">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-141">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="37306-142">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="37306-142">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="37306-143">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-143">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="37306-144">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-144">Distributed Memory Cache</span></span>

<span data-ttu-id="37306-145">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。</span><span class="sxs-lookup"><span data-stu-id="37306-145">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="37306-146">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="37306-146">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="37306-147">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="37306-147">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="37306-148">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="37306-148">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="37306-149">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="37306-149">In development and testing scenarios.</span></span>
* <span data-ttu-id="37306-150">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="37306-150">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="37306-151">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="37306-151">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="37306-152">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="37306-152">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="37306-153">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="37306-153">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="37306-154">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-154">Distributed SQL Server Cache</span></span>

<span data-ttu-id="37306-155">分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="37306-155">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="37306-156">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="37306-156">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="37306-157">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="37306-157">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="37306-158">`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="37306-158">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="37306-159">SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="37306-159">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="37306-160">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="37306-160">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="37306-161">`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="37306-161">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="37306-163">アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="37306-163">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="37306-164">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。</span><span class="sxs-lookup"><span data-stu-id="37306-164">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="37306-165"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>/ 通常、 (およびオプションで、および)はソース管理の外部に格納されます(たとえば、[シークレット マネージャー](xref:security/app-secrets)またはappsettingsappsettingsに格納されます。{<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> *ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="37306-165">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="37306-166">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="37306-166">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="37306-167">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="37306-167">Distributed Redis Cache</span></span>

<span data-ttu-id="37306-168">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="37306-168">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="37306-169">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="37306-169">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37306-170">アプリでは、`Startup.ConfigureServices`の以外の開発環境で <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) を使用して、キャッシュ実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="37306-170">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="37306-171">アプリでは、`Startup.ConfigureServices`の以外の開発環境で <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) を使用して、キャッシュ実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="37306-171">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="37306-172">アプリでは、<xref:Microsoft.Extensions.Caching.Redis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>) を使用してキャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="37306-172">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="37306-173">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="37306-173">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="37306-174">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="37306-174">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="37306-175">コマンドプロンプトから `redis-server` を実行します。</span><span class="sxs-lookup"><span data-stu-id="37306-175">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="37306-176">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="37306-176">Distributed NCache Cache</span></span>

<span data-ttu-id="37306-177">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="37306-177">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="37306-178">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="37306-178">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="37306-179">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="37306-179">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="37306-180">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="37306-180">To configure NCache:</span></span>

1. <span data-ttu-id="37306-181">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="37306-181">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="37306-182">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="37306-182">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="37306-183">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="37306-183">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="37306-184">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="37306-184">Use the distributed cache</span></span>

<span data-ttu-id="37306-185"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="37306-185">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="37306-186">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="37306-186">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37306-187">サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="37306-187">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="37306-188">現在の時刻は <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="37306-188">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37306-189">サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="37306-189">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="37306-190">現在の時刻は <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="37306-190">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="37306-191">このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="37306-191">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="37306-192">インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="37306-192">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="37306-193">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="37306-193">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="37306-194">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="37306-194">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="37306-195">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="37306-195">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="37306-196">このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="37306-196">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="37306-197"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="37306-197">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="37306-198">DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="37306-198">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="37306-199">推奨事項</span><span class="sxs-lookup"><span data-stu-id="37306-199">Recommendations</span></span>

<span data-ttu-id="37306-200">アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="37306-200">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="37306-201">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="37306-201">Existing infrastructure</span></span>
* <span data-ttu-id="37306-202">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="37306-202">Performance requirements</span></span>
* <span data-ttu-id="37306-203">Cost</span><span class="sxs-lookup"><span data-stu-id="37306-203">Cost</span></span>
* <span data-ttu-id="37306-204">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="37306-204">Team experience</span></span>

<span data-ttu-id="37306-205">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="37306-205">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="37306-206">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="37306-206">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="37306-207">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="37306-207">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="37306-208">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="37306-208">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="37306-209">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="37306-209">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="37306-210">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="37306-210">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37306-211">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="37306-211">Additional resources</span></span>

* [<span data-ttu-id="37306-212">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="37306-212">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="37306-213">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="37306-213">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="37306-214">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37306-214">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
