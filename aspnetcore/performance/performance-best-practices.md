---
title: ASP.NETコア パフォーマンスのベスト プラクティス
author: mjrousos
description: ASP.NET Core アプリのパフォーマンスを向上させ、一般的なパフォーマンスの問題を回避するためのヒント。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
no-loc:
- SignalR
uid: performance/performance-best-practices
ms.openlocfilehash: 068a35fbe410dad24030fe68c0dfd062b402212c
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977185"
---
# <a name="aspnet-core-performance-best-practices"></a>ASP.NETコア パフォーマンスのベスト プラクティス

作成者: [Mike Rousos](https://github.com/mjrousos)

この記事では、ASP.NET Core のパフォーマンスのベスト プラクティスに関するガイドラインを提供します。

## <a name="cache-aggressively"></a>積極的にキャッシュ

キャッシュについては、このドキュメントのいくつかの部分で説明します。 詳細については、「<xref:performance/caching/response>」を参照してください。

## <a name="understand-hot-code-paths"></a>ホット コード パスを理解する

このドキュメントでは、*ホット コード パス*は、頻繁に呼び出されるコード パスとして定義され、実行時間の多くが発生する場所です。 通常、ホット コード パスはアプリのスケールアウトとパフォーマンスを制限します。

## <a name="avoid-blocking-calls"></a>通話のブロックを避ける

core アプリASP.NET、多数の要求を同時に処理するように設計する必要があります。 非同期 API を使用すると、スレッドの小さなプールが、ブロッキング呼び出しを待機しないことによって数千の同時要求を処理できます。 実行時間の長い同期タスクの完了を待機するのではなく、スレッドは別の要求で動作できます。

ASP.NET Core アプリでの一般的なパフォーマンスの問題は、非同期である可能性のある呼び出しをブロックすることです。 多くの同期ブロッキング呼び出しは[、スレッドプールの枯不足](https://blogs.msdn.microsoft.com/vancem/2018/10/16/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall/)と応答時間の低下につながります。

**しないでください**:

* [Task.Wait](/dotnet/api/system.threading.tasks.task.wait)または[Task.Result](/dotnet/api/system.threading.tasks.task-1.result)を呼び出して非同期実行をブロックします。
* 共通コード パスのロックを取得します。 ASP.NET Core アプリは、コードを並列に実行するように設計された場合に最もパフォーマンスが高くなります。
* [Task.Run を](/dotnet/api/system.threading.tasks.task.run)呼び出し、すぐにそれを待ちます。 ASP.NET Core は既に通常のスレッド プール スレッドでアプリ コードを実行しているので、Task.Run を呼び出すと余分な不要なスレッド プールのスケジュールが作成されるだけです。 スケジュールされたコードがスレッドをブロックする場合でも、Task.Run は、それを防止しません。

**行う**:

* [ホット コード パス](#understand-hot-code-paths)を非同期にします。
* 非同期 API が使用可能な場合は、データ アクセス、I/O、および長時間実行の操作 API を非同期に呼び出します。 同期 API を非同期にする場合は[、Task.Run](/dotnet/api/system.threading.tasks.task.run)を使用**しないでください**。
* コントローラー/Razor ページ アクションを非同期にします。 [非同期/待機](/dotnet/csharp/programming-guide/concepts/async/)パターンの恩恵を受けるために、呼び出し履歴全体が非同期です。

[PerfView](https://github.com/Microsoft/perfview)などのプロファイラーを使用して、スレッド プールに頻繁に追加される[スレッド](/windows/desktop/procthread/thread-pools)を検索できます。 この`Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start`イベントは、スレッド プールに追加されたスレッドを示します。 <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="minimize-large-object-allocations"></a>大きなオブジェクトの割り当てを最小限に抑える

[NET Core ガベージ コレクター](/dotnet/standard/garbage-collection/)は、ASP.NET Core アプリでメモリの割り当てと解放を自動的に管理します。 自動ガベージ コレクションは、一般に、開発者がメモリが解放される方法やタイミングを心配する必要がならないことを意味します。 ただし、参照されていないオブジェクトのクリーンアップには CPU 時間がかかるため、開発者は[ホット コード パス](#understand-hot-code-paths)でのオブジェクトの割り当てを最小限に抑える必要があります。 ガベージ コレクションは、大きなオブジェクト (> 85 Kb) では特に負荷が高くなります。 ラージ オブジェクトは[ラージ オブジェクト ヒープ](/dotnet/standard/garbage-collection/large-object-heap)に格納され、クリーンアップには完全な (ジェネレーション 2) ガベージ コレクションが必要です。 ジェネレーション 0 およびジェネレーション 1 コレクションとは異なり、ジェネレーション 2 コレクションでは、アプリの実行を一時的に中断する必要があります。 大きなオブジェクトの頻繁な割り当てと割り当て解除は、パフォーマンスの一貫性に欠けます。

推奨事項:

* 頻繁に使用される大きなオブジェクトをキャッシュすることを検討**してください**。 大きなオブジェクトをキャッシュすると、負荷の高い割り当てを防ぐことができます。
* 大規模な配列を格納するために[、ArrayPool\<T>](/dotnet/api/system.buffers.arraypool-1)を使用して、プール・バッファーを**実行**します。
* [ホット コード パス](#understand-hot-code-paths)に、短時間で使用する多数の大きなオブジェクトを割り当て**ないでください**。

上記のようなメモリの問題は[、PerfView](https://github.com/Microsoft/perfview)でガベージ コレクション (GC) の統計を確認し、次の点を調べることによって診断できます。

* ガベージ コレクションの休止時間。
* ガベージ コレクションに費やされるプロセッサ時間の割合。
* 世代 0、1、2 のガベージ コレクションの数。

詳細については、「[ガベージ コレクションとパフォーマンス](/dotnet/standard/garbage-collection/performance)」を参照してください。

## <a name="optimize-data-access-and-io"></a>データ アクセスと I/O を最適化する

データ ストアやその他のリモート サービスとのやり取りは、多くの場合、ASP.NET Core アプリの最も低速な部分です。 パフォーマンスを向上させるには、データの読み取りと書き込みを効率的に行う必要があります。

推奨事項:

* すべてのデータ アクセス API を非同期的に呼び出**します**。
* 必要以上のデータを取得**しないでください**。 現在の HTTP 要求に必要なデータだけを返すクエリを記述します。
* 少しデータが最新のデータに問題がある場合は、データベースまたはリモート サービスから取得される頻繁にアクセスされるデータをキャッシュすることを検討**してください**。 シナリオに応じて、メモリ[キャッシュ](xref:performance/caching/memory)または[分散キャッシュ](xref:performance/caching/distributed)を使用します。 詳細については、「<xref:performance/caching/response>」を参照してください。
* ネットワークのラウンド トリップ**を**最小限に抑えます。 目標は、複数の呼び出しではなく、1 回の呼び出しで必要なデータを取得することです。
* 読み取り専用の目的でデータにアクセスする場合は、Entity Framework Core で[追跡クエリ](/ef/core/querying/tracking#no-tracking-queries)を使用**しないでください**。 EF Core は、追跡を行っていないクエリの結果をより効率的に返すことができます。
* データベース**によってフィルター処理**が実行されるように、LINQ `.Where``.Select`クエリ`.Sum`(、、、または ステートメントなど) をフィルター処理して集計します。
* EF Core がクライアント上の一部のクエリ演算子を解決し、クエリの実行が非効率的になる可能性があることを考慮**してください**。 詳細については、「[クライアント評価パフォーマンスの問題](/ef/core/querying/client-eval#client-evaluation-performance-issues)」を参照してください。
* "N + 1" SQL クエリを実行する可能性があるコレクションに対して、プロジェクション クエリを使用**しないでください**。 詳しくは、[相関サブクエリの最適化を](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries)参照してください。

ハイ スケール アプリのパフォーマンスを向上させる可能性のあるアプローチについては[、「EF ハイ](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)パフォーマンス」をご覧ください。

* [DbContext プール](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [明示的にコンパイルされたクエリ](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

コード ベースをコミットする前に、前述の高性能アプローチの影響を測定することをお勧めします。 コンパイルされたクエリの複雑さが増しても、パフォーマンスの向上が正当化されない場合があります。

クエリの問題は[、Application Insights](/azure/application-insights/app-insights-overview)を使用してデータにアクセスするのに要した時間を確認するか、プロファイリング ツールを使用して検出できます。 ほとんどのデータベースでは、頻繁に実行されるクエリに関する統計情報も利用できます。

## <a name="pool-http-connections-with-httpclientfactory"></a>HTTP 接続を使用して HTTP 接続をプールします。

[HttpClient は](/dotnet/api/system.net.http.httpclient)インターフェイスを`IDisposable`実装していますが、再利用用に設計されています。 クローズ`HttpClient`されたインスタンスは、ソケットを`TIME_WAIT`短時間開いたままにします。 `HttpClient`オブジェクトを作成して破棄するコード パスが頻繁に使用される場合、アプリは利用可能なソケットを使い果たす可能性があります。 [この問題の](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)解決策として、ASP.NETコア 2.1 で導入されました。 パフォーマンスと信頼性を最適化するために、HTTP 接続のプールを処理します。

推奨事項:

* インスタンスを直接作成して破棄**しないでください。** `HttpClient`
* インスタンスを取得`HttpClient`するには[、HttpClientFactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)を使用**してください**。 詳細については、「[HttpClientFactory を使用して回復力の高い HTTP 要求を実装する](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)」を参照してください。

## <a name="keep-common-code-paths-fast"></a>共通のコード パスを高速に維持する

すべてのコードを高速にする必要があります。 頻繁に呼び出されるコード パスは、最適化が最も重要です。 次の設定があります。

* アプリの要求処理パイプラインのミドルウェア コンポーネント、特にミドルウェアはパイプラインの初期段階で実行されます。 これらのコンポーネントは、パフォーマンスに大きな影響を与えます。
* 要求ごとに実行されるコード、または要求ごとに複数回実行されるコード。 たとえば、カスタム ログ、承認ハンドラー、一時的なサービスの初期化などです。

推奨事項:

* 実行時間の長いタスクでは、カスタムミドルウェアコンポーネントを使用**しないでください**。
* パフォーマンス プロファイリング ツール[(Visual Studio 診断ツール](/visualstudio/profiling/profiling-feature-tour)や[PerfView](https://github.com/Microsoft/perfview)など) を使用して[、ホット コード パス](#understand-hot-code-paths)を識別**する**。

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>HTTP 要求以外の長時間実行タスクを完了する

ASP.NET Core アプリへのほとんどの要求は、必要なサービスを呼び出して HTTP 応答を返すコントローラーまたはページ モデルによって処理できます。 実行時間の長いタスクを含む一部の要求では、要求/応答プロセス全体を非同期にすることをお勧めします。

推奨事項:

* 通常の HTTP 要求処理の一部として、長時間実行されるタスクが完了するのを待**つ必要はありません**。
* [Azure 関数](/azure/azure-functions/)**を**使用して[、バックグラウンド サービス](xref:fundamentals/host/hosted-services)またはアウト プロセスで長時間実行される要求を処理することを検討してください。 プロセス外で作業を完了することは、CPU を集中的に使用するタスクに特に役立ちます。
* **クライアント**と非同期通信するには、 などの[SignalR](xref:signalr/introduction)リアルタイム通信オプションを使用します。

## <a name="minify-client-assets"></a>顧客資産の縮小

複雑なフロントエンドを備えたASP.NETコアアプリは、多くのJavaScript、CSS、または画像ファイルを頻繁に提供します。 初期ロード要求のパフォーマンスは、次の方法で改善できます。

* 複数のファイルを 1 つに結合するバンドル。
* 縮小 : 空白やコメントを削除してファイルのサイズを縮小します。

推奨事項:

* クライアント資産のバンドルと縮小に対する Core の[組み込みサポート](xref:client-side/bundling-and-minification)ASP.NET使用**してください**。
* 複雑なクライアント資産管理のために[、Webpack](https://webpack.js.org/)などの他のサードパーティ製ツールを検討**してください**。

## <a name="compress-responses"></a>応答を圧縮する

 応答のサイズを小さくすると、通常、アプリの応答性が向上し、多くの場合、劇的に増加します。 ペイロード サイズを小さくする方法の 1 つは、アプリの応答を圧縮することです。 詳細については、[応答圧縮](xref:performance/response-compression)を参照してください。

## <a name="use-the-latest-aspnet-core-release"></a>最新のASP.NETコア リリースを使用する

ASP.NET Core の新しいリリースごとに、パフォーマンスの向上が含まれています。 NET Core と ASP.NET Core の最適化は、新しいバージョンが一般的に古いバージョンよりも優れ、その結果を上回るということです。 たとえば、.NET Core 2.1 はコンパイルされた正規表現のサポートを追加し[、Span\<T>](https://msdn.microsoft.com/magazine/mt814808.aspx)の恩恵を受けています。 ASP.NETコア 2.2 HTTP/2 のサポートが追加されました。 [ASP.NET Core 3.0 では、](xref:aspnetcore-3.0)メモリ使用量を削減し、スループットを向上させる多くの機能強化が追加されました。 パフォーマンスが優先される場合は、現在のバージョンの ASP.NET Core にアップグレードすることを検討してください。

## <a name="minimize-exceptions"></a>例外を最小限に抑える

例外はまれです。 例外のスローとキャッチは、他のコード フロー パターンに比べて遅くなります。 このため、通常のプログラム フローを制御するために例外を使用しないでください。

推奨事項:

* 通常のプログラム フローの手段として例外をスローまたはキャッチ**しないでください**[。](#understand-hot-code-paths)
* 例外の原因となる条件を検出して処理するロジックをアプリ**に含めます**。
* 異常なまたは予期しない条件の例外をスローまたはキャッチ**してください**。

アプリケーションインサイトなどのアプリ診断ツールは、パフォーマンスに影響を与える可能性のあるアプリの一般的な例外を特定するのに役立ちます。

## <a name="performance-and-reliability"></a>パフォーマンスと信頼性

以下のセクションでは、パフォーマンスに関するヒント、既知の信頼性の問題と解決策を示します。

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>HttpRequest/HttpResponse 本体で同期読み取りまたは書き込みを回避する

ASP.NETコアのすべての I/O は非同期です。 サーバーは、同期`Stream`および非同期の両方のオーバーロードを持つインターフェイスを実装します。 スレッド プールスレッドをブロックしないように、非同期のスレッドを優先する必要があります。 スレッドをブロックすると、スレッド プールの不足が生じる可能性があります。

**次の操作を行わない**。次の例では、<xref:System.IO.StreamReader.ReadToEnd*>を使用します。 現在のスレッドが結果を待つブロックされます。 これは、非同期での[同期の](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)例です。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

上記のコードでは、`Get`同期的に、HTTP 要求本文全体をメモリに読み取ります。 クライアントのアップロードが遅い場合、アプリは非同期で同期を実行しています。 Kestrel は同期読み取りをサポート**していないため**、アプリは非同期で同期します。

**これを行います。** 次の例では<xref:System.IO.StreamReader.ReadToEndAsync*>、読み取り中にスレッドを使用し、ブロックしません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

上記のコードは、HTTP 要求本文全体をメモリに非同期的に読み取ります。

> [!WARNING]
> 要求が大きい場合、HTTP 要求本文全体をメモリに読み込むと、メモリ不足 (OOM) 状態が発生する可能性があります。 OOM はサービス拒否の原因になります。  詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込まないように](#arlb)する」を参照してください。

**これを行います。** 次の例は、バッファリングされていない要求本文を使用して完全に非同期です。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

上記のコードでは、要求本文を非同期に C# オブジェクトに逆シリアル化します。

## <a name="prefer-readformasync-over-requestform"></a>要求よりも ReadFormAsync を優先します。

`HttpContext.Request.ReadFormAsync` の代わりに `HttpContext.Request.Form` を使用します。
`HttpContext.Request.Form`次の条件で安全に読み取り専用にすることができます。

* フォームは、 への`ReadFormAsync`呼び出しによって読み取られた
* キャッシュされたフォーム値を使用して読み取っています`HttpContext.Request.Form`

**次の操作を行わない**。次の例では`HttpContext.Request.Form`、 を使用します。  `HttpContext.Request.Form`[は非同期同期を](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
)使用するため、スレッド プールの枯れにつながる可能性があります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**これを行います。** 次の例では`HttpContext.Request.ReadFormAsync`、フォーム本体を非同期的に読み取るために使用します。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>大きな要求本文または応答本文をメモリに読み込まないようにする

.NET では、85 KB を超えるオブジェクト割り当てはすべて、ラージ オブジェクト ヒープ ([LOH](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)) に配置されます。 大きなオブジェクトは、次の 2 つの点でコストがかかります。

* 新しく割り当てられたラージ オブジェクトのメモリをクリアする必要があるため、割り当てコストが高くなります。 CLR は、新しく割り当てられたすべてのオブジェクトのメモリがクリアされることを保証します。
* LOH はヒープの残りの部分と共に収集されます。 LOH には、フル[ガベージ コレクション](/dotnet/standard/garbage-collection/fundamentals)または[Gen2 コレクション](/dotnet/standard/garbage-collection/fundamentals#generations)が必要です。

この[ブログ記事](https://adamsitnik.com/Array-Pool/#the-problem)では、問題を簡潔に説明します。

> 大きなオブジェクトが割り当てられると、Gen 2 オブジェクトとしてマークされます。 小さなオブジェクトに関しては Gen 0 ではありません。 その結果、LOH でメモリが不足すると、GC は LOH だけでなくマネージ ヒープ全体をクリーンアップします。 そこで、LOH を含む Gen 0、Gen 1、および Gen 2 をクリーンアップします。 これはフル ガベージ コレクションと呼ばれ、最も時間のかかるガベージ コレクションです。 多くのアプリケーションでは、許容できます。 しかし、平均的なウェブリクエスト(ソケットからの読み取り、解凍、JSON&のデコード)を処理するために必要な大きなメモリバッファがほとんどない高性能ウェブサーバーには、間違いなくありません。

単純に単一`byte[]`または`string`に大きな要求または応答の本文を格納します。

* LOH のスペースが急速に不足する可能性があります。
* フル GC が実行されているため、アプリのパフォーマンスの問題が発生する可能性があります。

## <a name="working-with-a-synchronous-data-processing-api"></a>同期データ処理 API の操作

同期読み取りと書き込みのみをサポートするシリアライザー/逆シリアライザーを使用する場合 ([たとえば、JSON.NET):](https://www.newtonsoft.com/json/help/html/Introduction.htm)

* シリアライザー/逆シリアライザーに渡す前に、データを非同期にメモリにバッファーします。

> [!WARNING]
> 要求が大きい場合は、メモリ不足 (OOM) 状態が発生する可能性があります。 OOM はサービス拒否の原因になります。  詳細については、このドキュメントの「[大きな要求本文または応答本文をメモリに読み込まないように](#arlb)する」を参照してください。

ASP.NET Core 3.0 では、既定で JSON シリアル化が使用<xref:System.Text.Json>されます。 <xref:System.Text.Json>:

* 非同期で JSON の読み取りと書き込みを行います。
* UTF-8 テキスト用に最適化されています。
* 通常、`Newtonsoft.Json` よりパフォーマンスが向上します。

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>フィールドに IHttp コンテキストアクセサー.Http コンテキストを格納しないでください。

[要求](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)スレッドからアクセスされると、アクティブな要求`HttpContext`のを返します。 フィールド`IHttpContextAccessor.HttpContext`または変数には格納**しないでください**。

**次の操作を行わない**。次の例では、`HttpContext`をフィールドに格納し、後で使用しようとします。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

上記のコードでは、コンストラクターで null または`HttpContext`誤りがある場合に、頻繁にキャプチャします。

**これを行います。** 次の例を示します。

* フィールドに<xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>を格納します。
* フィールドを`HttpContext`正しい時刻に使用し、`null`をチェックします。

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>複数のスレッドから HttpContext にアクセスしない

`HttpContext`はスレッドセーフ*ではありません*。 複数の`HttpContext`スレッドから並列にアクセスすると、ハング、クラッシュ、データの破損などの未定義の動作が発生する可能性があります。

**次の操作を行わない**。次の例では、3 つの並列要求を行い、発信 HTTP 要求の前後に受信要求パスをログに記録します。 要求パスは、複数のスレッドからアクセスされ、場合によっては並列でアクセスされる可能性があります。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**これを行います。** 次の例では、3 つの並列要求を行う前に、受信要求からすべてのデータをコピーします。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>要求が完了した後に HttpContext を使用しない

`HttpContext`は、ASP.NETコア パイプラインにアクティブな HTTP 要求がある場合にのみ有効です。 ASP.NET Core パイプライン全体は、すべての要求を実行するデリゲートの非同期チェーンです。 このチェーン`Task`から返されたが完了すると、が`HttpContext`リサイクルされます。

**次の操作を行わない**。次の例では`async void`、最初`await`の要求に達したときに HTTP 要求を完了させる例を使用します。

* これは**常に**ASP.NETコアアプリでは悪い習慣です。
* HTTP 要求`HttpResponse`が完了した後に にアクセスします。
* プロセスをクラッシュさせます。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**これを行います。** 次の例では、`Task`フレームワークに a が返されるため、アクションが完了するまで HTTP 要求は完了しません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>バックグラウンド スレッドで HttpContext をキャプチャしない

**次の操作を行わない**。次の例は、クロージャが`HttpContext``Controller`プロパティからキャプチャされていることを示しています。 作業項目は次の処理を行うことができるため、これは不適切な方法です。

* 要求スコープの外部で実行します。
* 間違った`HttpContext`を読み取ろうとします。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**これを行います。** 次の例を示します。

* 要求中にバックグラウンド タスクに必要なデータをコピーします。
* コントローラーから何も参照しません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

バックグラウンド タスクは、ホストされたサービスとして実装する必要があります。 詳細については、「[Background tasks with hosted services](xref:fundamentals/host/hosted-services)」(ホストされるタスクを使用するバックグラウンド タスク) を参照してください。

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>バックグラウンド スレッドのコントローラに挿入されたサービスをキャプチャしない

**次の操作を行わない**。次の例は、`DbContext``Controller`クロージャが action パラメーターからキャプチャされていることを示しています。 これは悪い習慣です。  作業項目は、要求スコープの外部で実行できます。 は`ContosoDbContext`、要求の範囲が指定され、その結果`ObjectDisposedException`、 が返されます。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**これを行います。** 次の例を示します。

* バックグラウンド作業項目に<xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>スコープを作成するために、 を挿入します。 `IServiceScopeFactory`シングルトンです。
* バックグラウンド スレッドに新しい依存関係挿入スコープを作成します。
* コントローラーから何も参照しません。
* 受信した要求から`ContosoDbContext`をキャプチャしません。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

次の強調表示されたコード。

* バックグラウンド操作の有効期間のスコープを作成し、そこからサービスを解決します。
* 正`ContosoDbContext`しいスコープから使用します。

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>応答本文が開始された後、状態コードまたはヘッダーを変更しない

ASP.NETコアは HTTP 応答本体をバッファしません。 初めて応答を書き込む場合:

* ヘッダーは、その本体のチャンクと共にクライアントに送信されます。
* 応答ヘッダーを変更することはできなくなりました。

**次の操作を行わない**。次のコードは、応答が既に開始された後に応答ヘッダーを追加しようとします。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

上記のコードでは、`context.Response.Headers["test"] = "test value";`応答に書き込まれた`next()`場合は例外をスローします。

**これを行います。** 次の例では、ヘッダーを変更する前に HTTP 応答が開始されているかどうかを確認します。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**これを行います。** 応答ヘッダーがクライアント`HttpResponse.OnStarting`にフラッシュされる前にヘッダーを設定する例を次に示します。

応答が開始されていないかどうかを確認すると、応答ヘッダーが書き込まれる直前に呼び出されるコールバックを登録できます。 応答が開始されていないかどうかの確認:

* ヘッダーを追加またはオーバーライドする機能を提供します。
* パイプライン内の次のミドルウェアに関する知識は必要ありません。

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>応答本文への書き込みを既に開始している場合は next() を呼び出さない

コンポーネントは、応答を処理して操作できる場合にのみ呼び出されることを想定します。
