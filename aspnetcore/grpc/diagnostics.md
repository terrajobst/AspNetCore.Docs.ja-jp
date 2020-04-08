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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="6c10e-103">.NET での gRPC のログ記録と診断</span><span class="sxs-lookup"><span data-stu-id="6c10e-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="6c10e-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6c10e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6c10e-105">この記事では、問題のトラブルシューティングに役立つ診断情報を gRPC アプリから収集するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="6c10e-106">取り上げるトピックは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6c10e-106">Topics covered include:</span></span>

* <span data-ttu-id="6c10e-107">**ログ** - [.NET Core ログ](xref:fundamentals/logging/index)に書き込まれる構造化ログ。</span><span class="sxs-lookup"><span data-stu-id="6c10e-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6c10e-108"><xref:Microsoft.Extensions.Logging.ILogger> は、ログを書き込むためにアプリケーション フレームワークによって使用され、アプリで独自のログ記録を行うためユーザーによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="6c10e-109">**トレース** - `DiaganosticSource` と `Activity` を使用して記述される操作に関連するイベント。</span><span class="sxs-lookup"><span data-stu-id="6c10e-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="6c10e-110">診断ソースからのトレースは、通常、[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) や [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) などのライブラリによって、アプリのテレメトリを収集するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="6c10e-111">**メトリック** - 1 秒あたりの要求数など、一定期間にわたるデータ測定値を表します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="6c10e-112">メトリックは `EventCounter` を使用して出力され、[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) コマンド ライン ツールまたは [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) を使用して観察できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="6c10e-113">ログの記録</span><span class="sxs-lookup"><span data-stu-id="6c10e-113">Logging</span></span>

