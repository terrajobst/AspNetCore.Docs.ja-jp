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
# <a name="cache-in-memory-in-aspnet-core"></a>ASP.NET Core 内のメモリ内のキャッシュ

[Rick Anderson](https://twitter.com/RickAndMSFT)、 [John luo](https://github.com/JunTaoLuo)、および[上田 Smith](https://ardalis.com/)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="caching-basics"></a>キャッシュの基本

キャッシュを使用すると、コンテンツを生成するために必要な作業量を減らすことで、アプリのパフォーマンスとスケーラビリティを大幅に向上させることができます。 キャッシュは、あまり変更されないデータで最適に機能します。 キャッシュを使用すると、元のソースよりもはるかに高速に返されるデータのコピーが作成されます。 アプリは、キャッシュされたデータに依存し**ない**ように作成およびテストする必要があります。

ASP.NET Core は、いくつかの異なるキャッシュをサポートしています。 最も単純なキャッシュは、web サーバーのメモリに格納されているキャッシュを表す[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)に基づいています。 複数のサーバーのサーバーファームで実行されるアプリでは、メモリ内キャッシュを使用するときにセッションが固定されていることを確認する必要があります。 固定セッションでは、クライアントからの後続の要求がすべて同じサーバーに送られるようにします。 たとえば、Azure Web apps では、[アプリケーション要求ルーティング](https://www.iis.net/learn/extensions/planning-for-arr)処理 (ARR) を使用して、後続のすべての要求を同じサーバーにルーティングします。

Web ファームの固定されていないセッションでは、キャッシュ整合性の問題を回避するために[分散キャッシュ](distributed.md)が必要です。 アプリによっては、分散キャッシュがメモリ内キャッシュよりも高いスケールアウトをサポートする場合があります。 分散キャッシュを使用すると、キャッシュメモリが外部プロセスにオフロードされます。

::: moniker range="< aspnetcore-2.0"

キャッシュ`IMemoryCache`の[優先順位](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)がに設定されていない限り、キャッシュは`CacheItemPriority.NeverRemove`メモリ不足のときにキャッシュエントリを削除します。 を設定して`CacheItemPriority` 、キャッシュがメモリ不足の状態にある見つけ項目の優先度を調整できます。

::: moniker-end

メモリ内キャッシュには、任意のオブジェクトを格納できます。分散キャッシュインターフェイスはに`byte[]`制限されています。 メモリ内および分散キャッシュストアは、キーと値のペアとしてキャッシュ項目を格納します。

## <a name="systemruntimecachingmemorycache"></a>System.string. キャッシュ/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>([NuGet パッケージ](https://www.nuget.org/packages/System.Runtime.Caching/)) は、次のものと共に使用できます。

* .NET Standard 2.0 以降。
* .NET Standard 2.0 以降を対象とするすべての[.net 実装](/dotnet/standard/net-standard#net-implementation-support)。 たとえば、2.0 以降の ASP.NET Core ます。
* .NET Framework 4.5 以降。

[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` ASP.NET Core に統合することをお勧めします。そのため、この記事で説明されているように、このキャッシュはお勧めします`System.Runtime.Caching`/`MemoryCache`。 たとえば、 `IMemoryCache`は ASP.NET Core[依存関係の挿入](xref:fundamentals/dependency-injection)とネイティブに連携します。

ASP.NET `System.Runtime.Caching` 4.x から ASP.NET Core にコードを移植するときは、互換性ブリッジとして使用/ `MemoryCache`します。

## <a name="cache-guidelines"></a>キャッシュのガイドライン

* コードには常に、データをフェッチするためのフォールバックオプションがあり、使用可能なキャッシュ値に依存し**ない**ようにする必要があります。
* キャッシュは、不足しているリソース (メモリ) を使用します。 キャッシュ拡張の制限:
  * 外部入力をキャッシュキーとして使用しないでください。
  * キャッシュの拡張を制限するには、有効期限を使用します。
  * [キャッシュサイズを制限するには、SetSize、Size、および SizeLimit を使用](#use-setsize-size-and-sizelimit-to-limit-cache-size)します。 ASP.NET Core ランタイムでは、メモリ負荷に基づいてキャッシュサイズが制限されません。 キャッシュサイズを制限するのは開発者だけです。

## <a name="using-imemorycache"></a>IMemoryCache の使用

> [!WARNING]
> [依存関係の挿入](xref:fundamentals/dependency-injection)から*共有*メモリキャッシュを使用し`Size`、、 `SizeLimit` 、またはを呼び出し`SetSize`てキャッシュサイズを制限すると、アプリが失敗する可能性があります。 キャッシュにサイズ制限が設定されている場合、すべてのエントリは追加時にサイズを指定する必要があります。 これにより、開発者は共有キャッシュを使用する内容を完全に制御できない場合があるため、問題が発生する可能性があります。 たとえば、Entity Framework Core は共有キャッシュを使用し、サイズを指定しません。 アプリがキャッシュサイズの制限を設定し、EF Core を使用する場合、 `InvalidOperationException`アプリはをスローします。
> 、 `SetSize` `SizeLimit` 、またはを使用してキャッシュを制限する場合は、キャッシュ用のキャッシュシングルトンを作成します。 `Size` 詳細と例については、「 [SetSize、サイズ、および SizeLimit を使用してキャッシュサイズを制限する](#use-setsize-size-and-sizelimit-to-limit-cache-size)」を参照してください。

インメモリキャッシュは、[依存関係の挿入](xref:fundamentals/dependency-injection)を使用してアプリから参照される*サービス*です。 で`AddMemoryCache` の`ConfigureServices`呼び出し:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

コンストラクターに`IMemoryCache`インスタンスを要求します。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache`NuGet パッケージの[拡張](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)が必要です。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache`では、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage)で使用できる NuGet パッケージ[microsoft. extension. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)が必要です。

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache`では、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)で入手できる NuGet パッケージ[microsoft. extension. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)が必要です。

::: moniker-end

次のコードでは、 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)を使用して、時間がキャッシュ内にあるかどうかを確認します。 時間がキャッシュされていない場合は、新しいエントリが作成され、が[設定](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)されたキャッシュに追加されます。

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

現在の時刻とキャッシュされた時間が表示されます。

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

キャッシュされた値は、タイムアウト期間内に要求があってもキャッシュに残ります。`DateTime` 次の図は、現在の時刻と、キャッシュから取得された古い時間を示しています。

![2つの異なる時刻が表示されたインデックスビュー](memory/_static/time.png)

次のコードでは、 [Getorcreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)と[Getorcreateasync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)を使用してデータをキャッシュしています。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

次のコードは、 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)を呼び出して、キャッシュされた時間を取得します。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>、および[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)は、の<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>機能を拡張する[cacheextensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)クラスの拡張メソッドです。 他のキャッシュメソッドの詳細については、「 [IMemoryCache メソッド](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)と[cacheextensions メソッド](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)」を参照してください。

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

次の例を次に示します。

* 絶対有効期限を設定します。 これは、エントリをキャッシュできる最大時間であり、スライド式有効期限が継続的に更新されるときに、項目が古くなるのを防ぐことができます。
* スライド式有効期限を設定します。 このキャッシュされた項目にアクセスする要求は、スライド式有効期限をリセットします。
* キャッシュの優先順位を`CacheItemPriority.NeverRemove`に設定します。
* エントリがキャッシュから削除された後に呼び出される[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)を設定します。 コールバックは、キャッシュから項目を削除するコードとは異なるスレッドで実行されます。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>SetSize、サイズ、および SizeLimit を使用してキャッシュサイズを制限する

インスタンス`MemoryCache`では、必要に応じてサイズ制限を指定して適用できます。 キャッシュにはエントリのサイズを測定する機構がないため、メモリサイズの制限には定義された測定単位がありません。 キャッシュメモリサイズの制限が設定されている場合、すべてのエントリでサイズを指定する必要があります。 ASP.NET Core ランタイムでは、メモリ負荷に基づいてキャッシュサイズが制限されません。 キャッシュサイズを制限するのは開発者だけです。 指定されたサイズは、開発者が選択した単位で示されます。

例:

* Web アプリが主に文字列をキャッシュしている場合は、各キャッシュエントリのサイズを文字列の長さにすることができます。
* アプリでは、すべてのエントリのサイズを1と指定することができ、サイズ制限はエントリの数です。

次のコードでは、[依存関係の挿入](xref:fundamentals/dependency-injection)によってアクセス可能な、単位固定サイズの[memorycache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1)が作成されます。

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[Sizelimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)に単位がありません。 キャッシュメモリサイズが設定されている場合、キャッシュされたエントリは、最適と思われる任意の単位でサイズを指定する必要があります。 キャッシュインスタンスのすべてのユーザーは、同じ単体システムを使用する必要があります。 キャッシュされたエントリサイズの合計がで`SizeLimit`指定された値を超えた場合、エントリはキャッシュされません。 キャッシュサイズの制限が設定されていない場合、エントリに設定されているキャッシュサイズは無視されます。

次のコードは`MyMemoryCache` 、[依存関係挿入](xref:fundamentals/dependency-injection)コンテナーに登録します。

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache`は、このサイズ制限付きキャッシュを認識し、キャッシュエントリサイズを適切に設定する方法を理解しているコンポーネントの独立したメモリキャッシュとして作成されます。

次のコードで`MyMemoryCache`は、を使用します。

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

キャッシュエントリのサイズは、 [size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)または[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)拡張メソッドを使用して設定できます。

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>キャッシュの依存関係

次のサンプルは、依存エントリの有効期限が切れた場合に、キャッシュエントリを期限切れにする方法を示しています。 `CancellationChangeToken`がキャッシュされた項目に追加されます。 でが呼び出される`Cancel`と、両方のキャッシュエントリが削除されます。`CancellationTokenSource`

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

を使用すると、複数のキャッシュエントリを1つのグループとして削除できます。`CancellationTokenSource` 上記のコードの`using` パターンでは、ブロック内に作成されたキャッシュエントリによって、トリガーと有効期限の設定が継承されます。`using`

## <a name="additional-notes"></a>補足メモ

* コールバックを使用してキャッシュ項目を再作成する場合:

  * コールバックが完了していないため、複数の要求でキャッシュされたキー値を空にすることができます。
  * これにより、複数のスレッドがキャッシュされた項目を再作成する可能性があります。

* あるキャッシュエントリを使用して別のキャッシュエントリを作成すると、子は親エントリの有効期限トークンと時間ベースの有効期限の設定をコピーします。 親エントリを手動で削除または更新しても、子の有効期限は切れません。

* キャッシュからキャッシュエントリが削除された後に起動されるコールバックを設定するには、 [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)を使用します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
