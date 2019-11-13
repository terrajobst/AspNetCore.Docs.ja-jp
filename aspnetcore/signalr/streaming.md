---
title: ASP.NET Core SignalR でのストリーミングの使用
author: bradygaster
description: クライアントとサーバーの間でデータをストリーミングする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 7825beba55cefb6236fd8d8e332d030a7e4fc6df
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963885"
---
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="24fd6-103">ASP.NET Core SignalR でのストリーミングの使用</span><span class="sxs-lookup"><span data-stu-id="24fd6-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="24fd6-104">[Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="24fd6-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24fd6-105">ASP.NET Core SignalR は、クライアントからサーバー、およびサーバーからクライアントへのストリーミングをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="24fd6-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="24fd6-106">これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="24fd6-107">ストリーミング時には、すべてのデータが使用可能になるのを待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントまたはサーバーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24fd6-108">ASP.NET Core SignalR では、サーバーメソッドのストリーミング戻り値がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="24fd6-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="24fd6-109">これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="24fd6-110">戻り値がクライアントにストリーミングされると、すべてのデータが使用可能になるまで待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="24fd6-111">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="24fd6-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="24fd6-112">ストリーミング用のハブを設定する</span><span class="sxs-lookup"><span data-stu-id="24fd6-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24fd6-113">ハブメソッドは、<xref:System.Collections.Generic.IAsyncEnumerable`1>、<xref:System.Threading.Channels.ChannelReader%601>、`Task<IAsyncEnumerable<T>>`、または `Task<ChannelReader<T>>`を返すと、自動的にストリーミングハブメソッドになります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="24fd6-114">ハブメソッドは、<xref:System.Threading.Channels.ChannelReader%601> または `Task<ChannelReader<T>>`を返すと、自動的にストリーミングハブメソッドになります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="24fd6-115">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24fd6-116">ストリーミングハブのメソッドは、`ChannelReader<T>`に加えて `IAsyncEnumerable<T>` を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="24fd6-117">`IAsyncEnumerable<T>` を返す最も簡単な方法は、次の例に示すように、ハブメソッドを非同期反復子メソッドにすることです。</span><span class="sxs-lookup"><span data-stu-id="24fd6-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="24fd6-118">ハブの非同期反復子メソッドは、クライアントがストリームからアンサブスクライブしたときにトリガーされる `CancellationToken` パラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="24fd6-119">非同期反復子メソッドは、チャネルに共通する問題を回避します。たとえば、`ChannelReader` が早期に返されたり、<xref:System.Threading.Channels.ChannelWriter`1>を完了せずにメソッドを終了したりすることはありません。</span><span class="sxs-lookup"><span data-stu-id="24fd6-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="24fd6-120">次のサンプルは、チャネルを使用してクライアントにデータをストリーミングする方法の基本を示しています。</span><span class="sxs-lookup"><span data-stu-id="24fd6-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="24fd6-121">オブジェクトが <xref:System.Threading.Channels.ChannelWriter%601>に書き込まれるたびに、オブジェクトは直ちにクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="24fd6-122">最後に、`ChannelWriter` は、ストリームが閉じられていることをクライアントに通知するために完了します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="24fd6-123">バックグラウンドスレッドで `ChannelWriter<T>` に書き込み、できるだけ早く `ChannelReader` を返します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="24fd6-124">その他のハブ呼び出しは、`ChannelReader` が返されるまでブロックされます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="24fd6-125">`try ... catch`にロジックをラップします。</span><span class="sxs-lookup"><span data-stu-id="24fd6-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="24fd6-126">ハブメソッドの呼び出しが正しく完了していることを確認するために、`catch` と `catch` の外側の `Channel` を完了します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="24fd6-127">サーバーからクライアントへのストリーミングハブメソッドでは、クライアントがストリームからアンサブスクライブしたときにトリガーされる `CancellationToken` パラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="24fd6-128">ストリームの終了前にクライアントが切断された場合は、このトークンを使用してサーバー操作を停止し、リソースを解放します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="24fd6-129">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-129">Client-to-server streaming</span></span>

<span data-ttu-id="24fd6-130">ハブメソッドは、<xref:System.Threading.Channels.ChannelReader%601> または <xref:System.Collections.Generic.IAsyncEnumerable%601>型の1つ以上のオブジェクトを受け入れると、クライアントとサーバー間のストリーミングハブメソッドに自動的になります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="24fd6-131">次のサンプルは、クライアントから送信されたストリーミングデータの読み取りの基本を示しています。</span><span class="sxs-lookup"><span data-stu-id="24fd6-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="24fd6-132">クライアントが <xref:System.Threading.Channels.ChannelWriter%601>に書き込むたびに、データはハブメソッドの読み取り元のサーバー上の `ChannelReader` に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="24fd6-133">メソッドの <xref:System.Collections.Generic.IAsyncEnumerable%601> バージョンは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="24fd6-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="24fd6-134">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="24fd6-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="24fd6-135">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="24fd6-136">`HubConnection` の `StreamAsync` および `StreamAsChannelAsync` メソッドは、サーバー間のストリーミングメソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="24fd6-137">ハブメソッドで定義されているハブメソッド名と引数を `StreamAsync` または `StreamAsChannelAsync`に渡します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="24fd6-138">`StreamAsync<T>` および `StreamAsChannelAsync<T>` のジェネリックパラメーターは、ストリーミングメソッドによって返されるオブジェクトの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="24fd6-139">`IAsyncEnumerable<T>` または `ChannelReader<T>` 型のオブジェクトがストリーム呼び出しから返され、クライアント上のストリームを表します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="24fd6-140">`IAsyncEnumerable<int>`を返す `StreamAsync` の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="24fd6-141">`ChannelReader<int>`を返す、対応する `StreamAsChannelAsync` の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="24fd6-142">`HubConnection` の `StreamAsChannelAsync` メソッドは、サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="24fd6-143">ハブメソッドで定義されているハブメソッド名と引数を `StreamAsChannelAsync`に渡します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="24fd6-144">`StreamAsChannelAsync<T>` のジェネリックパラメーターは、ストリーミングメソッドによって返されるオブジェクトの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="24fd6-145">ストリーム呼び出しから `ChannelReader<T>` が返され、クライアントのストリームを表します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="24fd6-146">`HubConnection` の `StreamAsChannelAsync` メソッドは、サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="24fd6-147">ハブメソッドで定義されているハブメソッド名と引数を `StreamAsChannelAsync`に渡します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="24fd6-148">`StreamAsChannelAsync<T>` のジェネリックパラメーターは、ストリーミングメソッドによって返されるオブジェクトの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="24fd6-149">ストリーム呼び出しから `ChannelReader<T>` が返され、クライアントのストリームを表します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="24fd6-150">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-150">Client-to-server streaming</span></span>

