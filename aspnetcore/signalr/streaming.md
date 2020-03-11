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
ms.openlocfilehash: 21dd8180fe168f81ed68b01f02b81a6264d6e5a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654974"
---
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a>ASP.NET Core SignalR でのストリーミングの使用

[Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR は、クライアントからサーバー、およびサーバーからクライアントへのストリーミングをサポートしています。 これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。 ストリーミング時には、すべてのデータが使用可能になるのを待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントまたはサーバーに送信されます。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR では、サーバーメソッドのストリーミング戻り値がサポートされています。 これは、時間の経過と共にデータのフラグメントが到着するシナリオに役立ちます。 戻り値がクライアントにストリーミングされると、すべてのデータが使用可能になるまで待機するのではなく、使用可能になるとすぐに各フラグメントがクライアントに送信されます。

::: moniker-end

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="set-up-a-hub-for-streaming"></a>ストリーミング用のハブを設定する

::: moniker range=">= aspnetcore-3.0"

ハブメソッドは、<xref:System.Collections.Generic.IAsyncEnumerable`1>、<xref:System.Threading.Channels.ChannelReader%601>、`Task<IAsyncEnumerable<T>>`、または `Task<ChannelReader<T>>`を返すと、自動的にストリーミングハブメソッドになります。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ハブメソッドは、<xref:System.Threading.Channels.ChannelReader%601> または `Task<ChannelReader<T>>`を返すと、自動的にストリーミングハブメソッドになります。

::: moniker-end

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング

::: moniker range=">= aspnetcore-3.0"

ストリーミングハブのメソッドは、`ChannelReader<T>`に加えて `IAsyncEnumerable<T>` を返すことができます。 `IAsyncEnumerable<T>` を返す最も簡単な方法は、次の例に示すように、ハブメソッドを非同期反復子メソッドにすることです。 ハブの非同期反復子メソッドは、クライアントがストリームからアンサブスクライブしたときにトリガーされる `CancellationToken` パラメーターを受け取ることができます。 非同期反復子メソッドは、チャネルに共通する問題を回避します。たとえば、`ChannelReader` が早期に返されたり、<xref:System.Threading.Channels.ChannelWriter`1>を完了せずにメソッドを終了したりすることはありません。

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

次のサンプルは、チャネルを使用してクライアントにデータをストリーミングする方法の基本を示しています。 オブジェクトが <xref:System.Threading.Channels.ChannelWriter%601>に書き込まれるたびに、オブジェクトは直ちにクライアントに送信されます。 最後に、`ChannelWriter` は、ストリームが閉じられていることをクライアントに通知するために完了します。

> [!NOTE]
> バックグラウンドスレッドで `ChannelWriter<T>` に書き込み、できるだけ早く `ChannelReader` を返します。 その他のハブ呼び出しは、`ChannelReader` が返されるまでブロックされます。
>
> `try ... catch`にロジックをラップします。 ハブメソッドの呼び出しが正しく完了していることを確認するために、`catch` と `catch` の外側の `Channel` を完了します。

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

サーバーからクライアントへのストリーミングハブメソッドでは、クライアントがストリームからアンサブスクライブしたときにトリガーされる `CancellationToken` パラメーターを受け取ることができます。 ストリームの終了前にクライアントが切断された場合は、このトークンを使用してサーバー操作を停止し、リソースを解放します。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>クライアントとサーバー間のストリーミング

ハブメソッドは、<xref:System.Threading.Channels.ChannelReader%601> または <xref:System.Collections.Generic.IAsyncEnumerable%601>型の1つ以上のオブジェクトを受け入れると、クライアントとサーバー間のストリーミングハブメソッドに自動的になります。 次のサンプルは、クライアントから送信されたストリーミングデータの読み取りの基本を示しています。 クライアントが <xref:System.Threading.Channels.ChannelWriter%601>に書き込むたびに、データはハブメソッドの読み取り元のサーバー上の `ChannelReader` に書き込まれます。

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

メソッドの <xref:System.Collections.Generic.IAsyncEnumerable%601> バージョンは次のとおりです。

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

## <a name="net-client"></a>.NET クライアント

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング


::: moniker range=">= aspnetcore-3.0"

`HubConnection` の `StreamAsync` および `StreamAsChannelAsync` メソッドは、サーバー間のストリーミングメソッドを呼び出すために使用されます。 ハブメソッドで定義されているハブメソッド名と引数を `StreamAsync` または `StreamAsChannelAsync`に渡します。 `StreamAsync<T>` および `StreamAsChannelAsync<T>` のジェネリックパラメーターは、ストリーミングメソッドによって返されるオブジェクトの種類を指定します。 `IAsyncEnumerable<T>` または `ChannelReader<T>` 型のオブジェクトがストリーム呼び出しから返され、クライアント上のストリームを表します。

`IAsyncEnumerable<int>`を返す `StreamAsync` の例を次に示します。

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

`ChannelReader<int>`を返す、対応する `StreamAsChannelAsync` の例を次に示します。

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

`HubConnection` の `StreamAsChannelAsync` メソッドは、サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。 ハブメソッドで定義されているハブメソッド名と引数を `StreamAsChannelAsync`に渡します。 `StreamAsChannelAsync<T>` のジェネリックパラメーターは、ストリーミングメソッドによって返されるオブジェクトの種類を指定します。 ストリーム呼び出しから `ChannelReader<T>` が返され、クライアントのストリームを表します。

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

`HubConnection` の `StreamAsChannelAsync` メソッドは、サーバーからクライアントへのストリーミングメソッドを呼び出すために使用されます。 ハブメソッドで定義されているハブメソッド名と引数を `StreamAsChannelAsync`に渡します。 `StreamAsChannelAsync<T>` のジェネリックパラメーターは、ストリーミングメソッドによって返されるオブジェクトの種類を指定します。 ストリーム呼び出しから `ChannelReader<T>` が返され、クライアントのストリームを表します。

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

### <a name="client-to-server-streaming"></a>クライアントとサーバー間のストリーミング

.NET クライアントからクライアントとサーバー間のストリーミングハブメソッドを呼び出すには、2つの方法があります。 呼び出されたハブメソッドに応じて、`IAsyncEnumerable<T>` または `ChannelReader` を、`SendAsync`、`InvokeAsync`、または `StreamAsChannelAsync`への引数として渡すことができます。

`IAsyncEnumerable` または `ChannelWriter` オブジェクトにデータが書き込まれるたびに、サーバーのハブメソッドは、クライアントからのデータを使用して新しい項目を受け取ります。

`IAsyncEnumerable` オブジェクトを使用している場合、ストリームはストリーム項目を返すメソッドが終了した後に終了します。

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

または、`ChannelWriter`を使用している場合は、`channel.Writer.Complete()`でチャネルを完了します。

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript クライアント

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング

JavaScript クライアントは、`connection.stream`を使用してハブでサーバーからクライアントへのストリーミングメソッドを呼び出します。 `stream` メソッドは、次の2つの引数を受け取ります。

* ハブ メソッドの名前。 次の例では、ハブメソッド名は `Counter`です。
* ハブメソッドで定義されている引数。 次の例では、引数は、受信するストリーム項目数とストリーム項目間の遅延をカウントします。

`connection.stream` は、`subscribe` メソッドを含む `IStreamResult`を返します。 `subscribe` に `IStreamSubscriber` を渡し、`next`、`error`、および `complete` のコールバックを設定して、`stream` 呼び出しからの通知を受信します。

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

クライアントからストリームを終了するには、`subscribe` メソッドから返された `ISubscription` に対して `dispose` メソッドを呼び出します。 このメソッドを呼び出すと、指定した場合、ハブメソッドの `CancellationToken` パラメーターがキャンセルされます。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

クライアントからストリームを終了するには、`subscribe` メソッドから返された `ISubscription` に対して `dispose` メソッドを呼び出します。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>クライアントとサーバー間のストリーミング

JavaScript クライアントは、呼び出されたハブメソッドに応じて、`Subject` を `send`、`invoke`、または `stream`の引数として渡すことによって、ハブでクライアントからサーバーへのストリーミングメソッドを呼び出します。 `Subject` は、`Subject`のようなクラスです。 たとえば、RxJS では、そのライブラリの[サブジェクト](https://rxjs-dev.firebaseapp.com/api/index/class/Subject)クラスを使用できます。

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

項目を使用して `subject.next(item)` を呼び出すと、項目がストリームに書き込まれ、ハブメソッドはサーバー上の項目を受け取ります。

ストリームを終了するには、`subject.complete()`を呼び出します。

## <a name="java-client"></a>Java クライアント

### <a name="server-to-client-streaming"></a>サーバーからクライアントへのストリーミング

SignalR Java クライアントは、`stream` メソッドを使用してストリーミングメソッドを呼び出します。 `stream` は、次の3つ以上の引数を受け取ります。

* ストリーム項目の予期される型。
* ハブ メソッドの名前。
* ハブメソッドで定義されている引数。

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`HubConnection` の `stream` メソッドは、ストリーム項目の型の観測可能なオブジェクトを返します。 観測可能な型の `subscribe` メソッドは、`onNext`、`onError`、および `onCompleted` ハンドラーが定義されている場所です。

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* [ハブ](xref:signalr/hubs)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
