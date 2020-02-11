---
title: ASP.NET Core での分散キャッシュ
author: guardrex
description: 特にクラウドまたはサーバーファーム環境で、ASP.NET Core 分散キャッシュを使用してアプリのパフォーマンスとスケーラビリティを向上させる方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/caching/distributed
ms.openlocfilehash: d39ac6c7496de7cf9dc8d40718bbaf611e744c19
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114745"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="f312e-103">ASP.NET Core での分散キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="f312e-104">[Luke Latham](https://github.com/guardrex)、 [Mohsin nasir](https://github.com/mohsinnasir)、および[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f312e-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f312e-105">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="f312e-106">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="f312e-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="f312e-107">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="f312e-108">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f312e-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="f312e-109">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="f312e-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="f312e-110">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="f312e-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="f312e-111">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="f312e-111">Doesn't use local memory.</span></span>

<span data-ttu-id="f312e-112">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="f312e-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="f312e-113">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f312e-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="f312e-114">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="f312e-115">どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="f312e-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="f312e-116">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f312e-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f312e-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="f312e-117">Prerequisites</span></span>

<span data-ttu-id="f312e-118">SQL Server 分散キャッシュを使用するには、パッケージ[への参照を追加します](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)。</span><span class="sxs-lookup"><span data-stu-id="f312e-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="f312e-119">Redis 分散キャッシュを使用するには、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="f312e-120">NCache 分散キャッシュを使用するには、パッケージ参照を[Ncache. Microsoft. Extensions. cache-control. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="f312e-121">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="f312e-121">IDistributedCache interface</span></span>

<span data-ttu-id="f312e-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="f312e-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="f312e-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="f312e-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="f312e-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="f312e-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="f312e-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="f312e-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="f312e-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="f312e-127">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="f312e-127">Establish distributed caching services</span></span>

<span data-ttu-id="f312e-128">`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="f312e-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f312e-129">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f312e-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="f312e-130">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="f312e-131">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="f312e-132">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="f312e-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="f312e-133">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="f312e-134">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-134">Distributed Memory Cache</span></span>

<span data-ttu-id="f312e-135">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。</span><span class="sxs-lookup"><span data-stu-id="f312e-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="f312e-136">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="f312e-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="f312e-137">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="f312e-138">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f312e-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="f312e-139">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="f312e-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="f312e-140">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="f312e-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="f312e-141">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="f312e-142">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="f312e-143">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="f312e-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="f312e-144">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="f312e-145">分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="f312e-146">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f312e-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="f312e-147">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="f312e-148">`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="f312e-149">SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="f312e-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="f312e-150">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="f312e-151">`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="f312e-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="f312e-153">アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="f312e-154">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。</span><span class="sxs-lookup"><span data-stu-id="f312e-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="f312e-155">通常、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (および必要に応じて <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> と <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings. json*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="f312e-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="f312e-156">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f312e-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="f312e-157">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f312e-157">Distributed Redis Cache</span></span>

<span data-ttu-id="f312e-158">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="f312e-159">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="f312e-160">アプリでは、`Startup.ConfigureServices`の以外の開発環境で <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) を使用して、キャッシュ実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="f312e-161">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f312e-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="f312e-162">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f312e-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="f312e-163">コマンドプロンプトから `redis-server` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f312e-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="f312e-164">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-164">Distributed NCache Cache</span></span>

<span data-ttu-id="f312e-165">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="f312e-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="f312e-166">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="f312e-167">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f312e-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="f312e-168">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="f312e-168">To configure NCache:</span></span>

1. <span data-ttu-id="f312e-169">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f312e-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="f312e-170">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="f312e-171">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="f312e-172">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="f312e-172">Use the distributed cache</span></span>

<span data-ttu-id="f312e-173"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="f312e-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="f312e-174">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f312e-175">サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="f312e-176">現在の時刻は <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="f312e-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="f312e-177">このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="f312e-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="f312e-178">インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="f312e-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="f312e-179">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="f312e-180">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="f312e-181">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="f312e-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="f312e-182">このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f312e-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="f312e-183"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f312e-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="f312e-184">DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="f312e-185">Recommendations</span><span class="sxs-lookup"><span data-stu-id="f312e-185">Recommendations</span></span>