<span data-ttu-id="6c10e-114">gRPC サービスと gRPC クライアントでは、[.NET Core ログ](xref:fundamentals/logging/index)を使用してログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6c10e-115">アプリの予期しない動作をデバッグする必要があるときは、ログが適切な開始場所となります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="6c10e-116">gRPC サービス ログ</span><span class="sxs-lookup"><span data-stu-id="6c10e-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="6c10e-117">サーバー側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6c10e-118">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="6c10e-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6c10e-119">gRPC サービスは ASP.NET Core でホストされるため、ASP.NET Core ログ システムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="6c10e-120">既定の構成では、gRPC でログに記録される情報は非常に少量ですが、これは構成可能です。</span><span class="sxs-lookup"><span data-stu-id="6c10e-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="6c10e-121">ASP.NET Core ログの構成の詳細については、[ASP.NET Core ログ](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c10e-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="6c10e-122">gRPC では、`Grpc` カテゴリの下にログが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="6c10e-123">gRPC からの詳細なログを有効にするには、`Grpc` 内の `Debug` サブセクションに次の項目を追加して、*appsettings.json* ファイルの `LogLevel` レベルに `Logging` プレフィックスを構成します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="6c10e-124">これは、*を使用して*Startup.cs`ConfigureLogging` で構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="6c10e-125">JSON ベースの構成を使用していない場合は、構成システム内に次の構成値を設定します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="6c10e-126">構成システムのドキュメントを調べて、入れ子になった構成値を指定する方法を確認してください。</span><span class="sxs-lookup"><span data-stu-id="6c10e-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="6c10e-127">たとえば、環境変数の使用時には、`_` の代わりに 2 つの `:` 文字を使用します (例: `Logging__LogLevel__Grpc`)。</span><span class="sxs-lookup"><span data-stu-id="6c10e-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="6c10e-128">アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="6c10e-129">`Trace` レベルでは非常に低レベルの診断情報が生成されるため、アプリの問題を診断するために必要となることはまれです。</span><span class="sxs-lookup"><span data-stu-id="6c10e-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="6c10e-130">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="6c10e-130">Sample logging output</span></span>

<span data-ttu-id="6c10e-131">次に、gRPC サービスの `Debug` レベルでのコンソール出力の例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="6c10e-132">サーバー側ログにアクセスする</span><span class="sxs-lookup"><span data-stu-id="6c10e-132">Access server-side logs</span></span>

<span data-ttu-id="6c10e-133">サーバー側のログにアクセスする方法は、実行環境によって異なります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="6c10e-134">コンソール アプリとして</span><span class="sxs-lookup"><span data-stu-id="6c10e-134">As a console app</span></span>

<span data-ttu-id="6c10e-135">コンソール アプリで実行中の場合は、[コンソール ロガー](xref:fundamentals/logging/index#console-provider)を既定で有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="6c10e-136">gRPC ログはコンソールに表示されるようになります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="6c10e-137">その他の環境</span><span class="sxs-lookup"><span data-stu-id="6c10e-137">Other environments</span></span>

<span data-ttu-id="6c10e-138">アプリが別の環境 (Docker、Kubernetes、Windows サービスなど) にデプロイされる場合は、その環境に適したログ プロバイダーを構成する方法についての詳細を、「<xref:fundamentals/logging/index>」で確認してください。</span><span class="sxs-lookup"><span data-stu-id="6c10e-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="6c10e-139">gRPC クライアント ログ</span><span class="sxs-lookup"><span data-stu-id="6c10e-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="6c10e-140">クライアント側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6c10e-141">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="6c10e-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6c10e-142">.NET クライアントからログを取得するには、クライアントのチャネルが作成されるときに `GrpcChannelOptions.LoggerFactory` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="6c10e-143">ASP.NET Core アプリから gRPC サービスを呼び出す場合は、依存関係の挿入 (DI) からロガー ファクトリを解決できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="6c10e-144">クライアント ログを有効にする別の方法として、[gRPC クライアント ファクトリ](xref:grpc/clientfactory) を使用してクライアントを作成する方法があります。</span><span class="sxs-lookup"><span data-stu-id="6c10e-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="6c10e-145">クライアント ファクトリに登録され、DI から解決された gRPC クライアントは、アプリの構成済みログを自動的に使用します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="6c10e-146">アプリが DI を使用していない場合は、`ILoggerFactory`LoggerFactory.Create[ を使用して新しい ](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*) インスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="6c10e-147">このメソッドにアクセスするには、アプリに [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="6c10e-148">gRPC クライアント ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="6c10e-148">gRPC client log scopes</span></span>

<span data-ttu-id="6c10e-149">gRPC クライアントは、gRPC 呼び出し中に作成されるログに[ログ スコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) を追加します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="6c10e-150">このスコープには、その gRPC 呼び出しに関連するメタデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6c10e-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="6c10e-151">**GrpcMethodType** - gRPC メソッドの型。</span><span class="sxs-lookup"><span data-stu-id="6c10e-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="6c10e-152">ありえる値は、`Grpc.Core.MethodType` 列挙型の名前です (例: Unary)。</span><span class="sxs-lookup"><span data-stu-id="6c10e-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="6c10e-153">**GrpcUri** - gRPC メソッドの相対 URI (例: /greet.Greeter/SayHellos)</span><span class="sxs-lookup"><span data-stu-id="6c10e-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="6c10e-154">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="6c10e-154">Sample logging output</span></span>

<span data-ttu-id="6c10e-155">次に、gRPC クライアントの `Debug` レベルでのコンソール出力の例を示します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="6c10e-156">トレース</span><span class="sxs-lookup"><span data-stu-id="6c10e-156">Tracing</span></span>

<span data-ttu-id="6c10e-157">gRPC サービスと gRPC クライアントでは、[DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) と [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity) を使用して、gRPC 呼び出しに関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="6c10e-158">.NET gRPC では、アクティビティを使用して gRPC 呼び出しが表されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="6c10e-159">gRPC 呼び出しアクティビティの開始時と停止時には、トレース イベントが診断ソースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="6c10e-160">gRPC ストリーミング呼び出しの有効期間中にメッセージがいつ送信されるかに関する情報は、トレースにキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="6c10e-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="6c10e-161">gRPC サービスのトレース</span><span class="sxs-lookup"><span data-stu-id="6c10e-161">gRPC service tracing</span></span>

<span data-ttu-id="6c10e-162">gRPC サービスは、受信 HTTP 要求に関するイベントを報告する ASP.NET Core でホストされます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="6c10e-163">gRPC 固有のメタデータは、ASP.NET Core で提供される既存の HTTP 要求診断に追加されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="6c10e-164">診断ソース名は `Microsoft.AspNetCore` です。</span><span class="sxs-lookup"><span data-stu-id="6c10e-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="6c10e-165">アクティビティ名は `Microsoft.AspNetCore.Hosting.HttpRequestIn` です。</span><span class="sxs-lookup"><span data-stu-id="6c10e-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="6c10e-166">gRPC 呼び出しによって呼び出される gRPC メソッドの名前は、`grpc.method` という名前のタグとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="6c10e-167">gRPC 呼び出しが完了したときの状態コードは、`grpc.status_code` という名前のタグとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="6c10e-168">gRPC クライアント トレース</span><span class="sxs-lookup"><span data-stu-id="6c10e-168">gRPC client tracing</span></span>

<span data-ttu-id="6c10e-169">.NET gRPC クライアントは、`HttpClient` を使用して gRPC 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="6c10e-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="6c10e-170">`HttpClient` では診断イベントが書き込まれますが、.NET gRPC クライアントでは、gRPC 呼び出しに関する完全な情報を収集できるように、カスタム診断ソース、アクティビティ、およびイベントが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="6c10e-171">診断ソース名は `Grpc.Net.Client` です。</span><span class="sxs-lookup"><span data-stu-id="6c10e-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="6c10e-172">アクティビティ名は `Grpc.Net.Client.GrpcOut` です。</span><span class="sxs-lookup"><span data-stu-id="6c10e-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="6c10e-173">gRPC 呼び出しによって呼び出される gRPC メソッドの名前は、`grpc.method` という名前のタグとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="6c10e-174">gRPC 呼び出しが完了したときの状態コードは、`grpc.status_code` という名前のタグとして追加されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="6c10e-175">トレースの収集</span><span class="sxs-lookup"><span data-stu-id="6c10e-175">Collecting tracing</span></span>

<span data-ttu-id="6c10e-176">`DiagnosticSource` を使用する最も簡単な方法は、[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) や [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) などのテレメトリ ライブラリを、アプリ内で構成することです。</span><span class="sxs-lookup"><span data-stu-id="6c10e-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="6c10e-177">ライブラリでは、他のアプリ テレメトリと一緒に、gRPC 呼び出しに関する情報を処理します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="6c10e-178">トレースは、Application Insights のような管理サービスで表示できます。または、独自の分散トレース システムを実行することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="6c10e-179">OpenTelemetry は、トレース データの [Jaeger](https://www.jaegertracing.io/) や [Zipkin](https://zipkin.io/) へのエクスポートをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="6c10e-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="6c10e-180">`DiagnosticSource` では、`DiagnosticListener` を使用してコード内のトレース イベントを利用できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="6c10e-181">コードを使用して診断ソースをリッスンすることについては、[DiagnosticSource のユーザー ガイド](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6c10e-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="6c10e-182">現在のところ、テレメトリ ライブラリでは gRPC 固有の `Grpc.Net.Client.GrpcOut` テレメトリはキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="6c10e-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="6c10e-183">このトレースをキャプチャするテレメトリ ライブラリの改善の取り組みが進行中です。</span><span class="sxs-lookup"><span data-stu-id="6c10e-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="6c10e-184">メトリック</span><span class="sxs-lookup"><span data-stu-id="6c10e-184">Metrics</span></span>

<span data-ttu-id="6c10e-185">メトリックは、1 秒あたりの要求数など、一定期間にわたるデータ測定値を表します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="6c10e-186">メトリック データを使用すると、アプリの状態を高レベルで監視できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="6c10e-187">.NET gRPC メトリックは `EventCounter` を使用して出力されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="6c10e-188">gRPC サービスのメトリック</span><span class="sxs-lookup"><span data-stu-id="6c10e-188">gRPC service metrics</span></span>

<span data-ttu-id="6c10e-189">gRPC サーバーのメトリックは、`Grpc.AspNetCore.Server` イベント ソースで報告されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="6c10e-190">Name</span><span class="sxs-lookup"><span data-stu-id="6c10e-190">Name</span></span>                      | <span data-ttu-id="6c10e-191">説明</span><span class="sxs-lookup"><span data-stu-id="6c10e-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="6c10e-192">合計呼び出し数</span><span class="sxs-lookup"><span data-stu-id="6c10e-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="6c10e-193">現在の呼び出し数</span><span class="sxs-lookup"><span data-stu-id="6c10e-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="6c10e-194">失敗した合計呼び出し数</span><span class="sxs-lookup"><span data-stu-id="6c10e-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="6c10e-195">合計呼び出し数の限度を超えました</span><span class="sxs-lookup"><span data-stu-id="6c10e-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="6c10e-196">送信メッセージの総数</span><span class="sxs-lookup"><span data-stu-id="6c10e-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="6c10e-197">受信メッセージの総数</span><span class="sxs-lookup"><span data-stu-id="6c10e-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="6c10e-198">実装されていない呼び出しの総数</span><span class="sxs-lookup"><span data-stu-id="6c10e-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="6c10e-199">ASP.NET Core では、`Microsoft.AspNetCore.Hosting` イベント ソースに関する固有のメトリックも提供されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="6c10e-200">gRPC クライアントのメトリック</span><span class="sxs-lookup"><span data-stu-id="6c10e-200">gRPC client metrics</span></span>

<span data-ttu-id="6c10e-201">gRPC クライアントのメトリックは、`Grpc.Net.Client` イベント ソースで報告されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="6c10e-202">Name</span><span class="sxs-lookup"><span data-stu-id="6c10e-202">Name</span></span>                      | <span data-ttu-id="6c10e-203">説明</span><span class="sxs-lookup"><span data-stu-id="6c10e-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="6c10e-204">合計呼び出し数</span><span class="sxs-lookup"><span data-stu-id="6c10e-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="6c10e-205">現在の呼び出し数</span><span class="sxs-lookup"><span data-stu-id="6c10e-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="6c10e-206">失敗した合計呼び出し数</span><span class="sxs-lookup"><span data-stu-id="6c10e-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="6c10e-207">合計呼び出し数の限度を超えました</span><span class="sxs-lookup"><span data-stu-id="6c10e-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="6c10e-208">送信メッセージの総数</span><span class="sxs-lookup"><span data-stu-id="6c10e-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="6c10e-209">受信メッセージの総数</span><span class="sxs-lookup"><span data-stu-id="6c10e-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="6c10e-210">メトリックを観察する</span><span class="sxs-lookup"><span data-stu-id="6c10e-210">Observe metrics</span></span>

<span data-ttu-id="6c10e-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) は、アドホックな正常性監視と最初のレベルのパフォーマンス調査を目的としたパフォーマンス監視ツールです。</span><span class="sxs-lookup"><span data-stu-id="6c10e-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="6c10e-212">プロバイダー名として `Grpc.AspNetCore.Server` または `Grpc.Net.Client` のいずれかを使用して .NET アプリを監視します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="6c10e-213">gRPC メトリックを観察する別の方法は、Application Insights の [Microsoft ApplicationInsights.EventCounterCollector パッケージ](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)を使用してカウンター データをキャプチャすることです。</span><span class="sxs-lookup"><span data-stu-id="6c10e-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="6c10e-214">セットアップすると、Application Insights で、実行時に一般的な .NET カウンターが収集されます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="6c10e-215">gRPC のカウンターは既定では収集されませんが、[追加のカウンターを含めるように App Insights をカスタマイズ](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)できます。</span><span class="sxs-lookup"><span data-stu-id="6c10e-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="6c10e-216">*Startup.cs* で収集するように、Application insights の gRPC カウンターを指定します。</span><span class="sxs-lookup"><span data-stu-id="6c10e-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6c10e-217">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6c10e-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
