---
title: .NET での gRPC のログ記録と診断
author: jamesnk
description: .NET で gRPC アプリから診断情報を収集する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417515"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET での gRPC のログ記録と診断

作成者: [James Newton-King](https://twitter.com/jamesnk)

この記事では、問題のトラブルシューティングに役立つ診断情報を gRPC アプリから収集するためのガイダンスを提供します。 取り上げるトピックは次のとおりです。

* **ログ** - [.NET Core ログ](xref:fundamentals/logging/index)に書き込まれる構造化ログ。 <xref:Microsoft.Extensions.Logging.ILogger> は、ログを書き込むためにアプリケーション フレームワークによって使用され、アプリで独自のログ記録を行うためユーザーによって使用されます。
* **トレース** - `DiaganosticSource` と `Activity` を使用して記述される操作に関連するイベント。 診断ソースからのトレースは、通常、[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) や [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) などのライブラリによって、アプリのテレメトリを収集するために使用されます。
* **メトリック** - 1 秒あたりの要求数など、一定期間にわたるデータ測定値を表します。 メトリックは `EventCounter` を使用して出力され、[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) コマンド ライン ツールまたは [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) を使用して観察できます。

## <a name="logging"></a>ログの記録

gRPC サービスと gRPC クライアントでは、[.NET Core ログ](xref:fundamentals/logging/index)を使用してログを書き込みます。 アプリの予期しない動作をデバッグする必要があるときは、ログが適切な開始場所となります。

### <a name="grpc-services-logging"></a>gRPC サービス ログ

> [!WARNING]
> サーバー側のログには、アプリからの機密情報が含まれる場合があります。 運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。

gRPC サービスは ASP.NET Core でホストされるため、ASP.NET Core ログ システムが使用されます。 既定の構成では、gRPC でログに記録される情報は非常に少量ですが、これは構成可能です。 ASP.NET Core ログの構成の詳細については、[ASP.NET Core ログ](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。

gRPC では、`Grpc` カテゴリの下にログが追加されます。 gRPC からの詳細なログを有効にするには、`Grpc` 内の `Debug` サブセクションに次の項目を追加して、*appsettings.json* ファイルの `LogLevel` レベルに `Logging` プレフィックスを構成します。

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

これは、*を使用して*Startup.cs`ConfigureLogging` で構成することもできます。

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

JSON ベースの構成を使用していない場合は、構成システム内に次の構成値を設定します。

* `Logging:LogLevel:Grpc` = `Debug`

構成システムのドキュメントを調べて、入れ子になった構成値を指定する方法を確認してください。 たとえば、環境変数の使用時には、`_` の代わりに 2 つの `:` 文字を使用します (例: `Logging__LogLevel__Grpc`)。

アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することが推奨されます。 `Trace` レベルでは非常に低レベルの診断情報が生成されるため、アプリの問題を診断するために必要となることはまれです。

#### <a name="sample-logging-output"></a>サンプルのログ記録の出力

次に、gRPC サービスの `Debug` レベルでのコンソール出力の例を示します。

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>サーバー側ログにアクセスする

サーバー側のログにアクセスする方法は、実行環境によって異なります。

#### <a name="as-a-console-app"></a>コンソール アプリとして

コンソール アプリで実行中の場合は、[コンソール ロガー](xref:fundamentals/logging/index#console-provider)を既定で有効にする必要があります。 gRPC ログはコンソールに表示されるようになります。

#### <a name="other-environments"></a>その他の環境

アプリが別の環境 (Docker、Kubernetes、Windows サービスなど) にデプロイされる場合は、その環境に適したログ プロバイダーを構成する方法についての詳細を、「<xref:fundamentals/logging/index>」で確認してください。

### <a name="grpc-client-logging"></a>gRPC クライアント ログ

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれる場合があります。 運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。

.NET クライアントからログを取得するには、クライアントのチャネルが作成されるときに `GrpcChannelOptions.LoggerFactory` プロパティを設定します。 ASP.NET Core アプリから gRPC サービスを呼び出す場合は、依存関係の挿入 (DI) からロガー ファクトリを解決できます。

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

クライアント ログを有効にする別の方法として、[gRPC クライアント ファクトリ](xref:grpc/clientfactory) を使用してクライアントを作成する方法があります。 クライアント ファクトリに登録され、DI から解決された gRPC クライアントは、アプリの構成済みログを自動的に使用します。

アプリが DI を使用していない場合は、`ILoggerFactory`LoggerFactory.Create[ を使用して新しい ](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*) インスタンスを作成できます。 このメソッドにアクセスするには、アプリに [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) パッケージを追加します。

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC クライアント ログのスコープ

gRPC クライアントは、gRPC 呼び出し中に作成されるログに[ログ スコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) を追加します。 このスコープには、その gRPC 呼び出しに関連するメタデータが含まれています。

* **GrpcMethodType** - gRPC メソッドの型。 ありえる値は、`Grpc.Core.MethodType` 列挙型の名前です (例: Unary)。
* **GrpcUri** - gRPC メソッドの相対 URI (例: /greet.Greeter/SayHellos)

#### <a name="sample-logging-output"></a>サンプルのログ記録の出力

次に、gRPC クライアントの `Debug` レベルでのコンソール出力の例を示します。

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>トレース

gRPC サービスと gRPC クライアントでは、[DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) と [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity) を使用して、gRPC 呼び出しに関する情報を提供します。

* .NET gRPC では、アクティビティを使用して gRPC 呼び出しが表されます。
* gRPC 呼び出しアクティビティの開始時と停止時には、トレース イベントが診断ソースに書き込まれます。
* gRPC ストリーミング呼び出しの有効期間中にメッセージがいつ送信されるかに関する情報は、トレースにキャプチャされません。

### <a name="grpc-service-tracing"></a>gRPC サービスのトレース

gRPC サービスは、受信 HTTP 要求に関するイベントを報告する ASP.NET Core でホストされます。 gRPC 固有のメタデータは、ASP.NET Core で提供される既存の HTTP 要求診断に追加されます。

* 診断ソース名は `Microsoft.AspNetCore` です。
* アクティビティ名は `Microsoft.AspNetCore.Hosting.HttpRequestIn` です。
  * gRPC 呼び出しによって呼び出される gRPC メソッドの名前は、`grpc.method` という名前のタグとして追加されます。
  * gRPC 呼び出しが完了したときの状態コードは、`grpc.status_code` という名前のタグとして追加されます。

### <a name="grpc-client-tracing"></a>gRPC クライアント トレース

.NET gRPC クライアントは、`HttpClient` を使用して gRPC 呼び出しを行います。 `HttpClient` では診断イベントが書き込まれますが、.NET gRPC クライアントでは、gRPC 呼び出しに関する完全な情報を収集できるように、カスタム診断ソース、アクティビティ、およびイベントが提供されます。

* 診断ソース名は `Grpc.Net.Client` です。
* アクティビティ名は `Grpc.Net.Client.GrpcOut` です。
  * gRPC 呼び出しによって呼び出される gRPC メソッドの名前は、`grpc.method` という名前のタグとして追加されます。
  * gRPC 呼び出しが完了したときの状態コードは、`grpc.status_code` という名前のタグとして追加されます。

### <a name="collecting-tracing"></a>トレースの収集

`DiagnosticSource` を使用する最も簡単な方法は、[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) や [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) などのテレメトリ ライブラリを、アプリ内で構成することです。 ライブラリでは、他のアプリ テレメトリと一緒に、gRPC 呼び出しに関する情報を処理します。

トレースは、Application Insights のような管理サービスで表示できます。または、独自の分散トレース システムを実行することを選択できます。 OpenTelemetry は、トレース データの [Jaeger](https://www.jaegertracing.io/) や [Zipkin](https://zipkin.io/) へのエクスポートをサポートしています。

`DiagnosticSource` では、`DiagnosticListener` を使用してコード内のトレース イベントを利用できます。 コードを使用して診断ソースをリッスンすることについては、[DiagnosticSource のユーザー ガイド](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)を参照してください。

> [!NOTE]
> 現在のところ、テレメトリ ライブラリでは gRPC 固有の `Grpc.Net.Client.GrpcOut` テレメトリはキャプチャされません。 このトレースをキャプチャするテレメトリ ライブラリの改善の取り組みが進行中です。

## <a name="metrics"></a>メトリック

メトリックは、1 秒あたりの要求数など、一定期間にわたるデータ測定値を表します。 メトリック データを使用すると、アプリの状態を高レベルで監視できます。 .NET gRPC メトリックは `EventCounter` を使用して出力されます。

### <a name="grpc-service-metrics"></a>gRPC サービスのメトリック

gRPC サーバーのメトリックは、`Grpc.AspNetCore.Server` イベント ソースで報告されます。

| Name                      | 説明                   |
| --------------------------|-------------------------------|
| `total-calls`             | 合計呼び出し数                   |
| `current-calls`           | 現在の呼び出し数                 |
| `calls-failed`            | 失敗した合計呼び出し数            |
| `calls-deadline-exceeded` | 合計呼び出し数の限度を超えました |
| `messages-sent`           | 送信メッセージの総数           |
| `messages-received`       | 受信メッセージの総数       |
| `calls-unimplemented`     | 実装されていない呼び出しの総数     |

ASP.NET Core では、`Microsoft.AspNetCore.Hosting` イベント ソースに関する固有のメトリックも提供されます。

### <a name="grpc-client-metrics"></a>gRPC クライアントのメトリック

gRPC クライアントのメトリックは、`Grpc.Net.Client` イベント ソースで報告されます。

| Name                      | 説明                   |
| --------------------------|-------------------------------|
| `total-calls`             | 合計呼び出し数                   |
| `current-calls`           | 現在の呼び出し数                 |
| `calls-failed`            | 失敗した合計呼び出し数            |
| `calls-deadline-exceeded` | 合計呼び出し数の限度を超えました |
| `messages-sent`           | 送信メッセージの総数           |
| `messages-received`       | 受信メッセージの総数       |

### <a name="observe-metrics"></a>メトリックを観察する

[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) は、アドホックな正常性監視と最初のレベルのパフォーマンス調査を目的としたパフォーマンス監視ツールです。 プロバイダー名として `Grpc.AspNetCore.Server` または `Grpc.Net.Client` のいずれかを使用して .NET アプリを監視します。

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

gRPC メトリックを観察する別の方法は、Application Insights の [Microsoft ApplicationInsights.EventCounterCollector パッケージ](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)を使用してカウンター データをキャプチャすることです。 セットアップすると、Application Insights で、実行時に一般的な .NET カウンターが収集されます。 gRPC のカウンターは既定では収集されませんが、[追加のカウンターを含めるように App Insights をカスタマイズ](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)できます。

*Startup.cs* で収集するように、Application insights の gRPC カウンターを指定します。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