<span data-ttu-id="f312e-186">アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="f312e-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="f312e-187">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="f312e-187">Existing infrastructure</span></span>
* <span data-ttu-id="f312e-188">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="f312e-188">Performance requirements</span></span>
* <span data-ttu-id="f312e-189">コスト</span><span class="sxs-lookup"><span data-stu-id="f312e-189">Cost</span></span>
* <span data-ttu-id="f312e-190">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="f312e-190">Team experience</span></span>

<span data-ttu-id="f312e-191">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="f312e-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="f312e-192">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="f312e-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="f312e-193">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="f312e-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="f312e-194">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="f312e-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="f312e-195">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="f312e-196">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f312e-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f312e-197">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f312e-197">Additional resources</span></span>

* [<span data-ttu-id="f312e-198">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f312e-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="f312e-199">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="f312e-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="f312e-200">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f312e-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f312e-201">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="f312e-202">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="f312e-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="f312e-203">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="f312e-204">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f312e-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="f312e-205">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="f312e-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="f312e-206">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="f312e-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="f312e-207">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="f312e-207">Doesn't use local memory.</span></span>

<span data-ttu-id="f312e-208">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="f312e-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="f312e-209">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f312e-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="f312e-210">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="f312e-211">どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="f312e-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="f312e-212">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f312e-212">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f312e-213">前提条件</span><span class="sxs-lookup"><span data-stu-id="f312e-213">Prerequisites</span></span>

<span data-ttu-id="f312e-214">SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="f312e-215">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="f312e-216">Redis パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイルで Redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="f312e-217">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="f312e-218">NCache パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="f312e-219">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="f312e-219">IDistributedCache interface</span></span>

<span data-ttu-id="f312e-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="f312e-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="f312e-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="f312e-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="f312e-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="f312e-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="f312e-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="f312e-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="f312e-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="f312e-225">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="f312e-225">Establish distributed caching services</span></span>

<span data-ttu-id="f312e-226">`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="f312e-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f312e-227">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f312e-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="f312e-228">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="f312e-229">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="f312e-230">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="f312e-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="f312e-231">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="f312e-232">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-232">Distributed Memory Cache</span></span>

<span data-ttu-id="f312e-233">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。</span><span class="sxs-lookup"><span data-stu-id="f312e-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="f312e-234">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="f312e-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="f312e-235">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="f312e-236">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f312e-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="f312e-237">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="f312e-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="f312e-238">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="f312e-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="f312e-239">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="f312e-240">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="f312e-241">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="f312e-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="f312e-242">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="f312e-243">分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="f312e-244">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f312e-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="f312e-245">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="f312e-246">`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="f312e-247">SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="f312e-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="f312e-248">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="f312e-249">`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="f312e-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="f312e-251">アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="f312e-252">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。</span><span class="sxs-lookup"><span data-stu-id="f312e-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="f312e-253">通常、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (および必要に応じて <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> と <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings. json*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="f312e-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="f312e-254">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f312e-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="f312e-255">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f312e-255">Distributed Redis Cache</span></span>

<span data-ttu-id="f312e-256">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="f312e-257">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="f312e-258">アプリでは、`Startup.ConfigureServices`の以外の開発環境で <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) を使用して、キャッシュ実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="f312e-259">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f312e-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="f312e-260">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f312e-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="f312e-261">コマンドプロンプトから `redis-server` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f312e-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="f312e-262">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-262">Distributed NCache Cache</span></span>

<span data-ttu-id="f312e-263">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="f312e-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="f312e-264">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="f312e-265">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f312e-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="f312e-266">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="f312e-266">To configure NCache:</span></span>

1. <span data-ttu-id="f312e-267">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f312e-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="f312e-268">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="f312e-269">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="f312e-270">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="f312e-270">Use the distributed cache</span></span>

