---
title: .NET での gRPC でのログ記録と診断
author: jamesnk
description: .NET で gRPC アプリから診断を収集する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: ce6ad96d9e26c9cd3844093536745f8f9bea4a76
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204325"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="763ee-103">.NET での gRPC でのログ記録と診断</span><span class="sxs-lookup"><span data-stu-id="763ee-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="763ee-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="763ee-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="763ee-105">この記事では、問題のトラブルシューティングに役立つ gRPC アプリから診断情報を収集するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="763ee-105">This article provides guidance for gathering diagnostics from your gRPC app to help troubleshoot issues.</span></span>

## <a name="grpc-services-logging"></a><span data-ttu-id="763ee-106">gRPC サービスのログ記録</span><span class="sxs-lookup"><span data-stu-id="763ee-106">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="763ee-107">サーバー側のログには、アプリからの機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="763ee-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="763ee-108">運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="763ee-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="763ee-109">GRPC サービスは ASP.NET Core でホストされるため、ASP.NET Core ログシステムを使用します。</span><span class="sxs-lookup"><span data-stu-id="763ee-109">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="763ee-110">既定の構成では、gRPC はほとんど情報をログに記録しませんが、これは構成できます。</span><span class="sxs-lookup"><span data-stu-id="763ee-110">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="763ee-111">ASP.NET Core ログの構成の詳細については、 [ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="763ee-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="763ee-112">grpc は、カテゴリの`Grpc`下にログを追加します。</span><span class="sxs-lookup"><span data-stu-id="763ee-112">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="763ee-113">Grpc から詳細なログを有効にする`Grpc`には、 `Debug`の`LogLevel` `Logging`サブセクションに次の項目を追加して、 *appsettings*ファイルのレベルにプレフィックスを構成します。</span><span class="sxs-lookup"><span data-stu-id="763ee-113">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7)]

<span data-ttu-id="763ee-114">これは、 *Startup.cs*で次のよう`ConfigureLogging`に構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="763ee-114">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5)]

<span data-ttu-id="763ee-115">JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。</span><span class="sxs-lookup"><span data-stu-id="763ee-115">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="763ee-116">構成システムのドキュメントを参照して、入れ子になった構成値を指定する方法を確認してください。</span><span class="sxs-lookup"><span data-stu-id="763ee-116">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="763ee-117">たとえば、環境変数を使用する場合、 `_`の代わり`:`に2文字が使用されます`Logging__LogLevel__Grpc`(たとえば、)。</span><span class="sxs-lookup"><span data-stu-id="763ee-117">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="763ee-118">アプリのより詳細`Debug`な診断情報を収集する場合は、レベルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="763ee-118">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="763ee-119">レベル`Trace`は非常に低いレベルの診断を生成するため、アプリの問題を診断するためにはほとんど必要ありません。</span><span class="sxs-lookup"><span data-stu-id="763ee-119">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

### <a name="sample-logging-output"></a><span data-ttu-id="763ee-120">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="763ee-120">Sample logging output</span></span>

<span data-ttu-id="763ee-121">Grpc サービスの`Debug`レベルでのコンソール出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="763ee-121">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```
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

## <a name="access-server-side-logs"></a><span data-ttu-id="763ee-122">サーバー側のログへのアクセス</span><span class="sxs-lookup"><span data-stu-id="763ee-122">Access server-side logs</span></span>

<span data-ttu-id="763ee-123">サーバー側のログにアクセスする方法は、を実行している環境によって異なります。</span><span class="sxs-lookup"><span data-stu-id="763ee-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app"></a><span data-ttu-id="763ee-124">コンソールアプリとして</span><span class="sxs-lookup"><span data-stu-id="763ee-124">As a console app</span></span>

<span data-ttu-id="763ee-125">コンソールアプリでを実行している場合は、既定で[コンソール logger](xref:fundamentals/logging/index#console-provider)が有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="763ee-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="763ee-126">gRPC ログがコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="763ee-126">gRPC logs will appear in the console.</span></span>

### <a name="other-environments"></a><span data-ttu-id="763ee-127">その他の環境</span><span class="sxs-lookup"><span data-stu-id="763ee-127">Other environments</span></span>

<span data-ttu-id="763ee-128">アプリが別の環境 (Docker、Kubernetes、または Windows サービスなど) に配置されている<xref:fundamentals/logging/index>場合は、「」を参照して、環境に適したログプロバイダーを構成する方法の詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="763ee-128">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="grpc-client-logging"></a><span data-ttu-id="763ee-129">gRPC クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="763ee-129">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="763ee-130">クライアント側のログには、アプリからの機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="763ee-130">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="763ee-131">運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="763ee-131">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="763ee-132">.Net クライアントからログを取得するには、クライアントの`GrpcChannelOptions.LoggerFactory`チャネルの作成時にプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="763ee-132">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="763ee-133">ASP.NET Core アプリから gRPC サービスを呼び出す場合は、logger ファクトリを依存関係の挿入 (DI) から解決できます。</span><span class="sxs-lookup"><span data-stu-id="763ee-133">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="763ee-134">クライアントのログ記録を有効にする別の方法として、 [Grpc クライアントファクトリ](xref:grpc/clientfactory)を使用してクライアントを作成する方法があります。</span><span class="sxs-lookup"><span data-stu-id="763ee-134">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="763ee-135">クライアントファクトリに登録され、DI から解決される gRPC クライアントは、アプリの構成済みログを自動的に使用します。</span><span class="sxs-lookup"><span data-stu-id="763ee-135">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="763ee-136">アプリが DI を使用していない場合は、 `ILoggerFactory` [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)を使用して新しいインスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="763ee-136">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="763ee-137">このメソッドにアクセスするには、アプリに[Microsoft 拡張子のログ](https://www.nuget.org/packages/microsoft.extensions.logging/)パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="763ee-137">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a><span data-ttu-id="763ee-138">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="763ee-138">Sample logging output</span></span>

<span data-ttu-id="763ee-139">Grpc クライアントの`Debug`レベルでのコンソール出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="763ee-139">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a><span data-ttu-id="763ee-140">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="763ee-140">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
