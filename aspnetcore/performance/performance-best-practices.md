---
title: ASP.NET Core パフォーマンスのベストプラクティス
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスを向上させ、一般的なパフォーマンスの問題を回避するためのヒントです。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/12/2019
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 279bf352580e5e45fc005e800ee536871210409b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963242"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NET Core パフォーマンスのベストプラクティス

[Mike/sos](https://github.com/mjrousos)

この記事では、ASP.NET Core のパフォーマンスのベストプラクティスに関するガイドラインを示します。

## <a name="cache-aggressively"></a>積極的にキャッシュ

キャッシュについては、このドキュメントのいくつかの部分で説明します。 詳細については、「<xref:performance/caching/response>」を参照してください。

## <a name="understand-hot-code-paths"></a>ホットコードパスについて

このドキュメントでは、*ホットコードパス*は、頻繁に呼び出される、実行時間の大半が発生するコードパスとして定義されています。 ホットコードパスは、通常、アプリのスケールアウトとパフォーマンスを制限し、このドキュメントのいくつかの部分で説明されています。

## <a name="avoid-blocking-calls"></a>ブロック呼び出しを回避する

ASP.NET Core アプリは、多数の要求を同時に処理するように設計する必要があります。 非同期 Api を使用すると、スレッドの小さなプールで、ブロックしている呼び出しを待機せずに、数千の同時要求を処理することができます。 実行時間の長い同期タスクが完了するのを待機するのではなく、スレッドが別の要求で作業できます。

ASP.NET Core アプリのパフォーマンスに関する一般的な問題は、非同期の呼び出しをブロックしていることです。 多くの同期ブロッキング呼び出しは、[スレッドプールの枯渇](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)と応答時間の低下につながります。

**実行**しない:

* [Task. Wait](/dotnet/api/system.threading.tasks.task.wait)または[task. Result](/dotnet/api/system.threading.tasks.task-1.result)を呼び出して、非同期実行をブロックします。
* 共通コードパスのロックを取得します。 ASP.NET Core アプリは、コードを並列実行するように設計されている場合に最もパフォーマンスが高くなります。

**操作**:

* [ホットコードパス](#understand-hot-code-paths)を非同期にします。
* データアクセスと長時間実行される操作 Api を非同期に呼び出します。
* コントローラー/Razor ページのアクションを非同期にします。 非同期[/await](/dotnet/csharp/programming-guide/concepts/async/)パターンを活用するために、呼び出し履歴全体が非同期になります。

[Perfview](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、[スレッドプール](/windows/desktop/procthread/thread-pools)に頻繁に追加されるスレッドを見つけることができます。 `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` イベントは、スレッドプールに追加されたスレッドを示します。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>大きなオブジェクトの割り当てを最小限にする

[.Net Core ガベージコレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリで自動的にメモリの割り当てと解放を管理します。 自動ガベージコレクションは、一般に、メモリが解放される方法とタイミングについて開発者が心配する必要がないことを意味します。 ただし、参照されていないオブジェクトをクリーンアップすると CPU 時間がかかるため、開発者は[ホットコードパス](#understand-hot-code-paths)内のオブジェクトの割り当てを最小限に抑える必要があります。 ガベージコレクションは、大きなオブジェクト (> 85 K バイト) で特にコストが高くなります。 ラージオブジェクトは[大きなオブジェクトヒープ](/dotnet/standard/garbage-collection/large-object-heap)に格納され、クリーンアップするには、完全な (ジェネレーション 2) ガベージコレクションが必要です。 ジェネレーション0およびジェネレーション1のコレクションとは異なり、ジェネレーション2のコレクションでは、アプリの実行を一時的に中断する必要があります。 大きなオブジェクトを頻繁に割り当てたり割り当て解除したりすると、パフォーマンスが低下する可能性があります。

推奨事項

* 頻繁に使用されるラージオブジェクトをキャッシュすること**を検討してください。** 大きなオブジェクトをキャッシュすると、コストのかかる割り当てを防ぐことができます。
* 大きな配列を格納するには、 [`ArrayPool<T>`](/dotnet/api/system.buffers.arraypool-1)を使用してバッファー**をプールし**ます。
* [ホットコードパス](#understand-hot-code-paths)には、有効期間が短い大きなオブジェクトを多数割り当て**ない**でください。

前述のようなメモリの問題は、 [Perfview](https://github.com/Microsoft/perfview)でガベージコレクション (GC) の統計を確認して調べることによって診断できます。

* ガベージコレクションの一時停止時刻。
* ガベージコレクションで消費されるプロセッサ時間の割合。
* ジェネレーション0、1、および2のガベージコレクションの数。

詳細については、「[ガベージコレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)」を参照してください。

## <a name="optimize-data-access"></a>データアクセスの最適化

多くの場合、データストアやその他のリモートサービスとのやり取りは、ASP.NET Core アプリの最も低速な部分です。 効率的なパフォーマンスを利用するには、データの読み取りと書き込みを効率的に行うことが重要です。

推奨事項

* すべてのデータアクセス Api を非同期**に呼び出します**。
* 必要以上に多くのデータを取得**しないで**ください。 現在の HTTP 要求に必要なデータだけを返すクエリを記述します。
* 少し古いデータが許容される場合は、データベースまたはリモートサービスから取得した頻繁にアクセスされるデータをキャッシュすること**を検討してください。** シナリオに応じて、 [Memorycache](xref:performance/caching/memory)または[microsoft.web.distributedcache](xref:performance/caching/distributed)を使用します。 詳細については、「<xref:performance/caching/response>」を参照してください。
* ネットワークラウンドトリップ**を最小限に**抑えます。 目的は、複数の呼び出しではなく、1回の呼び出しで必要なデータを取得することです。
* 読み取り専用の目的でデータにアクセスする場合**は**、Entity Framework Core で[追跡なしのクエリ](/ef/core/querying/tracking#no-tracking-queries)を使用します。 EF Core は、追跡しないクエリの結果をより効率的に返すことができます。
* (たとえば、`.Where`、`.Select`、または `.Sum` ステートメントを使用して) LINQ クエリのフィルター処理と集計を**実行**し、データベースによってフィルター処理が実行されるようにします。
* EF Core に**よってクライアント**上の一部のクエリ演算子が解決されるため、クエリの実行効率が悪くなることがあります。 詳細については、「[クライアント評価のパフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。
* コレクションに対してプロジェクションクエリを使用しないでください。これにより、"N + 1" 個の SQL クエリが実行される可能性が**あり**ます。 詳細については、「[相関サブクエリの最適化](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)」を参照してください。

高スケールアプリのパフォーマンスを向上させる方法については、「 [EF High performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) 」を参照してください。

* [DbContext プーリング](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [明示的にコンパイルされたクエリ](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

コードベースをコミットする前に、上記の高パフォーマンスアプローチの影響を測定することをお勧めします。 コンパイル済みクエリの複雑さが増えると、パフォーマンスの向上には合わない可能性があります。

[Application Insights](/azure/application-insights/app-insights-overview)またはプロファイリングツールでデータにアクセスするために費やされた時間を確認することで、クエリの問題を検出できます。 ほとんどのデータベースでは、頻繁に実行されるクエリに関する統計も利用できます。

## <a name="pool-http-connections-with-httpclientfactory"></a>HttpClientFactory を使用して HTTP 接続をプールする

[Httpclient](/dotnet/api/system.net.http.httpclient)は `IDisposable` インターフェイスを実装していますが、再利用できるように設計されています。 閉じられた `HttpClient` インスタンスは、短時間、`TIME_WAIT` の状態でソケットを開いたままにします。 `HttpClient` オブジェクトを作成および破棄するコードパスが頻繁に使用される場合、アプリは使用可能なソケットを消費する可能性があります。 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)は、この問題の解決策として ASP.NET Core 2.1 で導入されました。 これは、パフォーマンスと信頼性を最適化するために、プール HTTP 接続を処理します。

推奨事項

* `HttpClient` インスタンスを直接作成して破棄しない**で**ください。
* `HttpClient` インスタンスを取得するに**は**、 [HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を使用します。 詳細については、「 [HttpClientFactory を使用して弾力性のある HTTP 要求を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)」を参照してください。

## <a name="keep-common-code-paths-fast"></a>共通コードパスを高速に保つ

すべてのコードが高速になるようにするには、コードパスと呼ばれることがよくあります。

* アプリケーションの要求処理パイプラインのミドルウェアコンポーネント (特に、ミドルウェアはパイプラインの早い段階で実行されます)。 これらのコンポーネントは、パフォーマンスに大きな影響を与えます。
* 要求ごとに、または要求ごとに複数回実行されるコード。 たとえば、カスタムログ、承認ハンドラー、または一時的なサービスの初期化などです。

推奨事項

* 実行時間の長いタスクでカスタムミドルウェアコンポーネントを使用**しない**でください。
* [ホットコードパス](#understand-hot-code-paths)を特定するには、 [Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)や[perfview](https://github.com/Microsoft/perfview)などのパフォーマンスプロファイリングツール**を使用し**ます。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>HTTP 要求の外部で実行時間の長いタスクを完了する

ASP.NET Core アプリに対するほとんどの要求は、必要なサービスを呼び出すコントローラーまたはページモデルによって処理され、HTTP 応答を返すことができます。 長時間実行されるタスクが含まれている一部の要求では、要求-応答プロセス全体を非同期にすることをお勧めします。

推奨事項

* 通常の HTTP 要求処理の一部として、長時間実行されるタスクが完了するまで待機しない**で**ください。
* [バックグラウンドサービス](xref:fundamentals/host/hosted-services)で長時間実行される要求や、 [Azure 関数](/azure/azure-functions/)を使用したアウトプロセスを処理すること**を検討してください。** 特に、CPU を集中的に使用するタスクには、アウトプロセスの完了が役立ちます。
* [SignalR](xref:signalr/introduction)などのリアルタイム通信オプションを使用して、クライアントと非同期的に**通信します**。

## <a name="minify-client-assets"></a>クライアント資産の縮小

複雑なフロントエンドを使用する ASP.NET Core アプリは、多くの JavaScript、CSS、またはイメージファイルに頻繁に対応します。 初期読み込み要求のパフォーマンスは、次の方法で改善できます。

* バンドル。複数のファイルを1つに結合します。
* 縮小。空白とコメントを削除することによってファイルのサイズを縮小します。

推奨事項

* クライアント資産のバンドルと縮小には ASP.NET Core の[組み込みサポート](xref:client-side/bundling-and-minification)**を使用し**ます。
* 複雑なクライアント資産管理には、 [Webpack](https://webpack.js.org/)などの他のサードパーティ製ツールを使用すること**を検討してください。**

## <a name="compress-responses"></a>応答を圧縮する

 通常、応答のサイズを小さくすると、アプリの応答性が大幅に向上します。 ペイロードサイズを減らす方法の1つは、アプリの応答を圧縮することです。 詳細については、「[応答の圧縮](xref:performance/response-compression)」を参照してください。

## <a name="use-the-latest-aspnet-core-release"></a>最新の ASP.NET Core リリースを使用する

ASP.NET Core の新しいリリースにはそれぞれ、パフォーマンスが向上しています。 .NET Core と ASP.NET Core での最適化により、新しいバージョンの方が以前のバージョンより高い値になります。 たとえば、.NET Core 2.1 では、コンパイルされた正規表現と享受のサポートが[`Span<T>`](https://msdn.microsoft.com/magazine/mt814808.aspx)から追加されました。 ASP.NET Core 2.2 では、HTTP/2 のサポートが追加されました。 [ASP.NET Core 3.0](xref:aspnetcore-3.0)では、メモリ使用量を減らし、スループットを向上させる多くの機能強化が加えられています。 パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。

## <a name="minimize-exceptions"></a>例外を最小化する

例外はめったに発生しません。 例外のスローとキャッチは、他のコードフローパターンと比べて低速です。 このため、通常のプログラムフローを制御するために例外を使用することはできません。

推奨事項

* 特に[ホットコードパス](#understand-hot-code-paths)では、通常のプログラムフローの手段として例外をスローまたはキャッチし**ない**ようにします。
* 例外を発生させる条件を検出して処理するには、アプリにロジック**を含めます**。
* 例外的または予期しない条件の例外**をスローまた**はキャッチします。

Application Insights などのアプリ診断ツールを使用すると、アプリケーションでのパフォーマンスに影響する可能性のある一般的な例外を識別できます。

## <a name="performance-and-reliability"></a>パフォーマンスと信頼性

次のセクションでは、パフォーマンスのヒントと既知の信頼性の問題と解決策について説明します。

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/Httpresponse.cache 本体で同期読み取りまたは書き込みを回避する

ASP.NET Core の IO はすべて非同期です。 サーバーは、同期と非同期の両方のオーバーロードを持つ `Stream` インターフェイスを実装します。 スレッドプールのスレッドがブロックされないようにするために、非同期のものを使用することをお勧めします。 ブロックしているスレッドは、スレッドプールの枯渇につながる可能性があります。

**この**操作は避けてください。次の例では、<xref:System.IO.StreamReader.ReadToEnd*>を使用します。 このメソッドは、現在のスレッドが結果を待機するのをブロックします。 非同期的な[同期](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)の例を次に示します。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

上記のコードでは、`Get` は、HTTP 要求の本体全体をメモリに同期的に読み取ります。 クライアントが低速でアップロードしている場合、アプリは非同期で同期を実行しています。 Kestrel では同期読み取りがサポート**されてい**ないため、アプリは非同期経由で同期します。

次の**手順を実行します。** 次の例では <xref:System.IO.StreamReader.ReadToEndAsync*> を使用し、読み取り中にスレッドをブロックしません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

上記のコードは、HTTP 要求本文全体を非同期的にメモリに読み込みます。

> [!WARNING]
> 要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態になる可能性があります。 OOM を使用すると、サービス拒否が発生する可能性があります。  詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。

次の**手順を実行します。** 次の例は、バッファーされていない要求本文を使用する完全な非同期です。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

上のコードは、要求本文を非同期的にC#オブジェクトにシリアル化解除します。

## <a name="prefer-readformasync-over-requestform"></a>要求で ReadFormAsync を優先します。フォーム

`HttpContext.Request.ReadFormAsync` の代わりに `HttpContext.Request.Form` を使用します。
`HttpContext.Request.Form` は、次の条件で安全に読み取ることができます。

* フォームは `ReadFormAsync`への呼び出しによって読み取られました。
* キャッシュされたフォーム値は `HttpContext.Request.Form` を使用して読み取られています

**この**操作は避けてください。次の例では、`HttpContext.Request.Form`を使用します。  `HttpContext.Request.Form` では[async over async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)が使用されるため、スレッドプールが枯渇する可能性があります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

次の**手順を実行します。** 次の例では、`HttpContext.Request.ReadFormAsync` を使用してフォーム本文を非同期に読み取ります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>大きな要求本文または応答本文をメモリに読み込むことは避けてください

.NET では、85 KB を超えるすべてのオブジェクト割り当ては、大きなオブジェクトヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) で終了します。 ラージオブジェクトは、次の2つの方法でコストが高くなります。

* 新しく割り当てられたラージオブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。 CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。
* LOH は、ヒープの残りの部分で収集されます。 LOH には、フル[ガベージコレクション](/dotnet/standard/garbage-collection/fundamentals)または[Gen2 collection](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。

この[ブログ投稿](https://adamsitnik.com/Array-Pool/#the-problem)では、問題について簡潔に説明しています。

> ラージオブジェクトが割り当てられると、ジェネレーション2のオブジェクトとしてマークされます。 小さいオブジェクトの場合は Gen 0 として生成されません。 結果として、LOH でメモリが不足していると、LOH だけでなく、マネージヒープ全体が GC によってクリーンアップされます。 そのため、LOH を含む Gen 0、Gen 1、Gen 2 をクリーンアップします。 これはフルガベージコレクションと呼ばれ、最も時間のかかるガベージコレクションです。 多くのアプリケーションでは、許容される可能性があります。 しかし、高パフォーマンスの web サーバーでは、平均的な web 要求を処理するために大量のメモリバッファーが必要になることはありません (ソケットからの読み取り、圧縮解除、JSON & のデコードなど)。

ネイティブは、大規模な要求または応答の本文を1つの `byte[]` または `string`に格納します。

* LOH の領域がすぐに不足する可能性があります。
* 完全な Gc が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。

## <a name="working-with-a-synchronous-data-processing-api"></a>同期データ処理 API を使用した作業

同期読み取りと書き込みのみをサポートするシリアライザー/デシリアライザーを使用する場合 (たとえば、 [JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* シリアライザー/デシリアライザーに渡す前に、データをメモリに非同期的にバッファーします。

> [!WARNING]
> 要求が大きい場合、メモリ不足 (OOM) 状態になる可能性があります。 OOM を使用すると、サービス拒否が発生する可能性があります。  詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込む](#arlb)ことを避ける」を参照してください。

ASP.NET Core 3.0 では、JSON シリアル化のために既定で <xref:System.Text.Json> が使用されます。 <xref:System.Text.Json>:

* 非同期で JSON の読み取りと書き込みを行います。
* UTF-8 テキスト用に最適化されています。
* 通常、`Newtonsoft.Json` よりパフォーマンスが向上します。

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>フィールドに IHttpContextAccessor を格納しない

[IHttpContextAccessor](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)は、要求スレッドからアクセスされるときに、アクティブな要求の `HttpContext` を返します。 `IHttpContextAccessor.HttpContext` をフィールドまたは変数に格納することはでき**ません**。

**この**操作は避けてください。次の例では、フィールドに `HttpContext` を格納し、後でそれを使用しようとします。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

上記のコードでは、コンストラクター内の null または正しくない `HttpContext` が頻繁にキャプチャされます。

次の**手順を実行します。** 次に例を示します。

* フィールドに <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> を格納します。
* は、正しい時刻に `HttpContext` フィールドを使用し、`null`を確認します。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>複数のスレッドから HttpContext にアクセスしない

`HttpContext` はスレッドセーフでは*ありません*。 複数のスレッドから並行して `HttpContext` にアクセスすると、ハング、クラッシュ、データ破損などの未定義の動作が発生する可能性があります。

**この**操作は避けてください。次の例では、3つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。 要求パスは、複数のスレッドから同時にアクセスされる可能性があります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

次の**手順を実行します。** 次の例では、3つの並列要求を行う前に、受信要求からすべてのデータをコピーします。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>要求の完了後に HttpContext を使用しない

`HttpContext` は、ASP.NET Core パイプラインにアクティブな HTTP 要求がある限り有効です。 ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。 このチェーンから返された `Task` が完了すると、`HttpContext` がリサイクルされます。

**この**操作は避けてください。次の例では、最初の `await` に達したときに HTTP 要求が完了するように `async void` を使用します。

* これは、ASP.NET Core アプリでは**常**に不適切な方法です。
* HTTP 要求が完了した後、`HttpResponse` にアクセスします。
* プロセスをクラッシュさせる。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

次の**手順を実行します。** 次の例では、アクションが完了するまで HTTP 要求が完了しないように、フレームワークに `Task` を返します。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>バックグラウンドスレッドで HttpContext をキャプチャしない

**この**操作は避けてください。次の例は、`Controller` プロパティから `HttpContext` をキャプチャすることを示しています。 作業項目は次のようになる可能性があるため、この方法は不適切です。

* 要求スコープの外部で実行します。
* 間違った `HttpContext`を読み取ろうとしました。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

次の**手順を実行します。** 次に例を示します。

* 要求中にバックグラウンドタスクで必要なデータをコピーします。
* コントローラーから何も参照していません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

バックグラウンドタスクはホステッドサービスとして実装する必要があります。 詳細については、「[Background tasks with hosted services](xref:fundamentals/host/hosted-services)」(ホストされるタスクを使用するバックグラウンド タスク) を参照してください。

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>バックグラウンドスレッドでコントローラーに挿入されたサービスをキャプチャしない

**この**操作は避けてください。次の例は、`Controller` アクションパラメーターから `DbContext` をキャプチャすることを示しています。 これは不適切な方法です。  この作業項目は、要求スコープの外部で実行される可能性があります。 `ContosoDbContext` は要求に対してスコープが設定され、`ObjectDisposedException`になります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

次の**手順を実行します。** 次に例を示します。

* バックグラウンド作業項目のスコープを作成するために <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> を挿入します。 `IServiceScopeFactory` はシングルトンです。
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

**この**操作は避けてください。次のコードでは、応答が既に開始された後に応答ヘッダーを追加しようとしています。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

前のコードでは、`next()` が応答に書き込まれた場合、`context.Response.Headers["test"] = "test value";` は例外をスローします。

次の**手順を実行します。** 次の例では、ヘッダーを変更する前に、HTTP 応答が開始されたかどうかを確認します。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

次の**手順を実行します。** 次の例では、`HttpResponse.OnStarting` を使用して、応答ヘッダーをクライアントにフラッシュする前にヘッダーを設定します。

応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。 応答が開始されていないかどうかを確認しています:

* ヘッダーをジャストインタイムで追加またはオーバーライドする機能を提供します。
* では、パイプライン内の次のミドルウェアに関する知識は必要ありません。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>応答本文への書き込みを既に開始している場合は、next () を呼び出さないでください。

コンポーネントは、応答を処理および操作できる場合にのみ呼び出されることを想定しています。
