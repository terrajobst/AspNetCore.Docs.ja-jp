---
title: .NET クライアントを使用して gRPC サービスを呼び出す
author: jamesnk
description: .NET gRPC クライアントを使用して gRPC サービスを呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 1e7887388a752fb35d00e65db210c3924c6ab192
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829102"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="d4a28-103">.NET クライアントを使用して gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="d4a28-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="d4a28-104">.NET gRPC クライアントライブラリは、 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)NuGet パッケージで入手できます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="d4a28-105">このドキュメントでは、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-105">This document explains how to:</span></span>

* <span data-ttu-id="d4a28-106">Grpc サービスを呼び出すように gRPC クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="d4a28-107">GRPC に対して、単項、サーバーストリーミング、クライアントストリーミング、および双方向のストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="d4a28-108">GRPC クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="d4a28-108">Configure gRPC client</span></span>

<span data-ttu-id="d4a28-109">gRPC クライアントは、[ *\*.proto* ファイルから生成される](xref:grpc/basics#generated-c-assets)具象的なクライアントの種類です。</span><span class="sxs-lookup"><span data-stu-id="d4a28-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="d4a28-110">具象 gRPC クライアントには、 *\*.proto* ファイル内の gRPC サービスに変換するためのメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="d4a28-111">GRPC クライアントは、チャネルから作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="d4a28-112">まず、`GrpcChannel.ForAddress` を使用してチャネルを作成し、次にチャネルを使用して gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="d4a28-113">チャネルは、gRPC サービスへの長期的な接続を表します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="d4a28-114">チャネルが作成されると、サービスの呼び出しに関連するオプションが構成されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="d4a28-115">たとえば、呼び出しを行うために使用される `HttpClient`、送信メッセージと受信メッセージの最大サイズ、およびログ記録を `GrpcChannelOptions` で指定し、`GrpcChannel.ForAddress`と共に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="d4a28-116">オプションの完全な一覧については、「[クライアント構成オプション](xref:grpc/configuration#configure-client-options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4a28-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="d4a28-117">チャネルとクライアントのパフォーマンスと使用状況:</span><span class="sxs-lookup"><span data-stu-id="d4a28-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="d4a28-118">チャネルの作成は、コストのかかる操作になることがあります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="d4a28-119">GRPC 呼び出しにチャネルを再利用すると、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="d4a28-120">gRPC クライアントは、チャネルを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="d4a28-121">gRPC クライアントはライトウェイトオブジェクトであり、キャッシュまたは再利用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4a28-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="d4a28-122">複数の gRPC クライアントは、さまざまな種類のクライアントを含むチャネルから作成できます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="d4a28-123">チャネルとチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="d4a28-124">チャネルから作成されたクライアントは、複数の同時呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="d4a28-125">gRPC クライアントを作成するためのオプションは `GrpcChannel.ForAddress` だけではありません。</span><span class="sxs-lookup"><span data-stu-id="d4a28-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="d4a28-126">ASP.NET Core アプリから gRPC サービスを呼び出している場合は、 [grpc クライアントファクトリの統合](xref:grpc/clientfactory)を検討してください。</span><span class="sxs-lookup"><span data-stu-id="d4a28-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="d4a28-127">gRPC と `HttpClientFactory` の統合には、gRPC クライアントを作成するための一元化された代替手段が用意されています。</span><span class="sxs-lookup"><span data-stu-id="d4a28-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="d4a28-128">[.Net クライアントでセキュリティで保護されていない gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)には、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="d4a28-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="d4a28-129">GRPC 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="d4a28-129">Make gRPC calls</span></span>

<span data-ttu-id="d4a28-130">GRPC 呼び出しは、クライアントでメソッドを呼び出すことによって開始されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-130">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="d4a28-131">GRPC クライアントは、メッセージのシリアル化を処理し、適切なサービスに対する gRPC 呼び出しに対処します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-131">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="d4a28-132">gRPC には、さまざまな種類のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-132">gRPC has different types of methods.</span></span> <span data-ttu-id="d4a28-133">クライアントを使用して gRPC 呼び出しを行う方法は、呼び出すメソッドの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-133">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="d4a28-134">GRPC のメソッドの種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d4a28-134">The gRPC method types are:</span></span>

* <span data-ttu-id="d4a28-135">単項</span><span class="sxs-lookup"><span data-stu-id="d4a28-135">Unary</span></span>
* <span data-ttu-id="d4a28-136">サーバーストリーミング</span><span class="sxs-lookup"><span data-stu-id="d4a28-136">Server streaming</span></span>
* <span data-ttu-id="d4a28-137">クライアントストリーミング</span><span class="sxs-lookup"><span data-stu-id="d4a28-137">Client streaming</span></span>
* <span data-ttu-id="d4a28-138">双方向ストリーミング</span><span class="sxs-lookup"><span data-stu-id="d4a28-138">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="d4a28-139">単項呼び出し</span><span class="sxs-lookup"><span data-stu-id="d4a28-139">Unary call</span></span>

<span data-ttu-id="d4a28-140">単項呼び出しは、クライアントからの要求メッセージの送信を開始します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-140">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="d4a28-141">サービスの終了時に応答メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-141">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="d4a28-142">*\*プロトコル*ファイルの各単項サービスメソッドは、メソッドを呼び出すための具象 grpc クライアント型に、非同期メソッドとブロッキングメソッドの2つの .net メソッドを生成します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-142">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="d4a28-143">たとえば、`GreeterClient` で `SayHello`を呼び出すには、次の2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-143">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="d4a28-144">`GreeterClient.SayHelloAsync`-`Greeter.SayHello` サービスを非同期に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-144">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="d4a28-145">待機することができます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-145">Can be awaited.</span></span>
* <span data-ttu-id="d4a28-146">`GreeterClient.SayHello`-`Greeter.SayHello` サービスを呼び出し、完了するまでブロックします。</span><span class="sxs-lookup"><span data-stu-id="d4a28-146">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="d4a28-147">非同期コードでは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d4a28-147">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="d4a28-148">サーバーストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="d4a28-148">Server streaming call</span></span>

<span data-ttu-id="d4a28-149">サーバーストリーミング呼び出しは、クライアントから要求メッセージを送信することで開始されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-149">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="d4a28-150">`ResponseStream.MoveNext()` は、サービスからストリーミングされたメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-150">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="d4a28-151">`ResponseStream.MoveNext()` が `false`を返すと、サーバーストリーミングの呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-151">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

<span data-ttu-id="d4a28-152">8以降を使用C#している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-152">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="d4a28-153">`IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、応答ストリームからすべてのメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-153">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="d4a28-154">クライアントストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="d4a28-154">Client streaming call</span></span>

<span data-ttu-id="d4a28-155">クライアントのストリーミング呼び出しは、クライアントがメッセージを送信する*ことなく*開始されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-155">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="d4a28-156">クライアントは、`RequestStream.WriteAsync`を使用してメッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="d4a28-157">クライアントがメッセージの送信を完了したら `RequestStream.CompleteAsync` を呼び出してサービスに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-157">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="d4a28-158">サービスが応答メッセージを返すと、呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-158">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="d4a28-159">双方向ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="d4a28-159">Bi-directional streaming call</span></span>

<span data-ttu-id="d4a28-160">双方向のストリーミング呼び出しは、クライアントがメッセージを送信する*ことなく*開始されます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-160">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="d4a28-161">クライアントは、`RequestStream.WriteAsync`を使用してメッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-161">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="d4a28-162">サービスからストリーミングされたメッセージには、`ResponseStream.MoveNext()` または `ResponseStream.ReadAllAsync()`でアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-162">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="d4a28-163">双方向ストリーミング呼び出しは、`ResponseStream` にメッセージがなくなったときに完了します。</span><span class="sxs-lookup"><span data-stu-id="d4a28-163">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

<span data-ttu-id="d4a28-164">双方向のストリーミング呼び出しでは、クライアントとサービスはいつでもメッセージを相互に送信できます。</span><span class="sxs-lookup"><span data-stu-id="d4a28-164">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="d4a28-165">双方向呼び出しとの対話に最適なクライアントロジックは、サービスロジックによって異なります。</span><span class="sxs-lookup"><span data-stu-id="d4a28-165">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4a28-166">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d4a28-166">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
