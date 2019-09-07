---
title: .NET クライアントを使用して gRPC サービスを呼び出す
author: jamesnk
description: .NET gRPC クライアントを使用して gRPC サービスを呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 27e4b3e7307ae49bacb01a46fbc1b55b6967c7a0
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773691"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="accda-103">.NET クライアントを使用して gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="accda-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="accda-104">.NET gRPC クライアントライブラリは、 [grpc .net. クライアント](https://www.nuget.org/packages/Grpc.Net.Client)NuGet パッケージで入手できます。</span><span class="sxs-lookup"><span data-stu-id="accda-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="accda-105">このドキュメントでは、次の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="accda-105">This document explains how to:</span></span>

* <span data-ttu-id="accda-106">Grpc サービスを呼び出すように gRPC クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="accda-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="accda-107">GRPC に対して、単項、サーバーストリーミング、クライアントストリーミング、および双方向のストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="accda-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="accda-108">GRPC クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="accda-108">Configure gRPC client</span></span>

<span data-ttu-id="accda-109">grpc クライアントは、 [  *\*プロトコル*ファイルから生成](xref:grpc/basics#generated-c-assets)される具象クライアントの種類です。</span><span class="sxs-lookup"><span data-stu-id="accda-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="accda-110">具象 grpc クライアントには、  *\*proto*ファイルの grpc サービスに変換するメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="accda-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="accda-111">GRPC クライアントは、チャネルから作成されます。</span><span class="sxs-lookup"><span data-stu-id="accda-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="accda-112">まずを使用`GrpcChannel.ForAddress`してチャネルを作成し、次にチャネルを使用して grpc クライアントを作成します。</span><span class="sxs-lookup"><span data-stu-id="accda-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="accda-113">チャネルは、gRPC サービスへの長期的な接続を表します。</span><span class="sxs-lookup"><span data-stu-id="accda-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="accda-114">チャネルが作成されると、サービスの呼び出しに関連するオプションを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="accda-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="accda-115">たとえば、呼び出しを`HttpClient`行うために使用される、送信メッセージと受信メッセージの最大サイズ、およびログ記録`GrpcChannelOptions`は、で`GrpcChannel.ForAddress`指定し、と共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="accda-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="accda-116">オプションの完全な一覧については、「[クライアント構成オプション](xref:grpc/configuration#configure-client-options)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="accda-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="accda-117">チャネルの作成は高価な操作であり、gRPC 呼び出しのチャネルを再利用すると、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="accda-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="accda-118">複数の具象 gRPC クライアントは、さまざまな種類のクライアントを含むチャネルから作成できます。</span><span class="sxs-lookup"><span data-stu-id="accda-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="accda-119">具象 gRPC クライアントの種類は軽量のオブジェクトであり、必要に応じて作成できます。</span><span class="sxs-lookup"><span data-stu-id="accda-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="accda-120">`GrpcChannel.ForAddress`gRPC クライアントを作成するための唯一のオプションではありません。</span><span class="sxs-lookup"><span data-stu-id="accda-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="accda-121">ASP.NET Core アプリから gRPC サービスを呼び出す場合は、 [grpc クライアントファクトリの統合](xref:grpc/clientfactory)を検討してください。</span><span class="sxs-lookup"><span data-stu-id="accda-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="accda-122">grpc との`HttpClientFactory`統合には、grpc クライアントを作成するための一元化された代替手段が用意されています。</span><span class="sxs-lookup"><span data-stu-id="accda-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="accda-123">GRPC 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="accda-123">Make gRPC calls</span></span>

<span data-ttu-id="accda-124">GRPC 呼び出しは、クライアントでメソッドを呼び出すことによって開始されます。</span><span class="sxs-lookup"><span data-stu-id="accda-124">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="accda-125">GRPC クライアントは、メッセージのシリアル化を処理し、適切なサービスに対する gRPC 呼び出しに対処します。</span><span class="sxs-lookup"><span data-stu-id="accda-125">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="accda-126">gRPC には、さまざまな種類のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="accda-126">gRPC has different types of methods.</span></span> <span data-ttu-id="accda-127">クライアントを使用して gRPC 呼び出しを行う方法は、呼び出すメソッドの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="accda-127">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="accda-128">GRPC のメソッドの種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="accda-128">The gRPC method types are:</span></span>

* <span data-ttu-id="accda-129">単項</span><span class="sxs-lookup"><span data-stu-id="accda-129">Unary</span></span>
* <span data-ttu-id="accda-130">サーバーストリーミング</span><span class="sxs-lookup"><span data-stu-id="accda-130">Server streaming</span></span>
* <span data-ttu-id="accda-131">クライアントストリーミング</span><span class="sxs-lookup"><span data-stu-id="accda-131">Client streaming</span></span>
* <span data-ttu-id="accda-132">双方向ストリーミング</span><span class="sxs-lookup"><span data-stu-id="accda-132">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="accda-133">単項呼び出し</span><span class="sxs-lookup"><span data-stu-id="accda-133">Unary call</span></span>

<span data-ttu-id="accda-134">単項呼び出しは、クライアントからの要求メッセージの送信を開始します。</span><span class="sxs-lookup"><span data-stu-id="accda-134">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="accda-135">サービスの終了時に応答メッセージが返されます。</span><span class="sxs-lookup"><span data-stu-id="accda-135">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="accda-136">*\*Proto*ファイルの各単項サービスメソッドは、メソッドを呼び出すための具象 grpc クライアント型に対して、非同期メソッドとブロッキングメソッドの2つの .net メソッドを生成します。</span><span class="sxs-lookup"><span data-stu-id="accda-136">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="accda-137">たとえば、を呼び出す`GreeterClient` `SayHello`には、次の2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="accda-137">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="accda-138">`GreeterClient.SayHelloAsync`-サービス`Greeter.SayHello`を非同期的に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="accda-138">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="accda-139">待機することができます。</span><span class="sxs-lookup"><span data-stu-id="accda-139">Can be awaited.</span></span>
* <span data-ttu-id="accda-140">`GreeterClient.SayHello`-サービス`Greeter.SayHello`を呼び出し、が完了するまでブロックします。</span><span class="sxs-lookup"><span data-stu-id="accda-140">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="accda-141">非同期コードでは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="accda-141">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="accda-142">サーバーストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="accda-142">Server streaming call</span></span>

<span data-ttu-id="accda-143">サーバーストリーミング呼び出しは、クライアントから要求メッセージを送信することで開始されます。</span><span class="sxs-lookup"><span data-stu-id="accda-143">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="accda-144">`ResponseStream.MoveNext()`サービスからストリーミングされたメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="accda-144">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="accda-145">が返さ`ResponseStream.MoveNext()` `false`れると、サーバーストリーミングの呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="accda-145">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="accda-146">8以降を使用C#している場合は`await foreach` 、構文を使用してメッセージを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="accda-146">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="accda-147">拡張`IAsyncStreamReader<T>.ReadAllAsync()`メソッドは、応答ストリームからすべてのメッセージを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="accda-147">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="accda-148">クライアントストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="accda-148">Client streaming call</span></span>

<span data-ttu-id="accda-149">クライアントのストリーミング呼び出しは、クライアントがメッセージを送信する*ことなく*開始されます。</span><span class="sxs-lookup"><span data-stu-id="accda-149">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="accda-150">クライアントは、を使用して`RequestStream.WriteAsync`メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="accda-150">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="accda-151">クライアントがメッセージ`RequestStream.CompleteAsync`の送信を完了したら、サービスに通知するためにを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="accda-151">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="accda-152">サービスが応答メッセージを返すと、呼び出しが完了します。</span><span class="sxs-lookup"><span data-stu-id="accda-152">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="accda-153">双方向ストリーミング呼び出し</span><span class="sxs-lookup"><span data-stu-id="accda-153">Bi-directional streaming call</span></span>

<span data-ttu-id="accda-154">双方向のストリーミング呼び出しは、クライアントがメッセージを送信する*ことなく*開始されます。</span><span class="sxs-lookup"><span data-stu-id="accda-154">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="accda-155">クライアントは、を使用して`RequestStream.WriteAsync`メッセージを送信することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="accda-155">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="accda-156">サービスからストリーミングされたメッセージに`ResponseStream.MoveNext()`は`ResponseStream.ReadAllAsync()`、またはを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="accda-156">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="accda-157">にメッセージ`ResponseStream`がなくなった場合、双方向のストリーミング呼び出しは完了です。</span><span class="sxs-lookup"><span data-stu-id="accda-157">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="accda-158">双方向のストリーミング呼び出しでは、クライアントとサービスはいつでもメッセージを相互に送信できます。</span><span class="sxs-lookup"><span data-stu-id="accda-158">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="accda-159">双方向呼び出しとの対話に最適なクライアントロジックは、サービスロジックによって異なります。</span><span class="sxs-lookup"><span data-stu-id="accda-159">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="accda-160">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="accda-160">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
