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
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core のパフォーマンスに関するベスト プラクティス

によって[Mike Rousos](https://github.com/mjrousos)

この記事では、ASP.NET Core のパフォーマンスのベストプラクティスに関するガイドラインを示します。

## <a name="cache-aggressively"></a>積極的にキャッシュします。

キャッシュは、このドキュメントの複数の部分で説明します。 詳細については、「 <xref:performance/caching/response> 」を参照してください。

## <a name="understand-hot-code-paths"></a>ホットコードパスについて

このドキュメントでは、*ホットコードパス*は、頻繁に呼び出される、実行時間の大半が発生するコードパスとして定義されています。 ホットコードパスは、通常、アプリのスケールアウトとパフォーマンスを制限し、このドキュメントのいくつかの部分で説明されています。

## <a name="avoid-blocking-calls"></a>呼び出しがブロックされないように

ASP.NET Core アプリを設計すると、同時に多数の要求を処理する必要があります。 非同期 Api を使用すると、呼び出しをブロックしていない待機して何千もの同時要求を処理するスレッドの小さなプール。 実行時間の長い同期タスクが完了するを待機しているのではなく、スレッドは、別の要求を処理できます。

ASP.NET Core アプリで一般的なパフォーマンスの問題は、非同期可能性がある呼び出しをブロックしています。 多くの同期ブロッキング呼び出しは、[スレッドプールの枯渇](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)と応答時間の低下につながります。

**しない**:

* 呼び出すことによって、非同期実行をブロック[Task.Wait](/dotnet/api/system.threading.tasks.task.wait)または[Task.Result](/dotnet/api/system.threading.tasks.task-1.result)します。
* 一般的なコード パスでロックを取得します。 ASP.NET Core アプリでは、最も効率的なコードを並列で実行するように構築する場合です。

**行う**

* ように[ホット コード パス](#understand-hot-code-paths)非同期です。
* データ アクセスと実行時間の長い操作の Api を非同期的に呼び出します。
* コント ローラー/Razor ページのアクションを非同期にします。 非同期[/await](/dotnet/csharp/programming-guide/concepts/async/)パターンを活用するために、呼び出し履歴全体が非同期になります。

[Perfview](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、[スレッドプール](/windows/desktop/procthread/thread-pools)に頻繁に追加されるスレッドを見つけることができます。 @No__t-0 イベントは、スレッドプールに追加されたスレッドを示します。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>ラージ オブジェクトの割り当てを最小限に抑える

[.Net Core ガベージコレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリで自動的にメモリの割り当てと解放を管理します。 一般に、自動ガベージ コレクションは、開発者がメモリを解放する方法やタイミングについて心配する必要があることを意味します。 ただし、未参照のオブジェクトのクリーンアップ時間がかかる CPU のため開発者は内のオブジェクトの割り当てを最小限に抑えて[ホット コード パス](#understand-hot-code-paths)します。 ガベージ コレクションは、ラージ オブジェクト (> 85 K バイト) では特に高価です。 大きなオブジェクトが格納されている、[大きなオブジェクト ヒープ](/dotnet/standard/garbage-collection/large-object-heap)完全 (第 2 世代) を必要とガベージ コレクションをクリーンアップします。 ジェネレーション0およびジェネレーション1のコレクションとは異なり、ジェネレーション2のコレクションでは、アプリの実行を一時的に中断する必要があります。 頻繁に割り当てと大きなオブジェクトの割り当てを解除することによってパフォーマンスの一貫性のない可能性があります。

推奨事項:

* **行う** よく使われる大規模なオブジェクトのキャッシュを検討してください。 ラージ オブジェクトをキャッシュには、高価な割り当てができないようにします。
* **行う** を使用してバッファーをプールする[ `ArrayPool<T>` ](/dotnet/api/system.buffers.arraypool-1)大きな配列を格納します。
* **しない**に割り当てる大きなオブジェクトの有効期間が短い、[ホット コード パス](#understand-hot-code-paths)します。

前述のようなメモリの問題は、 [Perfview](https://github.com/Microsoft/perfview)でガベージコレクション (GC) の統計を確認して調べることによって診断できます。

* ガベージ コレクションの一時停止時間。
* プロセッサ時間の割合は、ガベージ コレクションに費やされました。
* ガベージ コレクションの数とは、世代 0、1、および 2 です。

詳細については、次を参照してください。[ガベージ コレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)します。

## <a name="optimize-data-access"></a>データ アクセスを最適化します。

多くの場合、データストアやその他のリモートサービスとのやり取りは、ASP.NET Core アプリの最も低速な部分です。 データの読み書きを効率的には、良好なパフォーマンスにとって重要です。

推奨事項:

* **行う** すべてのデータ アクセス Api を非同期的に呼び出します。
* **しない**は必要以上のデータを取得します。 現在の HTTP 要求に必要なデータだけを返すクエリを記述します。
* **行う**若干古いデータが許容される場合は、データベースやリモート サービスから取得されたデータをアクセス頻繁にキャッシュを検討してください。 シナリオに応じて、 [Memorycache](xref:performance/caching/memory)または[microsoft.web.distributedcache](xref:performance/caching/distributed)を使用します。 詳細については、「 <xref:performance/caching/response> 」を参照してください。
* **行う**を最小限に抑えるネットワーク ラウンド トリップします。 目的は、複数の呼び出しではなく、1回の呼び出しで必要なデータを取得することです。
* **行う** 使用[追跡なしのクエリ](/ef/core/querying/tracking#no-tracking-queries)読み取り専用の目的でデータにアクセスするときに、Entity Framework Core でします。 EF Core より効率的に追跡なしのクエリの結果を返すことができます。
* **行う**フィルターと集計の LINQ クエリ (で`.Where`、 `.Select`、または`.Sum`ステートメントなどの)、フィルター処理がデータベースで実行できるようにします。
* **行う** EF Core に、クライアントは、非効率的なクエリの実行につながる可能性のいくつかのクエリ演算子が解決されることを検討してください。 詳細については、「[クライアント評価のパフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。
* **しない**プロジェクション クエリを使用して、"n+1"を実行するがこのコレクションは、上の SQL クエリ。 詳細については、次を参照してください。[相関サブクエリの最適化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)します。

参照してください[EF 高性能](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)のための手法を高スケールのアプリでのパフォーマンスを向上させる可能性があります。

* [DbContext プール](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [明示的にコンパイルされたクエリ](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

コードベースをコミットする前に、上記の高パフォーマンスアプローチの影響を測定することをお勧めします。 コンパイル済みクエリの複雑さを増加は、パフォーマンスの向上を見合わない場合があります。

[Application Insights](/azure/application-insights/app-insights-overview)またはプロファイリングツールでデータにアクセスするために費やされた時間を確認することで、クエリの問題を検出できます。 ほとんどのデータベースも利用できる統計をに関する頻繁に実行されるクエリ。

## <a name="pool-http-connections-with-httpclientfactory"></a>HttpClientFactory との接続をプール HTTP

[Httpclient](/dotnet/api/system.net.http.httpclient)は `IDisposable` インターフェイスを実装していますが、再利用できるように設計されています。 閉じられた`HttpClient`ソケットのままで開いているインスタンス、`TIME_WAIT`短時間の状態。 @No__t 0 オブジェクトを作成および破棄するコードパスが頻繁に使用される場合、アプリは使用可能なソケットを消費する可能性があります。 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)この問題をソリューションとしての ASP.NET Core 2.1 で導入されました。 パフォーマンスと信頼性を最適化するためにプールの HTTP 接続を処理します。

推奨事項:

* **行う**の作成し、破棄の`HttpClient`直接インスタンス化します。
* **行う** 使用[HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を取得する`HttpClient`インスタンス。 詳細については、次を参照してください。[回復力のある HTTP 要求を実装するために使用 HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)します。

## <a name="keep-common-code-paths-fast"></a>高速の一般的なコード パスを維持します。

すべてのコードが高速になるようにするには、コードパスと呼ばれることがよくあります。

* アプリの要求処理パイプラインでミドルウェア コンポーネント、特にミドルウェアはパイプラインの早い段階で実行します。 これらのコンポーネントは、パフォーマンスに大きな影響を与えます。
* 要求ごとに、または要求ごとに複数回実行されるコード。 たとえば、カスタム ログ、承認ハンドラー、または一時的なサービスの初期化にします。

推奨事項:

* **しない**実行時間の長いタスクでカスタムのミドルウェア コンポーネントを使用します。
* **行う**パフォーマンス プロファイリング ツールなどを使用して、 [Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)または[PerfView](https://github.com/Microsoft/perfview)) を識別[ホット コード パス](#understand-hot-code-paths)します。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>HTTP 要求の外部で長時間タスクを完了します。

コント ローラーまたはページ モデルに必要なサービスを呼び出すと、HTTP 応答を返すことによって、ASP.NET Core アプリにほとんどの要求を処理できます。 要求実行時間の長いタスクに関連するいくつかの場合は、全体の要求-応答プロセスを非同期にすることをお勧めします。

推奨事項:

* **しない**実行時間の長いタスクが通常の HTTP 要求の処理の一部として完了するまで待ちます。
* **行う** での実行時間の長い要求の処理を検討してください[バック グラウンド サービス](xref:fundamentals/host/hosted-services)またはアウト プロセスで、 [Azure 関数](/azure/azure-functions/)します。 作業のアウト プロセスの完了は、CPU を消費するタスクに特に有益です。
* **行う**などのリアルタイム通信のオプションを使用して[SignalR](xref:signalr/introduction)クライアントと非同期的に通信します。

## <a name="minify-client-assets"></a>クライアントの資産を縮小します。

複雑なフロント エンドを使用した ASP.NET Core アプリは、多くの JavaScript、CSS、またはイメージ ファイルを頻繁に機能します。 により、初期読み込み要求のパフォーマンスを向上できます。

* 1 つに複数のファイルを結合するバンドル化します。
* 縮小。空白とコメントを削除することによってファイルのサイズを縮小します。

推奨事項:

* **行う** で ASP.NET Core の使用[組み込みサポート](xref:client-side/bundling-and-minification)バンドルと縮小クライアント資産。
* **行う**などその他のサード パーティ製ツールを検討してください[Webpack](https://webpack.js.org/)、複雑なクライアント資産管理のためです。

## <a name="compress-responses"></a>応答を圧縮する

 通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。 ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。 詳細については、「[応答の圧縮](xref:performance/response-compression)」を参照してください。

## <a name="use-the-latest-aspnet-core-release"></a>ASP.NET Core の最新のリリースを使用します。

ASP.NET Core の新しいリリースにはそれぞれ、パフォーマンスが向上しています。 .NET Core と ASP.NET Core での最適化により、新しいバージョンの方が以前のバージョンより高い値になります。 .NET Core 2.1 にサポートが追加されてコンパイルされる正規表現との恩恵をたとえば、 [ `Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx)します。 Http/2 のサポートを ASP.NET Core 2.2 を追加します。 [ASP.NET Core 3.0](xref:aspnetcore-3.0)では、メモリ使用量を減らし、スループットを向上させる多くの機能強化が加えられています。 パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。

## <a name="minimize-exceptions"></a>例外を最小限に抑える

例外は、まれである必要があります。 スローして、例外のキャッチは、他のコード フロー パターンを基準と遅いです。 このため、通常のプログラムフローを制御するために例外を使用することはできません。

推奨事項:

* 特に[ホットコードパス](#understand-hot-code-paths)では、通常のプログラムフローの手段として例外をスローまたはキャッチし**ない**ようにします。
* **行う** ロジックを検出して例外の原因となる条件を処理するアプリケーションに含めることができます。
* **行う** スローまたは異常なまたは予期しない条件の例外をキャッチします。

Application Insights などのアプリ診断ツールを使用すると、アプリケーションでのパフォーマンスに影響する可能性のある一般的な例外を識別できます。

## <a name="performance-and-reliability"></a>パフォーマンスと信頼性

次のセクションでは、パフォーマンスのヒントと既知の信頼性の問題と解決策について説明します。

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/Httpresponse.cache 本体で同期読み取りまたは書き込みを回避する

ASP.NET Core の IO はすべて非同期です。 サーバーは、同期と非同期の両方のオーバーロードを持つ @no__t 0 インターフェイスを実装します。 スレッドプールのスレッドがブロックされないようにするために、非同期のものを使用することをお勧めします。 ブロックしているスレッドは、スレッドプールの枯渇につながる可能性があります。

**この操作は避けてください。** <xref:System.IO.StreamReader.ReadToEnd*> を使用する例を次に示します。 このメソッドは、現在のスレッドが結果を待機するのをブロックします。 これは、async @ no__t 経由の [sync の例です。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

前のコードでは、`Get` は、HTTP 要求の本体全体をメモリに同期的に読み取ります。 クライアントが低速でアップロードしている場合、アプリは非同期で同期を実行しています。 Kestrel では同期読み取りがサポート**されてい**ないため、アプリは非同期経由で同期します。

**次の手順を実行します。** 次の例では <xref:System.IO.StreamReader.ReadToEndAsync*> を使用し、読み取り中にスレッドをブロックしません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

上記のコードは、HTTP 要求本文全体を非同期的にメモリに読み込みます。

> [!WARNING]
> 要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態になる可能性があります。 OOM を使用すると、サービス拒否が発生する可能性があります。  詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。

**次の手順を実行します。** 次の例は、バッファーされていない要求本文を使用する完全な非同期です。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

上のコードは、要求本文を非同期的にC#オブジェクトにシリアル化解除します。

## <a name="prefer-readformasync-over-requestform"></a>要求で ReadFormAsync を優先します。フォーム

`HttpContext.Request.ReadFormAsync` の代わりに `HttpContext.Request.Form`を使用します。
`HttpContext.Request.Form` は、次の条件で安全に読み取ることができます。

* フォームは `ReadFormAsync` への呼び出しによって読み取られました。
* キャッシュされたフォーム値は `HttpContext.Request.Form` を使用して読み取られています

**この操作は避けてください。** 次の例では `HttpContext.Request.Form` を使用します。  `HttpContext.Request.Form` は、async @ no__t で [sync を使用します。これにより、スレッドプールが枯渇する可能性があります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**次の手順を実行します。** 次の例では、`HttpContext.Request.ReadFormAsync` を使用してフォーム本文を非同期に読み取ります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>大きな要求本文または応答本文をメモリに読み込むことは避けてください

.NET では、85 KB を超えるすべてのオブジェクト割り当ては、大きなオブジェクトヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) で終了します。 ラージオブジェクトは、次の2つの方法でコストが高くなります。

* 新しく割り当てられたラージオブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。 CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。
* LOH は、ヒープの残りの部分で収集されます。 LOH には、フル[ガベージコレクション](/dotnet/standard/garbage-collection/fundamentals)または[Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。

この[ブログ投稿](https://adamsitnik.com/Array-Pool/#the-problem)では、問題について簡潔に説明しています。

> ラージオブジェクトが割り当てられると、ジェネレーション2のオブジェクトとしてマークされます。 小さいオブジェクトの場合は Gen 0 として生成されません。 結果として、LOH でメモリが不足していると、LOH だけでなく、マネージヒープ全体が GC によってクリーンアップされます。 そのため、LOH を含む Gen 0、Gen 1、Gen 2 をクリーンアップします。 これはフルガベージコレクションと呼ばれ、最も時間のかかるガベージコレクションです。 多くのアプリケーションでは、許容される可能性があります。 しかし、高パフォーマンスの web サーバーでは、平均的な web 要求を処理するために大量のメモリバッファーが必要になることはありません (ソケットからの読み取り、圧縮解除、JSON & のデコードなど)。

ネイティブは、大規模な要求または応答の本文を1つの `byte[]` または `string` に格納します。

* LOH の領域がすぐに不足する可能性があります。
* 完全な Gc が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。

## <a name="working-with-a-synchronous-data-processing-api"></a>同期データ処理 API を使用した作業

同期読み取りと書き込みのみをサポートするシリアライザー/デシリアライザーを使用する場合 (たとえば、 [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* シリアライザー/デシリアライザーに渡す前に、データをメモリに非同期的にバッファーします。

> [!WARNING]
> 要求が大きい場合、メモリ不足 (OOM) 状態になる可能性があります。 OOM を使用すると、サービス拒否が発生する可能性があります。  詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。

ASP.NET Core 3.0 では、JSON シリアル化に対して既定で <xref:System.Text.Json> が使用されます。 <xref:System.Text.Json>:

* 非同期で JSON の読み取りと書き込みを行います。
* UTF-8 テキスト用に最適化されています。
* 通常、`Newtonsoft.Json` よりパフォーマンスが向上します。

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>フィールドに IHttpContextAccessor を格納しない

[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)は、要求スレッドからアクセスされるときに、アクティブな要求の `HttpContext` を返します。 @No__t-0 は、フィールドまたは変数には格納でき**ません**。

**この操作は避けてください。** 次の例では、フィールドに `HttpContext` を格納し、後でそれを使用しようとします。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

上記のコードでは、コンストラクターで null または正しくない @no__t 0 がキャプチャされることがよくあります。

**次の手順を実行します。** 次のような例です。

* フィールドに @no__t 0 を格納します。
* は、正しい時刻に `HttpContext` フィールドを使用し、`null` を確認します。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>複数のスレッドから HttpContext にアクセスしない

`HttpContext` はスレッドセーフでは*ありません*。 複数のスレッドから `HttpContext` に同時にアクセスすると、ハング、クラッシュ、データ破損などの未定義の動作が発生する可能性があります。

**この操作は避けてください。** 次の例では、3つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。 要求パスは、複数のスレッドから同時にアクセスされる可能性があります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**次の手順を実行します。** 次の例では、3つの並列要求を行う前に、受信要求からすべてのデータをコピーします。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>要求の完了後に HttpContext を使用しない

`HttpContext` は、ASP.NET Core パイプラインにアクティブな HTTP 要求がある限り有効です。 ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。 このチェーンから返された @no__t 0 が完了すると、`HttpContext` がリサイクルされます。

**この操作は避けてください。** 次の例では、最初の @no__t に到達したときに HTTP 要求が完了するように、`async void` を使用しています。

* これは、ASP.NET Core アプリでは**常**に不適切な方法です。
* HTTP 要求が完了した後に `HttpResponse` にアクセスします。
* プロセスをクラッシュさせる。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**次の手順を実行します。** 次の例では、アクションが完了するまで HTTP 要求が完了しないように、フレームワークに `Task` が返されます。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>バックグラウンドスレッドで HttpContext をキャプチャしない

**この操作は避けてください。** 次の例は、`Controller` プロパティから `HttpContext` をキャプチャしていることを示しています。 作業項目は次のようになる可能性があるため、この方法は不適切です。

* 要求スコープの外部で実行します。
* 間違った @no__t を読み取ろうとしました-0。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**次の手順を実行します。** 次のような例です。

* 要求中にバックグラウンドタスクで必要なデータをコピーします。
* コントローラーから何も参照していません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>バックグラウンドスレッドでコントローラーに挿入されたサービスをキャプチャしない

**この操作は避けてください。** 次の例は、`Controller` アクションパラメーターから `DbContext` をキャプチャすることを示しています。 これは不適切な方法です。  この作業項目は、要求スコープの外部で実行される可能性があります。 @No__t-0 は要求に対してスコープが設定され、その結果、`ObjectDisposedException` になります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**次の手順を実行します。** 次のような例です。

* バックグラウンド作業項目のスコープを作成するために @no__t 0 を挿入します。 `IServiceScopeFactory` はシングルトンです。
* バックグラウンドスレッドで新しい依存関係挿入スコープを作成します。
* コントローラーから何も参照していません。
* は、受信要求から `ContosoDbContext` をキャプチャしません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

次の強調表示されたコード。

* バックグラウンド操作の有効期間のスコープを作成し、そこからサービスを解決します。
* は、正しいスコープの `ContosoDbContext` を使用します。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>応答本文が開始された後に状態コードまたはヘッダーを変更しない

ASP.NET Core は、HTTP 応答の本文をバッファーしません。 最初に応答が書き込まれたとき:

* ヘッダーは、本文のそのチャンクと共にクライアントに送信されます。
* 応答ヘッダーを変更することはできなくなりました。

**この操作は避けてください。** 次のコードでは、応答が既に開始された後に応答ヘッダーを追加しようとしています。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

前のコードでは、`next()` が応答に書き込まれた場合、`context.Response.Headers["test"] = "test value";` は例外をスローします。

**次の手順を実行します。** 次の例では、ヘッダーを変更する前に、HTTP 応答が開始されたかどうかを確認します。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**次の手順を実行します。** 次の例では、`HttpResponse.OnStarting` を使用して、応答ヘッダーをクライアントにフラッシュする前にヘッダーを設定します。

応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。 応答が開始されていないかどうかを確認しています:

* ヘッダーをジャストインタイムで追加またはオーバーライドする機能を提供します。
* では、パイプライン内の次のミドルウェアに関する知識は必要ありません。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>応答本文への書き込みを既に開始している場合は、next () を呼び出さないでください。

コンポーネントは、応答を処理および操作できる場合にのみ呼び出されることを想定しています。