<span data-ttu-id="f312e-271"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="f312e-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="f312e-272">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f312e-273">サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="f312e-274">現在の時刻は <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="f312e-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="f312e-275">このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="f312e-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="f312e-276">インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="f312e-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="f312e-277">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="f312e-278">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="f312e-279">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="f312e-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="f312e-280">このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f312e-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="f312e-281"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f312e-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="f312e-282">DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="f312e-283">Recommendations</span><span class="sxs-lookup"><span data-stu-id="f312e-283">Recommendations</span></span>

<span data-ttu-id="f312e-284">アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="f312e-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="f312e-285">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="f312e-285">Existing infrastructure</span></span>
* <span data-ttu-id="f312e-286">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="f312e-286">Performance requirements</span></span>
* <span data-ttu-id="f312e-287">コスト</span><span class="sxs-lookup"><span data-stu-id="f312e-287">Cost</span></span>
* <span data-ttu-id="f312e-288">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="f312e-288">Team experience</span></span>

<span data-ttu-id="f312e-289">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="f312e-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="f312e-290">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="f312e-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="f312e-291">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="f312e-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="f312e-292">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="f312e-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="f312e-293">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="f312e-294">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f312e-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f312e-295">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f312e-295">Additional resources</span></span>

* [<span data-ttu-id="f312e-296">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f312e-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="f312e-297">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="f312e-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="f312e-298">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f312e-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f312e-299">分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="f312e-300">分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="f312e-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="f312e-301">分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="f312e-302">キャッシュされたデータが分散されると、データは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f312e-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="f312e-303">は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。</span><span class="sxs-lookup"><span data-stu-id="f312e-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="f312e-304">サーバーの再起動とアプリのデプロイが行われます。</span><span class="sxs-lookup"><span data-stu-id="f312e-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="f312e-305">はローカルメモリを使用しません。</span><span class="sxs-lookup"><span data-stu-id="f312e-305">Doesn't use local memory.</span></span>

<span data-ttu-id="f312e-306">分散キャッシュの構成は実装固有です。</span><span class="sxs-lookup"><span data-stu-id="f312e-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="f312e-307">この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f312e-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="f312e-308">[Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="f312e-309">どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。</span><span class="sxs-lookup"><span data-stu-id="f312e-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="f312e-310">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f312e-310">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f312e-311">前提条件</span><span class="sxs-lookup"><span data-stu-id="f312e-311">Prerequisites</span></span>

<span data-ttu-id="f312e-312">SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="f312e-313">Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し[て、パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)参照をパッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="f312e-314">Redis パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイルで Redis パッケージを個別に参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="f312e-315">NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="f312e-316">NCache パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="f312e-317">IDistributedCache インターフェイス</span><span class="sxs-lookup"><span data-stu-id="f312e-317">IDistributedCache interface</span></span>

<span data-ttu-id="f312e-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="f312e-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="f312e-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。</span><span class="sxs-lookup"><span data-stu-id="f312e-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="f312e-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="f312e-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。</span><span class="sxs-lookup"><span data-stu-id="f312e-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="f312e-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。</span><span class="sxs-lookup"><span data-stu-id="f312e-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="f312e-323">分散キャッシュサービスを確立する</span><span class="sxs-lookup"><span data-stu-id="f312e-323">Establish distributed caching services</span></span>

<span data-ttu-id="f312e-324">`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。</span><span class="sxs-lookup"><span data-stu-id="f312e-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f312e-325">このトピックで説明するフレームワークに用意されている実装は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f312e-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="f312e-326">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="f312e-327">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="f312e-328">分散 Redis cache</span><span class="sxs-lookup"><span data-stu-id="f312e-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="f312e-329">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="f312e-330">分散メモリキャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-330">Distributed Memory Cache</span></span>

<span data-ttu-id="f312e-331">分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。</span><span class="sxs-lookup"><span data-stu-id="f312e-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="f312e-332">分散メモリキャッシュは実際の分散キャッシュではありません。</span><span class="sxs-lookup"><span data-stu-id="f312e-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="f312e-333">キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="f312e-334">分散メモリキャッシュは、次のような実装に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="f312e-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="f312e-335">開発とテストのシナリオで。</span><span class="sxs-lookup"><span data-stu-id="f312e-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="f312e-336">運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。</span><span class="sxs-lookup"><span data-stu-id="f312e-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="f312e-337">分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="f312e-338">これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="f312e-339">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="f312e-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="f312e-340">分散 SQL Server キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="f312e-341">分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="f312e-342">SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f312e-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="f312e-343">このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="f312e-344">`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="f312e-345">SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="f312e-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="f312e-346">ツールが正常に実行されたことを示すメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="f312e-347">`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。</span><span class="sxs-lookup"><span data-stu-id="f312e-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="f312e-349">アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="f312e-350">このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。</span><span class="sxs-lookup"><span data-stu-id="f312e-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="f312e-351">通常、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (および必要に応じて <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> と <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings. json*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。</span><span class="sxs-lookup"><span data-stu-id="f312e-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="f312e-352">接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f312e-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="f312e-353">分散 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f312e-353">Distributed Redis Cache</span></span>

<span data-ttu-id="f312e-354">[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="f312e-355">Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="f312e-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="f312e-356">アプリでは、<xref:Microsoft.Extensions.Caching.Redis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>) を使用してキャッシュの実装を構成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="f312e-357">Redis をローカルコンピューターにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="f312e-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="f312e-358">[Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f312e-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="f312e-359">コマンドプロンプトから `redis-server` を実行します。</span><span class="sxs-lookup"><span data-stu-id="f312e-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="f312e-360">分散 NCache キャッシュ</span><span class="sxs-lookup"><span data-stu-id="f312e-360">Distributed NCache Cache</span></span>

<span data-ttu-id="f312e-361">[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="f312e-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="f312e-362">NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="f312e-363">NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f312e-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="f312e-364">NCache を構成するには:</span><span class="sxs-lookup"><span data-stu-id="f312e-364">To configure NCache:</span></span>

1. <span data-ttu-id="f312e-365">[Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f312e-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="f312e-366">[Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。</span><span class="sxs-lookup"><span data-stu-id="f312e-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="f312e-367">`Startup.ConfigureServices` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="f312e-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="f312e-368">分散キャッシュを使用する</span><span class="sxs-lookup"><span data-stu-id="f312e-368">Use the distributed cache</span></span>

<span data-ttu-id="f312e-369"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。</span><span class="sxs-lookup"><span data-stu-id="f312e-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="f312e-370">インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f312e-371">サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="f312e-372">現在の時刻は <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="f312e-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="f312e-373">このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。</span><span class="sxs-lookup"><span data-stu-id="f312e-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="f312e-374">インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。</span><span class="sxs-lookup"><span data-stu-id="f312e-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="f312e-375">キャッシュされた時間が経過していない場合は、時間が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="f312e-376">キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f312e-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="f312e-377">[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。</span><span class="sxs-lookup"><span data-stu-id="f312e-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="f312e-378">このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f312e-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="f312e-379"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f312e-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="f312e-380">DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="f312e-381">Recommendations</span><span class="sxs-lookup"><span data-stu-id="f312e-381">Recommendations</span></span>

<span data-ttu-id="f312e-382">アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="f312e-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="f312e-383">既存のインフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="f312e-383">Existing infrastructure</span></span>
* <span data-ttu-id="f312e-384">パフォーマンス要件</span><span class="sxs-lookup"><span data-stu-id="f312e-384">Performance requirements</span></span>
* <span data-ttu-id="f312e-385">コスト</span><span class="sxs-lookup"><span data-stu-id="f312e-385">Cost</span></span>
* <span data-ttu-id="f312e-386">チームエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="f312e-386">Team experience</span></span>

<span data-ttu-id="f312e-387">キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="f312e-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="f312e-388">一般的に使用されるデータのみをキャッシュに格納します。</span><span class="sxs-lookup"><span data-stu-id="f312e-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="f312e-389">一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="f312e-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="f312e-390">ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="f312e-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="f312e-391">SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f312e-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="f312e-392">分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f312e-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f312e-393">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f312e-393">Additional resources</span></span>

* [<span data-ttu-id="f312e-394">Azure での Redis Cache</span><span class="sxs-lookup"><span data-stu-id="f312e-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="f312e-395">Azure での SQL Database</span><span class="sxs-lookup"><span data-stu-id="f312e-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="f312e-396">[Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f312e-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
