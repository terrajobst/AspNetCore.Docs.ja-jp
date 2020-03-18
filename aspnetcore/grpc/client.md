---
title: .NET クライアントを使用して gRPC サービスを呼び出す
author: jamesnk
description: .NET gRPC クライアントを使用して gRPC サービスを呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650804"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="4805a-103">.NET クライアントを使用して gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="4805a-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="4805a-104">.NET gRPC クライアント ライブラリは、[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet パッケージで提供されています。</span><span class="sxs-lookup"><span data-stu-id="4805a-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="4805a-105">このドキュメントでは、以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4805a-105">This document explains how to:</span></span>

* <span data-ttu-id="4805a-106">gRPC サービスを呼び出すように gRPC クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="4805a-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="4805a-107">unary、サーバー ストリーミング、クライアント ストリーミング、双方向ストリーミングの各メソッドに対する gRPC 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="4805a-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="4805a-108">gRPC クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="4805a-108">Configure gRPC client</span></span>

<span data-ttu-id="4805a-109">gRPC クライアントは、[ *\*.proto* ファイルから生成される](xref:grpc/basics#generated-c-assets)具象的なクライアントの種類です。</span><span class="sxs-lookup"><span data-stu-id="4805a-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="4805a-110">具象 gRPC クライアントには、 *\*.proto* ファイル内の gRPC サービスに変換するためのメソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4805a-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="4805a-111">gRPC クライアントはチャネルから作成されます。</span><span class="sxs-lookup"><span data-stu-id="4805a-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="4805a-112">まず `GrpcChannel.ForAddress` を使用してチャネルを作成し、次にそのチャネルを使用して、gRPC クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="4805a-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="4805a-113">チャネルは、gRPC サービスへの有効期限が長い接続を表します。</span><span class="sxs-lookup"><span data-stu-id="4805a-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="4805a-114">チャネルが作成されるときには、サービスの呼び出しに関連するオプションによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="4805a-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="4805a-115">たとえば呼び出しを行うために使用される `HttpClient` の場合、`GrpcChannelOptions` で送受信メッセージの最大サイズとログ記録を指定し、`GrpcChannel.ForAddress` と共に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="4805a-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="4805a-116">オプションの完全な一覧については、[クライアント構成のオプション](xref:grpc/configuration#configure-client-options)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4805a-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="4805a-117">チャネルおよびクライアントのパフォーマンスと使用状況:</span><span class="sxs-lookup"><span data-stu-id="4805a-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="4805a-118">チャネルの作成は、コストのかかる操作となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4805a-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="4805a-119">チャネルを gRPC 呼び出しのために再利用すると、パフォーマンス上のメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="4805a-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="4805a-120">gRPC クライアントはチャネルを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="4805a-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="4805a-121">gRPC クライアントは軽量なオブジェクトであり、キャッシュしたり再利用したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4805a-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="4805a-122">異なる種類のクライアントを含め、1 つチャネルから複数の gRPC クライアントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="4805a-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="4805a-123">チャネルおよびそのチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4805a-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="4805a-124">チャネルから作成されたクライアントは、複数の同時呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="4805a-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="4805a-125">`GrpcChannel.ForAddress` は、gRPC クライアントを作成する際の唯一のオプションではありません。</span><span class="sxs-lookup"><span data-stu-id="4805a-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="4805a-126">ASP.NET Core アプリから gRPC サービスを呼び出そうとしている場合は、[gRPC クライアント ファクトリの統合](xref:grpc/clientfactory)を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4805a-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="4805a-127">gRPC と `HttpClientFactory` の統合により、gRPC クライアントを一元的に作成する別の方法が得られます。</span><span class="sxs-lookup"><span data-stu-id="4805a-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="4805a-128">[.NET クライアントで安全でない gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)には、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="4805a-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="4805a-129">現在のところ、`Grpc.Net.Client` による HTTP/2 を介した gRPC の呼び出しは、Xamarin ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4805a-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="4805a-130">将来の Xamarin のリリースで HTTP/2 のサポートを向上させるために作業を進めています。</span><span class="sxs-lookup"><span data-stu-id="4805a-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="4805a-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) と [gRPC-Web](xref:grpc/browser) は、現在でも機能する実行可能な代替手段です。</span><span class="sxs-lookup"><span data-stu-id="4805a-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="4805a-132">gRPC 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="4805a-132">Make gRPC calls</span></span>

<span data-ttu-id="4805a-133">gRPC 呼び出しは、クライアントでメソッドを呼び出すことで開始されます。</span><span class="sxs-lookup"><span data-stu-id="4805a-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="4805a-134">gRPC クライアントは、メッセージのシリアル化と、gRPC 呼び出しの正しいサービスへのアドレス指定を処理します。</span><span class="sxs-lookup"><span data-stu-id="4805a-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="4805a-135">gRPC には、さまざまな種類のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="4805a-135">gRPC has different types of methods.</span></span> <span data-ttu-id="4805a-136">クライアントを使用して gRPC 呼び出しを行う方法は、呼び出そうとしているメソッドの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="4805a-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="4805a-137">gRPC のメソッドの種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4805a-137">The gRPC method types are:</span></span>

* <span data-ttu-id="4805a-138">単項</span><span class="sxs-lookup"><span data-stu-id="4805a-138">Unary</span></span>
* <span data-ttu-id="4805a-139">サーバー ストリーミング。</span><span class="sxs-lookup"><span data-stu-id="4805a-139">Server streaming</span></span>
* <span data-ttu-id="4805a-140">クライアント ストリーミング</span><span class="sxs-lookup"><span data-stu-id="4805a-140">Client streaming</span></span>
* <span data-ttu-id="4805a-141">双方向ストリーミング</span><span class="sxs-lookup"><span data-stu-id="4805a-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="4805a-142">Unary 呼び出し</span><span class="sxs-lookup"><span data-stu-id="4805a-142">Unary call</span></span>

<span data-ttu-id="4805a-143">Unary 呼び出しは、要求メッセージを送信するクライアントで始まります。</span><span class="sxs-lookup"><span data-stu-id="4805a-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="4805a-144">サービスが終了すると応答メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="4805a-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="4805a-145">*\*.proto* ファイル内の各 Unary サービス メソッドは、メソッドを呼び出すための具象 gRPC クライアント型に関する 2 つの .NET メソッドとなります。非同期のメソッドとブロックするメソッドです。</span><span class="sxs-lookup"><span data-stu-id="4805a-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="4805a-146">たとえば `GreeterClient` に関しては、`SayHello` を呼び出す方法は 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="4805a-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="4805a-147">`GreeterClient.SayHelloAsync` - `Greeter.SayHello` サービスを非同期に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4805a-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="4805a-148">待機可能です。</span><span class="sxs-lookup"><span data-stu-id="4805a-148">Can be awaited.</span></span>
* <span data-ttu-id="4805a-149">`GreeterClient.SayHello` - `Greeter.SayHello` サービスを呼び出して、完了するまでブロックします。</span><span class="sxs-lookup"><span data-stu-id="4805a-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="4805a-150">非同期のコードでは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4805a-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="4805a-151">サーバー ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="4805a-151">Server streaming call</span></span>

<span data-ttu-id="4805a-152">サーバー ストリーミング呼び出しは、要求メッセージを送信するクライアントで始まります。</span><span class="sxs-lookup"><span data-stu-id="4805a-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="4805a-153">`ResponseStream.MoveNext()` は、サービスからストリーミングされたメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4805a-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="4805a-154">`ResponseStream.MoveNext()` が `false` を返すとサーバー ストリーミングの呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="4805a-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="4805a-155">C# 8 以降を使用している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4805a-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="4805a-156">`IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、応答ストリームからすべてのメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4805a-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="4805a-157">クライアント ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="4805a-157">Client streaming call</span></span>

<span data-ttu-id="4805a-158">クライアント ストリーミング呼び出しは、メッセージを送信するクライアント*なしで*始まります。</span><span class="sxs-lookup"><span data-stu-id="4805a-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="4805a-159">クライアントでは、`RequestStream.WriteAsync` を使用して、メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="4805a-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="4805a-160">クライアントがメッセージの送信を完了したら、`RequestStream.CompleteAsync` を呼び出してサービスに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4805a-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="4805a-161">サービスが応答メッセージを返すと呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="4805a-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="4805a-162">双方向ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="4805a-162">Bi-directional streaming call</span></span>

<span data-ttu-id="4805a-163">双方向ストリーミング呼び出しは、メッセージを送信するクライアント*なしで*始まります。</span><span class="sxs-lookup"><span data-stu-id="4805a-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="4805a-164">クライアントでは、`RequestStream.WriteAsync` を使用して、メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="4805a-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="4805a-165">サービスからストリーミングされたメッセージには、`ResponseStream.MoveNext()` または `ResponseStream.ReadAllAsync()` を使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="4805a-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="4805a-166">双方向ストリーミング呼び出しは、`ResponseStream` にメッセージがなくなると完了します。</span><span class="sxs-lookup"><span data-stu-id="4805a-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="4805a-167">双方向ストリーミング呼び出しの間、クライアントとサービスはいつでも相互にメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="4805a-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="4805a-168">双方向呼び出しの操作に最適なクライアント ロジックは、サービス ロジックによってさまざまです。</span><span class="sxs-lookup"><span data-stu-id="4805a-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4805a-169">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4805a-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
