---
title: ASP.NET Core での分散キャッシュ
author: guardrex
description: 特にクラウドまたはサーバーファーム環境で、ASP.NET Core 分散キャッシュを使用してアプリのパフォーマンスとスケーラビリティを向上させる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: performance/caching/distributed
ms.openlocfilehash: dbcdfcd07877fabfe6d18cd4d840b5597afa1afd
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081547"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="bd5c1-103">ASP.NET Core での分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="bd5c1-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="bd5c1-104">[Luke Latham](https://github.com/guardrex)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bd5c1-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bd5c1-105">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="bd5c1-106">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="bd5c1-107">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="bd5c1-108">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="bd5c1-109">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="bd5c1-110">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="bd5c1-111">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-111">Doesn't use local memory.</span></span>

<span data-ttu-id="bd5c1-112">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="bd5c1-113">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="bd5c1-114">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="bd5c1-115">どの実装が選択されているかにかかわらず、アプリは<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="bd5c1-116">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bd5c1-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="bd5c1-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd5c1-118">SQL Server 分散キャッシュを使用するには、パッケージ[への参照を追加します](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="bd5c1-119">Redis 分散キャッシュを使用するには、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bd5c1-120">SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-120">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="bd5c1-121">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-121">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="bd5c1-122">Redis パッケージは`Microsoft.AspNetCore.App`パッケージに含まれていないため、プロジェクトファイルで redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-122">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="bd5c1-123">SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-123">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="bd5c1-124">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し[て、パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)参照をパッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-124">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="bd5c1-125">Redis パッケージは`Microsoft.AspNetCore.App`パッケージに含まれていないため、プロジェクトファイルで redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-125">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="bd5c1-126">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="bd5c1-126">IDistributedCache interface</span></span>

<span data-ttu-id="bd5c1-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-127">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="bd5c1-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> `byte[]`文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を配列として取得します。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="bd5c1-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="bd5c1-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> 、文字列キーを使用`byte[]`して、 &ndash;キャッシュに項目 (配列として) を追加します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="bd5c1-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> 、&ndash;キーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="bd5c1-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>は<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> 、&ndash;文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="bd5c1-132">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="bd5c1-132">Establish distributed caching services</span></span>

<span data-ttu-id="bd5c1-133">のの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装をに`Startup.ConfigureServices`登録します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-133">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bd5c1-134">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-134">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="bd5c1-135">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="bd5c1-135">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="bd5c1-136">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="bd5c1-136">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="bd5c1-137">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="bd5c1-137">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="bd5c1-138">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="bd5c1-138">Distributed Memory Cache</span></span>

<span data-ttu-id="bd5c1-139">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、項目をメモリに格納するのフレームワーク提供の<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装です。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="bd5c1-140">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="bd5c1-141">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="bd5c1-142">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="bd5c1-143">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="bd5c1-144">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="bd5c1-145">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="bd5c1-146">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="bd5c1-147">このサンプルアプリでは、の`Startup.ConfigureServices`開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="bd5c1-148">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="bd5c1-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="bd5c1-149">分散型 SQL Server キャッシュの実装<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>() を使用すると、分散キャッシュで SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="bd5c1-150">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、 `sql-cache`ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="bd5c1-151">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-151">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="bd5c1-152">`sql-cache create`コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-152">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="bd5c1-153">SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、 `dbo`スキーマ (など)、テーブル名 ( `TestCache`など) を指定します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-153">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="bd5c1-154">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-154">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="bd5c1-155">`sql-cache`ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-155">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="bd5c1-157">アプリでは、では<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>なく、のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-157">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="bd5c1-158">このサンプルアプリは<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 、の`Startup.ConfigureServices`開発環境以外で実装されています。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-158">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="bd5c1-159"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>/ 通常、 (およびオプションで、および)はソース管理の外部に格納されます(たとえば、[シークレット マネージャー](xref:security/app-secrets)またはappsettingsappsettingsに格納されます。{<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> *ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-159">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="bd5c1-160">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-160">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="bd5c1-161">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="bd5c1-161">Distributed Redis Cache</span></span>

<span data-ttu-id="bd5c1-162">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-162">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="bd5c1-163">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-163">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd5c1-164">アプリでは、の<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> `Startup.ConfigureServices`開発以外の環境で<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>インスタンス () を使用して、キャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-164">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="bd5c1-165">アプリでは、の<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> `Startup.ConfigureServices`開発以外の環境で<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>インスタンス () を使用して、キャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-165">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="bd5c1-166">アプリは、 <xref:Microsoft.Extensions.Caching.Redis.RedisCache>インスタンス (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>) を使用してキャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-166">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="bd5c1-167">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-167">To install Redis on your local machine:</span></span>

* <span data-ttu-id="bd5c1-168">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-168">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="bd5c1-169">コマンド`redis-server`プロンプトからを実行します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-169">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="bd5c1-170">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="bd5c1-170">Use the distributed cache</span></span>

<span data-ttu-id="bd5c1-171"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インターフェイスを使用するには、アプリの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>任意のコンストラクターからのインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-171">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="bd5c1-172">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-172">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd5c1-173">サンプルアプリが起動すると<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、がに`Startup.Configure`挿入されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-173">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="bd5c1-174">現在の時刻はを使用<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>してキャッシュされます[(詳細については、「汎用ホスト:IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="bd5c1-174">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd5c1-175">サンプルアプリが起動すると<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 、がに`Startup.Configure`挿入されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="bd5c1-176">現在の時刻はを使用<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>してキャッシュされます[(詳細については、「Web Host:IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="bd5c1-176">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="bd5c1-177">サンプルアプリは、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>インデックスページ`IndexModel`で使用するために、に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="bd5c1-178">インデックスページが読み込まれるたびに、キャッシュがで`OnGetAsync`キャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="bd5c1-179">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="bd5c1-180">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="bd5c1-181">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="bd5c1-182">このボタンをクリック`OnPostResetCachedTime`すると、ハンドラーメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="bd5c1-183">インスタンスに対して<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>シングルトンまたはスコープ設定された有効期間を使用する必要はありません (少なくとも組み込み実装の場合)。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="bd5c1-184">また、DI を使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>する代わりに、必要に応じてインスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="bd5c1-185">推奨事項</span><span class="sxs-lookup"><span data-stu-id="bd5c1-185">Recommendations</span></span>

<span data-ttu-id="bd5c1-186">アプリに最適なの<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>実装を決定する際には、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="bd5c1-187">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="bd5c1-187">Existing infrastructure</span></span>
* <span data-ttu-id="bd5c1-188">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="bd5c1-188">Performance requirements</span></span>
* <span data-ttu-id="bd5c1-189">コスト</span><span class="sxs-lookup"><span data-stu-id="bd5c1-189">Cost</span></span>
* <span data-ttu-id="bd5c1-190">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="bd5c1-190">Team experience</span></span>

<span data-ttu-id="bd5c1-191">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="bd5c1-192">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="bd5c1-193">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="bd5c1-194">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="bd5c1-195">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="bd5c1-196">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bd5c1-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd5c1-197">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bd5c1-197">Additional resources</span></span>

* [<span data-ttu-id="bd5c1-198">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="bd5c1-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="bd5c1-199">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="bd5c1-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="bd5c1-200">[Web ファームでの NCache 用の IDistributedCache Provider の ASP.NET Core](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)([GitHub の Ncache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="bd5c1-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
