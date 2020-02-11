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
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core での分散キャッシュ

[Luke Latham](https://github.com/guardrex)、 [Mohsin nasir](https://github.com/mohsinnasir)、および[上田 Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。 分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。

分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。

キャッシュされたデータが分散されると、データは次のようになります。

* は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。
* サーバーの再起動とアプリのデプロイが行われます。
* はローカルメモリを使用しません。

分散キャッシュの構成は実装固有です。 この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。 [Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。 どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

SQL Server 分散キャッシュを使用するには、パッケージ[への参照を追加します](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)。

Redis 分散キャッシュを使用するには、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。

NCache 分散キャッシュを使用するには、パッケージ参照を[Ncache. Microsoft. Extensions. cache-control. OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。

## <a name="idistributedcache-interface"></a>IDistributedCache インターフェイス

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。

## <a name="establish-distributed-caching-services"></a>分散キャッシュサービスを確立する

`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。 このトピックで説明するフレームワークに用意されている実装は次のとおりです。

* [分散メモリキャッシュ](#distributed-memory-cache)
* [分散 SQL Server キャッシュ](#distributed-sql-server-cache)
* [分散 Redis cache](#distributed-redis-cache)
* [分散 NCache キャッシュ](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分散メモリキャッシュ

分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。 分散メモリキャッシュは実際の分散キャッシュではありません。 キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。

分散メモリキャッシュは、次のような実装に役立ちます。

* 開発とテストのシナリオで。
* 運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。 分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。 これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。

このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分散 SQL Server キャッシュ

分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。 SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。 このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。

`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。 SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

ツールが正常に実行されたことを示すメッセージがログに記録されます。

```console
Table and index were created successfully.
```

`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。

このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> 通常、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (および必要に応じて <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> と <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings. json*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。 接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。

### <a name="distributed-redis-cache"></a>分散 Redis Cache

[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。 Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。

アプリでは、`Startup.ConfigureServices`の以外の開発環境で <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) を使用して、キャッシュ実装を構成します。

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Redis をローカルコンピューターにインストールするには、次のようにします。

1. [Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。
1. コマンドプロンプトから `redis-server` を実行します。

### <a name="distributed-ncache-cache"></a>分散 NCache キャッシュ

[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。 NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。

NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。

NCache を構成するには:

1. [Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。
1. [Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。
1. `Startup.ConfigureServices` に次のコードを追加します。

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>分散キャッシュを使用する

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。 インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。

サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。 現在の時刻は <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)」を参照してください)。

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。

インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。 キャッシュされた時間が経過していない場合は、時間が表示されます。 キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。

[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。 このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。
>
> DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。

## <a name="recommendations"></a>Recommendations

アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。

* 既存のインフラストラクチャ
* パフォーマンス要件
* コスト
* チームエクスペリエンス

キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。 一般的に使用されるデータのみをキャッシュに格納します。

一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。 ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。

SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。 分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。

## <a name="additional-resources"></a>その他のリソース

* [Azure での Redis Cache](/azure/azure-cache-for-redis/)
* [Azure での SQL Database](/azure/sql-database/)
* [Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。 分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。

分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。

キャッシュされたデータが分散されると、データは次のようになります。

* は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。
* サーバーの再起動とアプリのデプロイが行われます。
* はローカルメモリを使用しません。

分散キャッシュの構成は実装固有です。 この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。 [Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。 どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。

Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)パッケージへのパッケージ参照を追加します。 Redis パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイルで Redis パッケージを個別に参照する必要があります。

NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。 NCache パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。

## <a name="idistributedcache-interface"></a>IDistributedCache インターフェイス

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。

## <a name="establish-distributed-caching-services"></a>分散キャッシュサービスを確立する

`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。 このトピックで説明するフレームワークに用意されている実装は次のとおりです。

* [分散メモリキャッシュ](#distributed-memory-cache)
* [分散 SQL Server キャッシュ](#distributed-sql-server-cache)
* [分散 Redis cache](#distributed-redis-cache)
* [分散 NCache キャッシュ](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分散メモリキャッシュ

分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。 分散メモリキャッシュは実際の分散キャッシュではありません。 キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。

分散メモリキャッシュは、次のような実装に役立ちます。

* 開発とテストのシナリオで。
* 運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。 分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。 これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。

このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分散 SQL Server キャッシュ

分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。 SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。 このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。

`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。 SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

ツールが正常に実行されたことを示すメッセージがログに記録されます。

```console
Table and index were created successfully.
```

`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。

このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> 通常、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (および必要に応じて <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> と <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings. json*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。 接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。

### <a name="distributed-redis-cache"></a>分散 Redis Cache

[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。 Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。

アプリでは、`Startup.ConfigureServices`の以外の開発環境で <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) を使用して、キャッシュ実装を構成します。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

Redis をローカルコンピューターにインストールするには、次のようにします。

1. [Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。
1. コマンドプロンプトから `redis-server` を実行します。

### <a name="distributed-ncache-cache"></a>分散 NCache キャッシュ

[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。 NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。

NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。

NCache を構成するには:

1. [Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。
1. [Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。
1. `Startup.ConfigureServices` に次のコードを追加します。

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>分散キャッシュを使用する

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。 インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。

サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。 現在の時刻は <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。

インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。 キャッシュされた時間が経過していない場合は、時間が表示されます。 キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。

[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。 このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。
>
> DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。

## <a name="recommendations"></a>Recommendations

アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。

* 既存のインフラストラクチャ
* パフォーマンス要件
* コスト
* チームエクスペリエンス

キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。 一般的に使用されるデータのみをキャッシュに格納します。

一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。 ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。

SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。 分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。

## <a name="additional-resources"></a>その他のリソース

* [Azure での Redis Cache](/azure/azure-cache-for-redis/)
* [Azure での SQL Database](/azure/sql-database/)
* [Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

分散キャッシュは、複数のアプリサーバーによって共有されるキャッシュであり、通常、それにアクセスするアプリサーバーに外部サービスとして保持されます。 分散キャッシュを使用すると、特にアプリがクラウドサービスまたはサーバーファームでホストされている場合に、ASP.NET Core アプリのパフォーマンスとスケーラビリティを向上させることができます。

分散キャッシュには、キャッシュされたデータが個々のアプリサーバーに格納される他のキャッシュシナリオよりも、いくつかの利点があります。

キャッシュされたデータが分散されると、データは次のようになります。

* は、複数のサーバーに対する複数の要求にわたっ*て一貫し*ています。
* サーバーの再起動とアプリのデプロイが行われます。
* はローカルメモリを使用しません。

分散キャッシュの構成は実装固有です。 この記事では、SQL Server と Redis の分散キャッシュを構成する方法について説明します。 [Ncache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) などのサードパーティの実装も利用できます。 どの実装が選択されているかにかかわらず、アプリは <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用してキャッシュと対話します。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>前提条件

SQL Server 分散キャッシュを使用するには、[メタパッケージ](xref:fundamentals/metapackage-app)を参照するか、AspNetCore[パッケージへのパッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)参照を追加します。

Redis 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し[て、パッケージ](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)参照をパッケージに追加します。 Redis パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイルで Redis パッケージを個別に参照する必要があります。

NCache 分散キャッシュを使用するには、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)を参照し、パッケージ参照を ncache. [opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)パッケージに追加します。 NCache パッケージは `Microsoft.AspNetCore.App` パッケージに含まれていないため、プロジェクトファイル内で個別に NCache パッケージを参照する必要があります。

## <a name="idistributedcache-interface"></a>IDistributedCache インターフェイス

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスには、分散キャッシュ実装の項目を操作するための次のメソッドが用意されています。

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; は文字列キーを受け取り、キャッシュ内に存在する場合は、キャッシュされた項目を `byte[]` 配列として取得します。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 文字列キーを使用して (`byte[]` 配列として) 項目をキャッシュに追加します。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; はキーに基づいてキャッシュ内の項目を更新し、スライド式有効期限タイムアウト (存在する場合) をリセットします。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 文字列キーに基づいてキャッシュ項目を削除します。

## <a name="establish-distributed-caching-services"></a>分散キャッシュサービスを確立する

`Startup.ConfigureServices`に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を登録します。 このトピックで説明するフレームワークに用意されている実装は次のとおりです。

* [分散メモリキャッシュ](#distributed-memory-cache)
* [分散 SQL Server キャッシュ](#distributed-sql-server-cache)
* [分散 Redis cache](#distributed-redis-cache)
* [分散 NCache キャッシュ](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分散メモリキャッシュ

分散メモリキャッシュ (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) は、メモリに項目を格納する <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のフレームワークによって提供される実装です。 分散メモリキャッシュは実際の分散キャッシュではありません。 キャッシュされた項目は、アプリが実行されているサーバー上のアプリインスタンスによって格納されます。

分散メモリキャッシュは、次のような実装に役立ちます。

* 開発とテストのシナリオで。
* 運用環境で1台のサーバーが使用されていて、メモリの消費量が問題ではない場合。 分散メモリキャッシュを実装すると、キャッシュされたデータストレージが抽象化されます。 これにより、複数のノードまたはフォールトトレランスが必要になった場合に、真の分散キャッシュソリューションを実装できます。

このサンプルアプリでは、`Startup.ConfigureServices`の開発環境でアプリを実行するときに、分散メモリキャッシュを使用します。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分散 SQL Server キャッシュ

分散 SQL Server キャッシュの実装 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) を使用すると、分散キャッシュは、SQL Server データベースをバッキングストアとして使用できます。 SQL Server インスタンスに SQL Server キャッシュされた項目テーブルを作成するには、`sql-cache` ツールを使用します。 このツールでは、指定した名前とスキーマを使用してテーブルが作成されます。

`sql-cache create` コマンドを実行して SQL Server にテーブルを作成します。 SQL Server インスタンス (`Data Source`)、データベース (`Initial Catalog`)、スキーマ (`dbo`など)、テーブル名 (たとえば、`TestCache`) を指定します。

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

ツールが正常に実行されたことを示すメッセージがログに記録されます。

```console
Table and index were created successfully.
```

`sql-cache` ツールによって作成されたテーブルには、次のスキーマがあります。

![SqlServer キャッシュテーブル](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> アプリでは、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>ではなく <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>のインスタンスを使用してキャッシュ値を操作する必要があります。

このサンプルアプリでは、`Startup.ConfigureServices`の開発環境以外で <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> を実装しています。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> 通常、<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (および必要に応じて <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> と <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) は、ソース管理の外部に格納されます (たとえば、[シークレットマネージャー](xref:security/app-secrets)または*appsettings. json*/appsettings に格納され*ます。 {ENVIRONMENT} json*ファイル)。 接続文字列には、ソース管理システムから保持する必要がある資格情報を含めることができます。

### <a name="distributed-redis-cache"></a>分散 Redis Cache

[Redis](https://redis.io/)は、オープンソースのメモリ内データストアであり、多くの場合、分散キャッシュとして使用されます。 Redis はローカルで使用でき、Azure でホストされる ASP.NET Core アプリの[Azure Redis Cache](https://azure.microsoft.com/services/cache/)を構成できます。

アプリでは、<xref:Microsoft.Extensions.Caching.Redis.RedisCache> インスタンス (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>) を使用してキャッシュの実装を構成します。

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

Redis をローカルコンピューターにインストールするには、次のようにします。

1. [Chocolatey Redis パッケージ](https://chocolatey.org/packages/redis-64/)をインストールします。
1. コマンドプロンプトから `redis-server` を実行します。

### <a name="distributed-ncache-cache"></a>分散 NCache キャッシュ

[Ncache](https://github.com/Alachisoft/NCache)は、.NET および .net Core でネイティブに開発されたオープンソースのメモリ内分散キャッシュです。 NCache はローカルに動作し、Azure または他のホスティングプラットフォームで実行される ASP.NET Core アプリの分散キャッシュクラスターとして構成されます。

NCache をローカルコンピューターにインストールして構成するには、「 [ncache はじめに Guide For Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)」を参照してください。

NCache を構成するには:

1. [Ncache オープンソース NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)をインストールします。
1. [Ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)でキャッシュクラスターを構成します。
1. `Startup.ConfigureServices` に次のコードを追加します。

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>分散キャッシュを使用する

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インターフェイスを使用するには、アプリの任意のコンストラクターから <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> のインスタンスを要求します。 インスタンスは、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)によって提供されます。

サンプルアプリが起動すると、<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> が `Startup.Configure`に挿入されます。 現在の時刻は <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> を使用してキャッシュされます (詳細については、「 [Web Host: IApplicationLifetime インターフェイス](xref:fundamentals/host/web-host#iapplicationlifetime-interface)」を参照してください)。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

このサンプルアプリでは、インデックスページで使用するために <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> を `IndexModel` に挿入します。

インデックスページが読み込まれるたびに、キャッシュが `OnGetAsync`にキャッシュされた時間についてチェックされます。 キャッシュされた時間が経過していない場合は、時間が表示されます。 キャッシュされた時間が最後にアクセスされてから20秒経過した場合 (このページが最後に読み込まれたとき)、ページにはキャッシュされた*時間*が表示されます。

[キャッシュされた時間の**リセット**] ボタンを選択して、キャッシュされた時刻を現在の時刻に直ちに更新します。 このボタンをクリックすると、`OnPostResetCachedTime` ハンドラメソッドがトリガーされます。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンス (少なくとも組み込みの実装では) に、シングルトンまたはスコープ設定された有効期間を使用する必要はありません。
>
> DI を使用する代わりに、必要な場所に <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> インスタンスを作成することもできますが、コードでインスタンスを作成すると、コードのテストが難しくなり、[明示的な依存関係の原則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)に違反する可能性があります。

## <a name="recommendations"></a>Recommendations

アプリに最適な <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> の実装を決定するときは、次の点を考慮してください。

* 既存のインフラストラクチャ
* パフォーマンス要件
* コスト
* チームエクスペリエンス

キャッシュソリューションは、通常、キャッシュされたデータを高速に取得するためにインメモリストレージに依存しますが、メモリは限られたリソースであり、拡張にはコストがかかります。 一般的に使用されるデータのみをキャッシュに格納します。

一般に、Redis cache は、SQL Server キャッシュよりも高いスループットを実現し、待機時間が短くなります。 ただし、通常、ベンチマークはキャッシュ戦略のパフォーマンス特性を判断するために必要です。

SQL Server が分散キャッシュバッキングストアとして使用されている場合、キャッシュに同じデータベースを使用すると、アプリの通常のデータストレージと取得が両方のパフォーマンスに悪影響を与える可能性があります。 分散キャッシュバッキングストアには専用の SQL Server インスタンスを使用することをお勧めします。

## <a name="additional-resources"></a>その他のリソース

* [Azure での Redis Cache](/azure/azure-cache-for-redis/)
* [Azure での SQL Database](/azure/sql-database/)
* [Web ファーム内の ncache 用の IDistributedCache Provider](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub の ncache](https://github.com/Alachisoft/NCache)) の ASP.NET Core
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
