---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC サービスを作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/28/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 128f5b36eac9112460c33693db5537134a077476
ms.sourcegitcommit: 23f79bd71d49c4efddb56377c1f553cc993d781b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70130704"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="9de98-103">ASP.NET Core を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="9de98-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="9de98-104">このドキュメントでは、ASP.NET Core を使用して gRPC サービスを開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9de98-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9de98-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9de98-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9de98-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9de98-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9de98-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9de98-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9de98-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9de98-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="9de98-109">ASP.NET Core の gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="9de98-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="9de98-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9de98-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9de98-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9de98-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9de98-112">GRPC プロジェクトを作成する方法の詳細については、「 [grpc サービスの概要](xref:tutorials/grpc/grpc-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9de98-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9de98-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9de98-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9de98-114">コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9de98-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="9de98-115">ASP.NET Core アプリに gRPC サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="9de98-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="9de98-116">gRPC には[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="9de98-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="9de98-117">GRPC の構成</span><span class="sxs-lookup"><span data-stu-id="9de98-117">Configure gRPC</span></span>

<span data-ttu-id="9de98-118">*Startup.cs* の場合:</span><span class="sxs-lookup"><span data-stu-id="9de98-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="9de98-119">grpc は、 `AddGrpc`メソッドを使用して有効になっています。</span><span class="sxs-lookup"><span data-stu-id="9de98-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="9de98-120">各 grpc サービスは、メソッドを`MapGrpcService`使用してルーティングパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9de98-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="9de98-121">ASP.NET Core ミドルウェアと features はルーティングパイプラインを共有するため、追加の要求ハンドラーを提供するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9de98-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="9de98-122">MVC コントローラーなどの追加の要求ハンドラーは、構成されている gRPC サービスと並行して動作します。</span><span class="sxs-lookup"><span data-stu-id="9de98-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="9de98-123">Kestrel の構成</span><span class="sxs-lookup"><span data-stu-id="9de98-123">Configure Kestrel</span></span>

<span data-ttu-id="9de98-124">Kestrel gRPC エンドポイント:</span><span class="sxs-lookup"><span data-stu-id="9de98-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="9de98-125">HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="9de98-125">Require HTTP/2.</span></span>
* <span data-ttu-id="9de98-126">HTTPS で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9de98-126">Should be secured with HTTPS.</span></span>

#### <a name="http2"></a><span data-ttu-id="9de98-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9de98-127">HTTP/2</span></span>

<span data-ttu-id="9de98-128">Kestrel は、最新のオペレーティングシステムで[HTTP/2 をサポート](xref:fundamentals/servers/kestrel#http2-support)しています。</span><span class="sxs-lookup"><span data-stu-id="9de98-128">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="9de98-129">Kestrel エンドポイントは、既定で HTTP/1.1 接続と HTTP/2 接続をサポートするように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9de98-129">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

> [!NOTE]
> <span data-ttu-id="9de98-130">macOS では、[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用した ASP.NET Core grpc はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9de98-130">macOS doesn't support ASP.NET Core gRPC with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9de98-131">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="9de98-131">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9de98-132">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9de98-132">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

#### <a name="https"></a><span data-ttu-id="9de98-133">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9de98-133">HTTPS</span></span>

<span data-ttu-id="9de98-134">GRPC に使用される kestrel エンドポイントは、HTTPS で保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9de98-134">Kestrel endpoints used for gRPC should be secured with HTTPS.</span></span> <span data-ttu-id="9de98-135">開発時には、ASP.NET Core 開発証明書が`https://localhost:5001`存在するときに、HTTPS エンドポイントがに自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="9de98-135">In development, an HTTPS endpoint is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="9de98-136">構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9de98-136">No configuration is required.</span></span>

<span data-ttu-id="9de98-137">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9de98-137">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9de98-138">次の*appsettings*の例では、HTTPS で保護された HTTP/2 エンドポイントが提供されています。</span><span class="sxs-lookup"><span data-stu-id="9de98-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with HTTPS is provided:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="9de98-139">または、 *Program.cs*で Kestrel endspoints を構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="9de98-139">Alternatively, Kestrel endspoints can be configured in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9de98-140">Kestrel での HTTP/2 および HTTPS の有効化の詳細については、「 [kestrel エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9de98-140">For more information on enabling HTTP/2 and HTTPS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="9de98-141">ASP.NET Core Api との統合</span><span class="sxs-lookup"><span data-stu-id="9de98-141">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="9de98-142">gRPC サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) や[ログ記録](xref:fundamentals/logging/index)などの ASP.NET Core 機能へのフルアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="9de98-142">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9de98-143">たとえば、サービス実装は、コンストラクターを使用して DI コンテナーから logger サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="9de98-143">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="9de98-144">既定では、gRPC サービス実装は、任意の有効期間 (シングルトン、スコープ、または一時的) を持つ他の DI サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="9de98-144">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="9de98-145">GRPC メソッドでの HttpContext の解決</span><span class="sxs-lookup"><span data-stu-id="9de98-145">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="9de98-146">GRPC API は、メソッド、ホスト、ヘッダー、トレーラーなど、一部の HTTP/2 メッセージデータへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9de98-146">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="9de98-147">アクセスは、各`ServerCallContext` grpc メソッドに渡される引数を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="9de98-147">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="9de98-148">`ServerCallContext`では、すべての ASP.NET `HttpContext` api でへのフルアクセスが提供されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="9de98-148">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="9de98-149">拡張`GetHttpContext`メソッドは、ASP.NET api の基`HttpContext`になる HTTP/2 メッセージを表すへのフルアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9de98-149">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="9de98-150">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9de98-150">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
