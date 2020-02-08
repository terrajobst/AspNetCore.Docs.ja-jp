---
title: .NET での gRPC でのログ記録と診断
author: jamesnk
description: .NET で gRPC アプリから診断を収集する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074524"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET での gRPC でのログ記録と診断

[James のニュートン-キング](https://twitter.com/jamesnk)別

この記事では、問題のトラブルシューティングに役立つ gRPC アプリから診断情報を収集するためのガイダンスを提供します。 取り上げるトピックは次のとおりです。

* **ログ**に構造化されたログは、 [.net Core](xref:fundamentals/logging/index)のログ記録に書き込まれます。 <xref:Microsoft.Extensions.Logging.ILogger> は、アプリフレームワークがログを書き込むために使用し、ユーザーはアプリに独自のログ記録を行います。
* **トレース**-`DiaganosticSource` と `Activity`を使用して記述された操作に関連するイベントです。 診断ソースからのトレースは、多くの場合、 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)や[OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)などのライブラリによってアプリのテレメトリを収集するために使用されます。
* **メトリック**-1 秒あたりの要求数など、一定期間にわたるデータ測定値を表します。 メトリックは `EventCounter` を使用して出力され、 [dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)コマンドラインツールまたは[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)を使用して確認できます。

## <a name="logging"></a>ログ記録

gRPC サービスと gRPC クライアントは、 [.Net Core のログ](xref:fundamentals/logging/index)記録を使用してログを書き込みます。 アプリケーションで予期しない動作をデバッグする必要がある場合は、ログを開始することをお勧めします。

### <a name="grpc-services-logging"></a>gRPC サービスのログ記録

> [!WARNING]
> サーバー側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

