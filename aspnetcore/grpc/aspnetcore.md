---
title: ASP.NET Core を使用した gRPC サービス
author: juntaoluo
description: ASP.NET Core で gRPC サービスを作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 26f0d7610151460967b97665ed61deab1ef56d68
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862937"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="94d6d-103">ASP.NET Core を使用した gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="94d6d-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="94d6d-104">このドキュメントでは、ASP.NET Core を使用して gRPC サービスを開始する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="94d6d-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="94d6d-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="94d6d-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94d6d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d6d-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94d6d-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d6d-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94d6d-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d6d-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="94d6d-109">ASP.NET Core の gRPC サービスの概要</span><span class="sxs-lookup"><span data-stu-id="94d6d-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="94d6d-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="94d6d-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94d6d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d6d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94d6d-112">GRPC プロジェクトを作成する方法の詳細については、「 [grpc サービスの概要](xref:tutorials/grpc/grpc-start)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94d6d-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="94d6d-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d6d-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="94d6d-114">コマンド ラインから `dotnet new grpc -o GrpcGreeter` を実行します。</span><span class="sxs-lookup"><span data-stu-id="94d6d-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="94d6d-115">ASP.NET Core アプリに gRPC サービスを追加する</span><span class="sxs-lookup"><span data-stu-id="94d6d-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="94d6d-116">gRPC には[AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore)パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="94d6d-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="94d6d-117">GRPC の構成</span><span class="sxs-lookup"><span data-stu-id="94d6d-117">Configure gRPC</span></span>

<span data-ttu-id="94d6d-118">grpc は、 `AddGrpc`次の方法で有効になります。</span><span class="sxs-lookup"><span data-stu-id="94d6d-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="94d6d-119">各 grpc サービスは、メソッドを`MapGrpcService`使用してルーティングパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="94d6d-120">ASP.NET Core ミドルウェアと features はルーティングパイプラインを共有するため、追加の要求ハンドラーを提供するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="94d6d-121">MVC コントローラーなどの追加の要求ハンドラーは、構成されている gRPC サービスと並行して動作します。</span><span class="sxs-lookup"><span data-stu-id="94d6d-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="94d6d-122">ASP.NET Core Api との統合</span><span class="sxs-lookup"><span data-stu-id="94d6d-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="94d6d-123">gRPC サービスには、[依存関係の挿入](xref:fundamentals/dependency-injection)(DI) や[ログ記録](xref:fundamentals/logging/index)などの ASP.NET Core 機能へのフルアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="94d6d-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="94d6d-124">たとえば、サービス実装は、コンストラクターを使用して DI コンテナーから logger サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="94d6d-125">既定では、gRPC サービス実装は、任意の有効期間 (シングルトン、スコープ、または一時的) を持つ他の DI サービスを解決できます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="94d6d-126">GRPC メソッドでの HttpContext の解決</span><span class="sxs-lookup"><span data-stu-id="94d6d-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="94d6d-127">GRPC API は、メソッド、ホスト、ヘッダー、トレーラーなど、一部の HTTP/2 メッセージデータへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="94d6d-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="94d6d-128">アクセスは、各`ServerCallContext` grpc メソッドに渡される引数を使用して行われます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="94d6d-129">`ServerCallContext`では、すべての ASP.NET `HttpContext` api でへのフルアクセスが提供されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="94d6d-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="94d6d-130">拡張`GetHttpContext`メソッドは、ASP.NET api の基`HttpContext`になる HTTP/2 メッセージを表すへのフルアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="94d6d-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="grpc-and-aspnet-core-on-macos"></a><span data-ttu-id="94d6d-131">macOS の gRPC と ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="94d6d-131">gRPC and ASP.NET Core on macOS</span></span>

<span data-ttu-id="94d6d-132">Kestrel では、macOS で[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用した HTTP/2 はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="94d6d-132">Kestrel doesn't support HTTP/2 with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) on macOS.</span></span> <span data-ttu-id="94d6d-133">ASP.NET Core gRPC テンプレートとサンプルでは、既定で TLS が使用されます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="94d6d-134">GRPC サーバーを起動しようとすると、次のエラーメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="94d6d-135">IPv4 ループバックインターフェイスの https://localhost:5001 にバインドできません。' HTTP/2 over TLS は、ALPN サポートがないため、macOS ではサポートされていません。 '。</span><span class="sxs-lookup"><span data-stu-id="94d6d-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="94d6d-136">この問題を回避するには、TLS を使用**せず**に HTTP/2 を使用するように Kestrel と grpc クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="94d6d-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 **without** TLS.</span></span> <span data-ttu-id="94d6d-137">これは開発時にのみ実行してください。</span><span class="sxs-lookup"><span data-stu-id="94d6d-137">You should only do this during development.</span></span> <span data-ttu-id="94d6d-138">TLS を使用しないと、gRPC メッセージが暗号化なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="94d6d-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="94d6d-139">Kestrel では、TLS を使用せずに`Program.cs`HTTP/2 エンドポイントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94d6d-139">Kestrel must configure a HTTP/2 endpoint without TLS in `Program.cs`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="94d6d-140">Grpc クライアントでは、 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`スイッチをに`true`設定し、サーバーアドレスでを使用`http`する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94d6d-140">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="94d6d-141">TLS を使用しない HTTP/2 は、アプリの開発中にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="94d6d-141">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="94d6d-142">実稼働アプリケーションでは、常にトランスポートセキュリティを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="94d6d-142">Production applications should always use transport security.</span></span> <span data-ttu-id="94d6d-143">詳細については、「 [gRPC のセキュリティに関する考慮事項 ASP.NET Core](xref:grpc/security#transport-security)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="94d6d-143">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94d6d-144">その他の資料</span><span class="sxs-lookup"><span data-stu-id="94d6d-144">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
