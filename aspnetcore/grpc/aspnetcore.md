---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC サービスを作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 28e6b8589bbe0b6a3723b64736c723c883302571
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238162"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="9b452-103">ASP.NET Core を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="9b452-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="9b452-104">このドキュメントでは、ASP.NET Core を使用して gRPC サービスを開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b452-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b452-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9b452-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b452-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b452-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9b452-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b452-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9b452-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b452-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="9b452-109">ASP.NET Core の gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="9b452-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="9b452-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9b452-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9b452-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b452-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b452-112">GRPC プロジェクトを作成する方法の詳細については、「 [grpc サービスの概要](xref:tutorials/grpc/grpc-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b452-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9b452-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9b452-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9b452-114">コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9b452-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="9b452-115">ASP.NET Core アプリに gRPC サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="9b452-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="9b452-116">gRPC には[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="9b452-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="9b452-117">GRPC の構成</span><span class="sxs-lookup"><span data-stu-id="9b452-117">Configure gRPC</span></span>

<span data-ttu-id="9b452-118">*Startup.cs* の場合:</span><span class="sxs-lookup"><span data-stu-id="9b452-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="9b452-119">grpc は、 `AddGrpc`メソッドを使用して有効になっています。</span><span class="sxs-lookup"><span data-stu-id="9b452-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="9b452-120">各 grpc サービスは、メソッドを`MapGrpcService`使用してルーティングパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9b452-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="9b452-121">ASP.NET Core ミドルウェアと features はルーティングパイプラインを共有するため、追加の要求ハンドラーを提供するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9b452-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="9b452-122">MVC コントローラーなどの追加の要求ハンドラーは、構成されている gRPC サービスと並行して動作します。</span><span class="sxs-lookup"><span data-stu-id="9b452-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="9b452-123">Kestrel の構成</span><span class="sxs-lookup"><span data-stu-id="9b452-123">Configure Kestrel</span></span>

<span data-ttu-id="9b452-124">Kestrel gRPC エンドポイント:</span><span class="sxs-lookup"><span data-stu-id="9b452-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="9b452-125">HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="9b452-125">Require HTTP/2.</span></span>
* <span data-ttu-id="9b452-126">HTTPS で保護されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b452-126">Should be secured with HTTPS.</span></span>

#### <a name="http2"></a><span data-ttu-id="9b452-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9b452-127">HTTP/2</span></span>

<span data-ttu-id="9b452-128">gRPC には HTTP/2 が必要です。</span><span class="sxs-lookup"><span data-stu-id="9b452-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="9b452-129">gRPC for ASP.NET Core では、 [HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)が`HTTP/2`検証されます。</span><span class="sxs-lookup"><span data-stu-id="9b452-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="9b452-130">Kestrel は、最新のオペレーティングシステムで[HTTP/2 をサポート](xref:fundamentals/servers/kestrel#http2-support)しています。</span><span class="sxs-lookup"><span data-stu-id="9b452-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="9b452-131">Kestrel エンドポイントは、既定で HTTP/1.1 接続と HTTP/2 接続をサポートするように構成されています。</span><span class="sxs-lookup"><span data-stu-id="9b452-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="https"></a><span data-ttu-id="9b452-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9b452-132">HTTPS</span></span>

<span data-ttu-id="9b452-133">GRPC に使用される kestrel エンドポイントは、HTTPS で保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b452-133">Kestrel endpoints used for gRPC should be secured with HTTPS.</span></span> <span data-ttu-id="9b452-134">開発時には、ASP.NET Core 開発証明書が`https://localhost:5001`存在するときに、HTTPS エンドポイントがに自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b452-134">In development, an HTTPS endpoint is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="9b452-135">構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9b452-135">No configuration is required.</span></span>

<span data-ttu-id="9b452-136">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b452-136">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9b452-137">次の*appsettings*の例では、HTTPS で保護された HTTP/2 エンドポイントが提供されています。</span><span class="sxs-lookup"><span data-stu-id="9b452-137">In the following *appsettings.json* example, an HTTP/2 endpoint secured with HTTPS is provided:</span></span>

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

<span data-ttu-id="9b452-138">または、 *Program.cs*で Kestrel エンドポイントを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="9b452-138">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

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

<span data-ttu-id="9b452-139">HTTP/2 エンドポイントが HTTPS を使用せずに構成されている場合、エンドポイントの`HttpProtocols.Http2` [listenoptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols)がに設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b452-139">When an HTTP/2 endpoint is configured without HTTPS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="9b452-140">`HttpProtocols.Http1AndHttp2`HTTP/2 のネゴシエートには HTTPS が必要であるため、を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="9b452-140">`HttpProtocols.Http1AndHttp2` can't be used because HTTPS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="9b452-141">HTTPS を使用しない場合、エンドポイントへのすべての接続は既定の HTTP/1.1 に設定され、gRPC の呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="9b452-141">Without HTTPS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="9b452-142">Kestrel での HTTP/2 および HTTPS の有効化の詳細については、「 [kestrel エンドポイントの構成](xref:fundamentals/servers/kestrel#endpoint-configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b452-142">For more information on enabling HTTP/2 and HTTPS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="9b452-143">macOS では、[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用した ASP.NET Core grpc はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="9b452-143">macOS doesn't support ASP.NET Core gRPC with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="9b452-144">macOS で gRPC サービスを正常に実行するには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="9b452-144">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9b452-145">詳細については、[macOS で ASP.NET Core gRPC アプリを起動できない](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)場合に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b452-145">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="9b452-146">ASP.NET Core Api との統合</span><span class="sxs-lookup"><span data-stu-id="9b452-146">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="9b452-147">gRPC サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) や[ログ記録](xref:fundamentals/logging/index)などの ASP.NET Core 機能へのフルアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="9b452-147">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9b452-148">たとえば、サービス実装は、コンストラクターを使用して DI コンテナーから logger サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="9b452-148">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="9b452-149">既定では、gRPC サービス実装は、任意の有効期間 (シングルトン、スコープ、または一時的) を持つ他の DI サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="9b452-149">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="9b452-150">GRPC メソッドでの HttpContext の解決</span><span class="sxs-lookup"><span data-stu-id="9b452-150">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="9b452-151">GRPC API は、メソッド、ホスト、ヘッダー、トレーラーなど、一部の HTTP/2 メッセージデータへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9b452-151">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="9b452-152">アクセスは、各`ServerCallContext` grpc メソッドに渡される引数を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="9b452-152">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="9b452-153">`ServerCallContext`では、すべての ASP.NET `HttpContext` api でへのフルアクセスが提供されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="9b452-153">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="9b452-154">拡張`GetHttpContext`メソッドは、ASP.NET api の基`HttpContext`になる HTTP/2 メッセージを表すへのフルアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9b452-154">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="9b452-155">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9b452-155">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