GRPC サービスは ASP.NET Core でホストされるため、ASP.NET Core ログシステムを使用します。 既定の構成では、gRPC はほとんど情報をログに記録しませんが、これは構成できます。 ASP.NET Core ログの構成の詳細については、 [ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。

gRPC は、[`Grpc`] カテゴリの下にログを追加します。 GRPC から詳細なログを有効にするには、次の項目を `Logging`の `LogLevel` サブセクションに追加して、 *appsettings*ファイルの `Debug` レベルに `Grpc` プレフィックスを構成します。

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

また、`ConfigureLogging`を使用して*Startup.cs*でこれを構成することもできます。

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。

* `Logging:LogLevel:Grpc` = `Debug`

入れ子になった構成値を指定する方法については、構成システムのドキュメントを確認してください。 たとえば、環境変数を使用する場合、`:` の代わりに2つの `_` 文字 (たとえば、`Logging__LogLevel__Grpc`) が使用されます。

アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することをお勧めします。 `Trace` レベルでは非常に低レベルの診断が生成されるため、アプリの問題を診断するためにはほとんど必要ありません。

#### <a name="sample-logging-output"></a>サンプルのログ記録の出力

GRPC サービスの `Debug` レベルでのコンソール出力の例を次に示します。

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

### <a name="access-server-side-logs"></a>サーバー側ログへのアクセス

サーバー側のログにアクセスする方法は、を実行している環境によって異なります。

#### <a name="as-a-console-app"></a>コンソールアプリとして

コンソールアプリでを実行している場合は、既定で[コンソール logger](xref:fundamentals/logging/index#console-provider)が有効になっている必要があります。 gRPC ログがコンソールに表示されます。

#### <a name="other-environments"></a>その他の環境

アプリが別の環境 (Docker、Kubernetes、または Windows サービスなど) にデプロイされている場合は、「<xref:fundamentals/logging/index>」を参照して、環境に適したログプロバイダーを構成する方法についての詳細を確認してください。

### <a name="grpc-client-logging"></a>gRPC クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれている場合があります。 運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。

.NET クライアントからログを取得するには、クライアントのチャネルの作成時に `GrpcChannelOptions.LoggerFactory` プロパティを設定します。 ASP.NET Core アプリから gRPC サービスを呼び出す場合は、logger ファクトリを依存関係の挿入 (DI) から解決できます。

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

クライアントのログ記録を有効にする別の方法として、 [Grpc クライアントファクトリ](xref:grpc/clientfactory)を使用してクライアントを作成する方法があります。 クライアントファクトリに登録され、DI から解決される gRPC クライアントは、アプリの構成済みログを自動的に使用します。

アプリが DI を使用していない場合は、 [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)を使用して新しい `ILoggerFactory` インスタンスを作成できます。 このメソッドにアクセスするには、アプリに[Microsoft 拡張子のログ](https://www.nuget.org/packages/microsoft.extensions.logging/)パッケージを追加します。

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC クライアントのログスコープ

GRPC クライアントは、gRPC 呼び出し中に作成されたログに[ログのスコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes)を追加します。 このスコープには、gRPC 呼び出しに関連するメタデータが含まれています。

* **Grpcmethodtype** -grpc メソッドの型。 指定できる値は `Grpc.Core.MethodType` 列挙型の名前 (単項など) です。
* **GrpcUri** -grpc メソッドの相対 URI (例:/greet.)Greeter/SayHellos

#### <a name="sample-logging-output"></a>サンプルのログ記録の出力

GRPC クライアントの `Debug` レベルでのコンソール出力の例を次に示します。

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

gRPC サービスと gRPC クライアントは、 [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource)と[アクティビティ](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)を使用して grpc 呼び出しに関する情報を提供します。

* .NET gRPC は、アクティビティを使用して gRPC 呼び出しを表します。
* トレースイベントは、gRPC 呼び出しアクティビティの開始時と停止時に診断ソースに書き込まれます。
* トレースでは、gRPC ストリーミング呼び出しの有効期間中にメッセージが送信されるタイミングに関する情報はキャプチャされません。

### <a name="grpc-service-tracing"></a>gRPC サービスのトレース

gRPC サービスは、受信 HTTP 要求に関するイベントを報告する ASP.NET Core でホストされます。 gRPC 固有のメタデータは、ASP.NET Core が提供する既存の HTTP 要求診断に追加されます。

* 診断ソース名が `Microsoft.AspNetCore`。
* アクティビティ名が `Microsoft.AspNetCore.Hosting.HttpRequestIn`。
  * GRPC 呼び出しによって呼び出される gRPC メソッドの名前は、`grpc.method`という名前のタグとして追加されます。
  * GRPC 呼び出し完了時の状態コードは、`grpc.status_code`という名前のタグとして追加されます。

### <a name="grpc-client-tracing"></a>gRPC クライアントのトレース

.NET gRPC クライアントは、`HttpClient` を使用して gRPC 呼び出しを行います。 `HttpClient` は診断イベントを書き込みますが、.NET gRPC クライアントは、gRPC 呼び出しに関する完全な情報を収集できるように、カスタム診断ソース、アクティビティ、およびイベントを提供します。

* 診断ソース名が `Grpc.Net.Client`。
* アクティビティ名が `Grpc.Net.Client.GrpcOut`。
  * GRPC 呼び出しによって呼び出される gRPC メソッドの名前は、`grpc.method`という名前のタグとして追加されます。
  * GRPC 呼び出し完了時の状態コードは、`grpc.status_code`という名前のタグとして追加されます。

### <a name="collecting-tracing"></a>トレースの収集

`DiagnosticSource` を使用する最も簡単な方法は、アプリで[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)や[OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)などのテレメトリライブラリを構成することです。 ライブラリは、他のアプリテレメトリに関する gRPC 呼び出しに関する情報を処理します。

トレースは、Application Insights などの管理されたサービスで表示できます。または、独自の分散トレースシステムを実行するように選択することもできます。 OpenTelemetry は、 [Jaeger](https://www.jaegertracing.io/)と[Zipkin](https://zipkin.io/)にトレースデータをエクスポートすることをサポートしています。

`DiagnosticSource` は、`DiagnosticListener`を使用してコードでトレースイベントを使用できます。 コードを使用して診断ソースをリッスンする方法の詳細については、『 [DiagnosticSource user's guide 』](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)を参照してください。

> [!NOTE]
> テレメトリライブラリは、現在、gRPC 固有の `Grpc.Net.Client.GrpcOut` テレメトリをキャプチャしません。 このトレースが進行中であることを把握するために、テレメトリライブラリの改善に協力してください。

## <a name="metrics"></a>メトリック

メトリックは、1秒あたりの要求数など、一定期間のデータ測定値を表します。 メトリックデータを使用すると、アプリの状態を高レベルで監視できます。 .NET gRPC メトリックは `EventCounter`を使用して出力されます。

### <a name="grpc-service-metrics"></a>gRPC サービスのメトリック

gRPC サーバーのメトリックは `Grpc.AspNetCore.Server` イベントソースで報告されます。

| Name                      | [説明]                   |
| --------------------------|-------------------------------|
| `total-calls`             | 合計呼び出し数                   |
| `current-calls`           | 現在の呼び出し                 |
| `calls-failed`            | 失敗した呼び出しの合計数            |
| `calls-deadline-exceeded` | 合計呼び出し期限を超えました |
| `messages-sent`           | 送信されたメッセージの総数           |
| `messages-received`       | 受信したメッセージの総数       |
| `calls-unimplemented`     | 未実装の呼び出しの合計数     |

ASP.NET Core は `Microsoft.AspNetCore.Hosting` イベントソースに固有のメトリックも提供します。

### <a name="grpc-client-metrics"></a>gRPC クライアントメトリック

gRPC クライアントメトリックは `Grpc.Net.Client` イベントソースで報告されます。

| Name                      | [説明]                   |
| --------------------------|-------------------------------|
| `total-calls`             | 合計呼び出し数                   |
| `current-calls`           | 現在の呼び出し                 |
| `calls-failed`            | 失敗した呼び出しの合計数            |
| `calls-deadline-exceeded` | 合計呼び出し期限を超えました |
| `messages-sent`           | 送信されたメッセージの総数           |
| `messages-received`       | 受信したメッセージの総数       |

### <a name="observe-metrics"></a>メトリックの監視

[dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)は、アドホックな正常性監視と第1レベルのパフォーマンス調査を行うためのパフォーマンス監視ツールです。 `Grpc.AspNetCore.Server` またはプロバイダー名として `Grpc.Net.Client` のいずれかを使用して .NET アプリを監視します。

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

GRPC メトリックを監視するもう1つの方法は、Application Insights の[Microsoft. ApplicationInsights. EventCounterCollector パッケージ](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)を使用してカウンターデータをキャプチャすることです。 セットアップが完了すると、Application Insights は実行時に共通の .NET カウンターを収集します。 gRPC のカウンターは既定では収集されませんが、App Insights[をカスタマイズして、追加のカウンターを含める](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)ことができます。

*Startup.cs*で収集するアプリケーションの洞察の grpc カウンターを指定します。

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

## <a name="additional-resources"></a>その他のリソース

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