<span data-ttu-id="24fd6-151">.NET クライアントからクライアントとサーバー間のストリーミングハブメソッドを呼び出すには、2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="24fd6-152">呼び出されたハブメソッドに応じて、`IAsyncEnumerable<T>` または `ChannelReader` を、`SendAsync`、`InvokeAsync`、または `StreamAsChannelAsync`への引数として渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="24fd6-153">`IAsyncEnumerable` または `ChannelWriter` オブジェクトにデータが書き込まれるたびに、サーバーのハブメソッドは、クライアントからのデータを使用して新しい項目を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="24fd6-154">`IAsyncEnumerable` オブジェクトを使用している場合、ストリームはストリーム項目を返すメソッドが終了した後に終了します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="24fd6-155">または、`ChannelWriter`を使用している場合は、`channel.Writer.Complete()`でチャネルを完了します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="24fd6-156">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="24fd6-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="24fd6-157">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-157">Server-to-client streaming</span></span>

<span data-ttu-id="24fd6-158">JavaScript クライアントは、`connection.stream`を使用してハブでサーバーからクライアントへのストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="24fd6-159">`stream` メソッドは、次の2つの引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="24fd6-160">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="24fd6-160">The name of the hub method.</span></span> <span data-ttu-id="24fd6-161">次の例では、ハブメソッド名は `Counter`です。</span><span class="sxs-lookup"><span data-stu-id="24fd6-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="24fd6-162">ハブメソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="24fd6-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="24fd6-163">次の例では、引数は、受信するストリーム項目数とストリーム項目間の遅延をカウントします。</span><span class="sxs-lookup"><span data-stu-id="24fd6-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="24fd6-164">`connection.stream` は、`subscribe` メソッドを含む `IStreamResult`を返します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="24fd6-165">`subscribe` に `IStreamSubscriber` を渡し、`next`、`error`、および `complete` のコールバックを設定して、`stream` 呼び出しからの通知を受信します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="24fd6-166">クライアントからストリームを終了するには、`subscribe` メソッドから返された `ISubscription` に対して `dispose` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="24fd6-167">このメソッドを呼び出すと、指定した場合、ハブメソッドの `CancellationToken` パラメーターがキャンセルされます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="24fd6-168">クライアントからストリームを終了するには、`subscribe` メソッドから返された `ISubscription` に対して `dispose` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="24fd6-169">クライアントとサーバー間のストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-169">Client-to-server streaming</span></span>

<span data-ttu-id="24fd6-170">JavaScript クライアントは、呼び出されたハブメソッドに応じて、`Subject` を `send`、`invoke`、または `stream`の引数として渡すことによって、ハブでクライアントからサーバーへのストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="24fd6-171">`Subject` は、`Subject`のようなクラスです。</span><span class="sxs-lookup"><span data-stu-id="24fd6-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="24fd6-172">たとえば、RxJS では、そのライブラリの[サブジェクト](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="24fd6-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="24fd6-173">項目を使用して `subject.next(item)` を呼び出すと、項目がストリームに書き込まれ、ハブメソッドはサーバー上の項目を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="24fd6-174">ストリームを終了するには、`subject.complete()`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="24fd6-175">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="24fd6-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="24fd6-176">サーバーからクライアントへのストリーミング</span><span class="sxs-lookup"><span data-stu-id="24fd6-176">Server-to-client streaming</span></span>

<span data-ttu-id="24fd6-177">SignalR Java クライアントは、`stream` メソッドを使用してストリーミングメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="24fd6-178">`stream` は、次の3つ以上の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="24fd6-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="24fd6-179">ストリーム項目の予期される型。</span><span class="sxs-lookup"><span data-stu-id="24fd6-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="24fd6-180">ハブメソッドの名前。</span><span class="sxs-lookup"><span data-stu-id="24fd6-180">The name of the hub method.</span></span>
* <span data-ttu-id="24fd6-181">ハブメソッドで定義されている引数。</span><span class="sxs-lookup"><span data-stu-id="24fd6-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="24fd6-182">`HubConnection` の `stream` メソッドは、ストリーム項目の型の観測可能なオブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="24fd6-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="24fd6-183">観測可能な型の `subscribe` メソッドは、`onNext`、`onError`、および `onCompleted` ハンドラーが定義されている場所です。</span><span class="sxs-lookup"><span data-stu-id="24fd6-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="24fd6-184">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="24fd6-184">Additional resources</span></span>

* [<span data-ttu-id="24fd6-185">ハブ</span><span class="sxs-lookup"><span data-stu-id="24fd6-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="24fd6-186">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="24fd6-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="24fd6-187">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="24fd6-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="24fd6-188">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="24fd6-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
