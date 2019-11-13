---
title: .NET Core での gRPC クライアントファクトリの統合
author: jamesnk
description: クライアントファクトリを使用して gRPC クライアントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963679"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="25ae0-103">.NET Core での gRPC クライアントファクトリの統合</span><span class="sxs-lookup"><span data-stu-id="25ae0-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="25ae0-104">gRPC と `HttpClientFactory` の統合には、gRPC クライアントを一元的に作成する方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="25ae0-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="25ae0-105">[スタンドアロンの gRPC クライアントインスタンスを構成](xref:grpc/client)する代わりに使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="25ae0-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="25ae0-106">ファクトリ統合は、 [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet パッケージで使用できます。</span><span class="sxs-lookup"><span data-stu-id="25ae0-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="25ae0-107">ファクトリには、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="25ae0-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="25ae0-108">論理 gRPC クライアントインスタンスを構成するための一元的な場所を提供します</span><span class="sxs-lookup"><span data-stu-id="25ae0-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="25ae0-109">基になる `HttpClientMessageHandler` の有効期間を管理します</span><span class="sxs-lookup"><span data-stu-id="25ae0-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="25ae0-110">ASP.NET Core gRPC サービスでの期限とキャンセルの自動伝達</span><span class="sxs-lookup"><span data-stu-id="25ae0-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="25ae0-111">GRPC クライアントの登録</span><span class="sxs-lookup"><span data-stu-id="25ae0-111">Register gRPC clients</span></span>

<span data-ttu-id="25ae0-112">GRPC クライアントを登録するには、`Startup.ConfigureServices`内で汎用 `AddGrpcClient` 拡張メソッドを使用し、gRPC で型指定されたクライアントクラスとサービスアドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="25ae0-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="25ae0-113">GRPC クライアントの種類は、依存関係の注入 (DI) と共に一時的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="25ae0-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="25ae0-114">これで、クライアントを DI によって作成された型に直接挿入して使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="25ae0-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="25ae0-115">ASP.NET Core MVC コントローラー、SignalR ハブ、および gRPC サービスは、gRPC クライアントが自動的に挿入される場所です。</span><span class="sxs-lookup"><span data-stu-id="25ae0-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="25ae0-116">HttpClient の構成</span><span class="sxs-lookup"><span data-stu-id="25ae0-116">Configure HttpClient</span></span>

<span data-ttu-id="25ae0-117">`HttpClientFactory` は、gRPC クライアントによって使用される `HttpClient` を作成します。</span><span class="sxs-lookup"><span data-stu-id="25ae0-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="25ae0-118">標準 `HttpClientFactory` メソッドを使用して、発信要求ミドルウェアを追加したり、`HttpClient`の基になる `HttpClientHandler` を構成したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="25ae0-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="25ae0-119">詳細については、「 [IHttpClientFactory を使用して HTTP 要求を作成する](xref:fundamentals/http-requests)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25ae0-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="25ae0-120">チャネルとインターセプターの構成</span><span class="sxs-lookup"><span data-stu-id="25ae0-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="25ae0-121">gRPC 固有のメソッドは、次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="25ae0-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="25ae0-122">GRPC クライアントの基になるチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="25ae0-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="25ae0-123">GRPC 呼び出しを行うときにクライアントが使用する `Interceptor` インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="25ae0-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="25ae0-124">期限と取り消しの反映</span><span class="sxs-lookup"><span data-stu-id="25ae0-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="25ae0-125">gRPC サービスでファクトリによって作成された gRPC クライアントは、期限とキャンセルトークンを子呼び出しに自動的に反映するように `EnableCallContextPropagation()` を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="25ae0-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="25ae0-126">`EnableCallContextPropagation()` の拡張メソッドは、 [AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet パッケージで使用できますが、</span><span class="sxs-lookup"><span data-stu-id="25ae0-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="25ae0-127">呼び出しコンテキストの伝達は、現在の gRPC 要求コンテキストから期限とキャンセルトークンを読み取り、gRPC クライアントによって行われた発信呼び出しに自動的に伝達することによって機能します。</span><span class="sxs-lookup"><span data-stu-id="25ae0-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="25ae0-128">呼び出しコンテキストの伝達は、複雑で入れ子になった gRPC のシナリオが常に期限とキャンセルを伝達することを保証する優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="25ae0-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="25ae0-129">期限と RPC のキャンセルの詳細については、「 [rpc ライフサイクル](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25ae0-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25ae0-130">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="25ae0-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
