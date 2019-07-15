---
title: ASP.NET Core でメモリ内キャッシュします。
author: rick-anderson
description: ASP.NET Core でメモリにデータをキャッシュする方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: performance/caching/memory
ms.openlocfilehash: ffd21f014c02f46d19364a7a54686b8d5c95dc1a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815026"
---
# <a name="cache-in-memory-in-aspnet-core"></a>ASP.NET Core でメモリ内キャッシュします。

によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)、および[Steve Smith](https://ardalis.com/)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="caching-basics"></a>キャッシュの基礎

キャッシュと、パフォーマンスとアプリケーションのスケーラビリティが大幅に、コンテンツの生成に必要な作業を減らすことで改善できます。 変更頻度の低いデータに最適なキャッシュのしくみです。 キャッシュで返すことができる多くのデータのコピー元のソースからよりも高速です。 アプリを記述およびテストする必要があります**決して**キャッシュされたデータに依存します。

ASP.NET Core には、いくつかの異なるキャッシュがサポートしています。 最も単純なキャッシュがに基づいて、 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)、web サーバーのメモリに格納されているキャッシュを表します。 複数のサーバーのサーバー ファームで実行されるアプリでは、セッションをメモリ内キャッシュを使用する場合は固定ことを確認してください。 スティッキー セッションでは、すべてのクライアントからの後続の要求が同じサーバーに移動することを確認します。 たとえば、Azure Web アプリの使用[アプリケーション要求ルーティング処理](https://www.iis.net/learn/extensions/planning-for-arr)処理 (ARR) を同じサーバーにすべての後続の要求をルーティングします。

Web ファーム内の非スティッキー セッションが必要です、[分散キャッシュ](distributed.md)キャッシュ整合性の問題を回避します。 一部のアプリでは、分散キャッシュはメモリ内キャッシュよりも高いスケール アウトをサポートできます。 分散キャッシュを使用して外部プロセスにキャッシュ メモリをオフロードします。

::: moniker range="< aspnetcore-2.0"

`IMemoryCache`しない限り、キャッシュがメモリ負荷の下でのキャッシュ エントリを削除は、[優先順位のキャッシュ](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)に設定されている`CacheItemPriority.NeverRemove`します。 設定することができます、`CacheItemPriority`キャッシュがメモリの負荷の下の項目を削除する優先順位を調整します。

::: moniker-end

メモリ内キャッシュは、すべてのオブジェクトを格納できます。分散キャッシュのインターフェイスに制限されます`byte[]`します。 キー/値ペアとしてし、インメモリ分散キャッシュ ストア キャッシュ項目。

## <a name="systemruntimecachingmemorycache"></a>System.Runtime.Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet パッケージ](https://www.nuget.org/packages/System.Runtime.Caching/)) で使用できます。

* .NET standard 2.0 以降。
* すべて[.NET 実装](/dotnet/standard/net-standard#net-implementation-support).NET Standard 2.0 以降をターゲットとします。 たとえば、ASP.NET Core 2.0 以降です。
* .NET framework 4.5 またはそれ以降。

[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (この記事で説明) よりもお勧め`System.Runtime.Caching` / `MemoryCache`のため、これは、ASP.NET Core に統合して強化します。 たとえば、 `IMemoryCache` ASP.NET Core では、ネイティブ動作[依存関係の注入](xref:fundamentals/dependency-injection)します。

使用`System.Runtime.Caching` / `MemoryCache` ASP.NET からのコードを移植するときに互換性の仲介役としての ASP.NET core 4.x です。

## <a name="cache-guidelines"></a>キャッシュのガイドライン

* データをフェッチするフォールバック オプションがコードと**いない**利用されているキャッシュされた値に依存します。
* キャッシュは、メモリ、不足しているリソースを使用します。 キャッシュ拡張を制限するには。
  * **いない**キャッシュ キーと外部入力を使用します。
  * 有効期限の設定を使用すると、キャッシュ増加を制限できます。
  * [SetSize、サイズ、および SizeLimit を使用して、キャッシュ サイズを制限する](#use-setsize-size-and-sizelimit-to-limit-cache-size)します。 ASP.NET Core ランタイムでは、メモリの負荷に基づいてキャッシュのサイズは制限されません。 キャッシュ サイズを制限する開発者です。

## <a name="using-imemorycache"></a>IMemoryCache を使用します。

メモリ内キャッシュは、*サービス*を使用して、アプリから参照される[依存関係の注入](../../fundamentals/dependency-injection.md)します。 呼び出す`AddMemoryCache`で`ConfigureServices`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

要求、`IMemoryCache`コンス トラクターでインスタンス。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache` NuGet パッケージが必要です[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)します。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache` NuGet パッケージが必要です[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)、用意されている、 [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)します。

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache` NuGet パッケージが必要です[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)、用意されている、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)します。

::: moniker-end

次のコードでは[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)キャッシュ内、時間をチェックします。 新しいエントリが作成され、キャッシュに追加、時間がキャッシュされていない場合[設定](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)します。

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

現在の時刻とキャッシュされた時刻が表示されます。

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

キャッシュされた`DateTime`タイムアウト期間内に要求があるときに値をキャッシュに保持します。 次の図は、現在の時刻と、キャッシュから取得した古い時刻を示します。

![インデックス ビューに表示される 2 つの時刻](memory/_static/time.png)

次のコードでは[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)と[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)データをキャッシュします。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

次のコード呼び出し[取得](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)キャッシュされる時間を取得します。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> 、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>、および[取得](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)の拡張メソッドの一部である、 [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)の機能を拡張するクラスを<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>します。 参照してください[IMemoryCache メソッド](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)と[CacheExtensions メソッド](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)の他のキャッシュ方法の説明。

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

次のサンプル:

* 絶対有効期限を設定します。 これは、エントリをキャッシュできる最大時間であり、項目が古くなりすぎないスライド式有効期限が継続的に更新されることを防ぎます。
* スライド式有効期限を設定します。 このキャッシュされた項目にアクセスする要求は、スライド式有効期限の時間にリセットされます。
* キャッシュ優先度を設定`CacheItemPriority.NeverRemove`します。
* セットを[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)するエントリがキャッシュから削除された後に呼び出されます。 コールバックは、キャッシュから項目を削除するコードから別のスレッドで実行されます。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>SetSize、サイズ、および SizeLimit を使用して、キャッシュ サイズを制限するには

A`MemoryCache`インスタンスが必要に応じて指定し、サイズ制限が適用されます。 メモリ サイズの制限は、キャッシュのエントリのサイズを測定するためのメカニズムがあるないために、メジャーの定義済みの単位がありません。 キャッシュ メモリ サイズの制限が設定されている場合、すべてのエントリはサイズを指定する必要があります。 ASP.NET Core ランタイムでは、メモリの負荷に基づいてキャッシュのサイズは制限されません。 キャッシュ サイズを制限する開発者です。 指定されたサイズは、単位、開発者が選択ができます。

例:

* Web アプリでは、文字列をキャッシュが主に、各キャッシュ エントリのサイズが文字列の長さになります。
* アプリは、1 とすべてのエントリのサイズを指定できます、サイズ制限はエントリの数。

次のコード作成、単位のない固定サイズ[MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1)からアクセスできる[依存関係の注入](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)ユニットではありません。 キャッシュされたエントリは、キャッシュ メモリのサイズが設定されている場合に最も適切なと見なした単位でサイズを指定する必要があります。 キャッシュ インスタンスのすべてのユーザーには、同じ単位システムを使用する必要があります。 キャッシュ エントリ サイズの合計がで指定された値を超えた場合、エントリはキャッシュされません`SizeLimit`します。 キャッシュ サイズの制限が設定されていない場合のエントリを設定するキャッシュ サイズは無視されます。

次のコードのレジスタ`MyMemoryCache`で、[依存関係の注入](xref:fundamentals/dependency-injection)コンテナー。

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` 独立したキャッシュはこのサイズの上限のキャッシュを意識して、キャッシュ エントリのサイズを適切に設定する方法を理解しているコンポーネントとして作成されます。

次のコードでは`MyMemoryCache`:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

キャッシュ エントリのサイズを設定できます[サイズ](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)または[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)拡張メソッド。

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>キャッシュの依存関係

次の例では、依存するエントリの有効期限が切れる場合、キャッシュ エントリを期限切れする方法を示します。 A`CancellationChangeToken`キャッシュされた項目に追加されます。 ときに`Cancel`で呼び出される、 `CancellationTokenSource`、両方のキャッシュ エントリが削除されます。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

使用して、`CancellationTokenSource`グループとして削除する複数のキャッシュ エントリを許可します。 `using`内で上記のコード パターンは、キャッシュ エントリの作成、`using`ブロックは、トリガーと有効期限の設定を継承します。

## <a name="additional-notes"></a>補足メモ

* コールバックを使用して、キャッシュ項目を再作成します。

  * コールバックが完了していないために、複数の要求には、キャッシュされたキーの値が空ことができます検索します。
  * これは、結果、複数のスレッドが、キャッシュされた項目を再作成します。

* 1 つのキャッシュ エントリを使用して、別に作成したときに、子は親エントリの有効期限のトークンと有効期限の時間ベースの設定をコピーします。 子は、手動で削除して期限切れまたは親エントリの更新はありません。

* 使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)キャッシュ エントリがキャッシュから削除された後に起動されるコールバックを設定します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
