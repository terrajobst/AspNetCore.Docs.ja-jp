---
title: .NET Core での gRPC クライアントファクトリの統合
author: jamesnk
description: クライアントファクトリを使用して gRPC クライアントを作成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: 5d719893e96ae017e2de0ee1744003d2d67a49c9
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773664"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="e9289-103">.NET Core での gRPC クライアントファクトリの統合</span><span class="sxs-lookup"><span data-stu-id="e9289-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="e9289-104">grpc との`HttpClientFactory`統合により、grpc クライアントを一元的に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="e9289-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="e9289-105">[スタンドアロンの gRPC クライアントインスタンスを構成](xref:grpc/client)する代わりに使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="e9289-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="e9289-106">ファクトリ統合は、 [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet パッケージで使用できます。</span><span class="sxs-lookup"><span data-stu-id="e9289-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="e9289-107">ファクトリには、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="e9289-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="e9289-108">論理 gRPC クライアントインスタンスを構成するための一元的な場所を提供します</span><span class="sxs-lookup"><span data-stu-id="e9289-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="e9289-109">基になるの有効期間を管理します`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="e9289-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="e9289-110">ASP.NET Core gRPC サービスでの期限とキャンセルの自動伝達</span><span class="sxs-lookup"><span data-stu-id="e9289-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="e9289-111">GRPC クライアントの登録</span><span class="sxs-lookup"><span data-stu-id="e9289-111">Register gRPC clients</span></span>

<span data-ttu-id="e9289-112">Grpc クライアントを登録するには、 `AddGrpcClient`内で`Startup.ConfigureServices`汎用拡張メソッドを使用し、grpc で型指定されたクライアントクラスとサービスアドレスを指定します。</span><span class="sxs-lookup"><span data-stu-id="e9289-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="e9289-113">GRPC クライアントの種類は、依存関係の注入 (DI) と共に一時的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="e9289-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="e9289-114">これで、クライアントを DI によって作成された型に直接挿入して使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e9289-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="e9289-115">ASP.NET Core MVC コントローラー、SignalR hub、および gRPC サービスは、gRPC クライアントが自動的に挿入される場所です。</span><span class="sxs-lookup"><span data-stu-id="e9289-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="e9289-116">HttpClient の構成</span><span class="sxs-lookup"><span data-stu-id="e9289-116">Configure HttpClient</span></span>

<span data-ttu-id="e9289-117">`HttpClientFactory`grpc `HttpClient`クライアントによって使用されるを作成します。</span><span class="sxs-lookup"><span data-stu-id="e9289-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="e9289-118">標準`HttpClientFactory`メソッドを使用して、発信要求ミドルウェアを追加したり、 `HttpClientHandler` `HttpClient`の基になるを構成したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="e9289-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="e9289-119">詳細については、「 [IHttpClientFactory を使用して HTTP 要求を作成する](xref:fundamentals/http-requests)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9289-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="e9289-120">チャネルとインターセプターの構成</span><span class="sxs-lookup"><span data-stu-id="e9289-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="e9289-121">gRPC 固有のメソッドは、次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="e9289-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="e9289-122">GRPC クライアントの基になるチャネルを構成します。</span><span class="sxs-lookup"><span data-stu-id="e9289-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="e9289-123">Grpc 呼び出しを行うときにクライアントが使用するインスタンスを追加`Interceptor`します。</span><span class="sxs-lookup"><span data-stu-id="e9289-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="e9289-124">期限と取り消しの反映</span><span class="sxs-lookup"><span data-stu-id="e9289-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="e9289-125">grpc サービスでファクトリによって作成された grpc クライアントは`EnableCallContextPropagation()` 、期限とキャンセルトークンを子呼び出しに自動的に反映するように、を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="e9289-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="e9289-126">拡張メソッドは、[Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet パッケージに含まれています。`EnableCallContextPropagation()`</span><span class="sxs-lookup"><span data-stu-id="e9289-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="e9289-127">呼び出しコンテキストの伝達は、現在の gRPC 要求コンテキストから期限とキャンセルトークンを読み取り、gRPC クライアントによって行われた発信呼び出しに自動的に伝達することによって機能します。</span><span class="sxs-lookup"><span data-stu-id="e9289-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="e9289-128">呼び出しコンテキストの伝達は、複雑で入れ子になった gRPC のシナリオが常に期限とキャンセルを伝達することを保証する優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="e9289-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="e9289-129">期限と RPC のキャンセルの詳細については、「 [rpc ライフサイクル](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9289-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9289-130">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e9289-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
