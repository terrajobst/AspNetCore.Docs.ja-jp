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
# <a name="call-grpc-services-with-the-net-client"></a>.NET クライアントを使用して gRPC サービスを呼び出す

.NET gRPC クライアントライブラリは、 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)NuGet パッケージで入手できます。 このドキュメントでは、次の方法について説明します。

* Grpc サービスを呼び出すように gRPC クライアントを構成します。
* GRPC に対して、単項、サーバーストリーミング、クライアントストリーミング、および双方向のストリーミングメソッドを呼び出します。

## <a name="configure-grpc-client"></a>GRPC クライアントを構成する

gRPC クライアントは、[ *\*.proto* ファイルから生成される](xref:grpc/basics#generated-c-assets)具象的なクライアントの種類です。 具象 gRPC クライアントには、 *\*.proto* ファイル内の gRPC サービスに変換するためのメソッドが含まれます。

GRPC クライアントは、チャネルから作成されます。 まず、`GrpcChannel.ForAddress` を使用してチャネルを作成し、次にチャネルを使用して gRPC クライアントを作成します。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

チャネルは、gRPC サービスへの長期的な接続を表します。 チャネルが作成されると、サービスの呼び出しに関連するオプションが構成されます。 たとえば、呼び出しを行うために使用される `HttpClient`、送信メッセージと受信メッセージの最大サイズ、およびログ記録を `GrpcChannelOptions` で指定し、`GrpcChannel.ForAddress`と共に使用することができます。 オプションの完全な一覧については、「[クライアント構成オプション](xref:grpc/configuration#configure-client-options)」を参照してください。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

チャネルとクライアントのパフォーマンスと使用状況:

* チャネルの作成は、コストのかかる操作になることがあります。 GRPC 呼び出しにチャネルを再利用すると、パフォーマンスが向上します。
* gRPC クライアントは、チャネルを使用して作成されます。 gRPC クライアントはライトウェイトオブジェクトであり、キャッシュまたは再利用する必要はありません。
* 複数の gRPC クライアントは、さまざまな種類のクライアントを含むチャネルから作成できます。
* チャネルとチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。
* チャネルから作成されたクライアントは、複数の同時呼び出しを行うことができます。

gRPC クライアントを作成するためのオプションは `GrpcChannel.ForAddress` だけではありません。 ASP.NET Core アプリから gRPC サービスを呼び出している場合は、 [grpc クライアントファクトリの統合](xref:grpc/clientfactory)を検討してください。 gRPC と `HttpClientFactory` の統合には、gRPC クライアントを作成するための一元化された代替手段が用意されています。

> [!NOTE]
> [.Net クライアントでセキュリティで保護されていない gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)には、追加の構成が必要です。

## <a name="make-grpc-calls"></a>GRPC 呼び出しを行う

GRPC 呼び出しは、クライアントでメソッドを呼び出すことによって開始されます。 GRPC クライアントは、メッセージのシリアル化を処理し、適切なサービスに対する gRPC 呼び出しに対処します。

gRPC には、さまざまな種類のメソッドがあります。 クライアントを使用して gRPC 呼び出しを行う方法は、呼び出すメソッドの種類によって異なります。 GRPC のメソッドの種類は次のとおりです。

* 単項
* サーバーストリーミング
* クライアントストリーミング
* 双方向ストリーミング

### <a name="unary-call"></a>単項呼び出し

単項呼び出しは、クライアントからの要求メッセージの送信を開始します。 サービスの終了時に応答メッセージが返されます。

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

*\*プロトコル*ファイルの各単項サービスメソッドは、メソッドを呼び出すための具象 grpc クライアント型に、非同期メソッドとブロッキングメソッドの2つの .net メソッドを生成します。 たとえば、`GreeterClient` で `SayHello`を呼び出すには、次の2つの方法があります。

* `GreeterClient.SayHelloAsync`-`Greeter.SayHello` サービスを非同期に呼び出します。 待機することができます。
* `GreeterClient.SayHello`-`Greeter.SayHello` サービスを呼び出し、完了するまでブロックします。 非同期コードでは使用しないでください。

### <a name="server-streaming-call"></a>サーバーストリーミング呼び出し

サーバーストリーミング呼び出しは、クライアントから要求メッセージを送信することで開始されます。 `ResponseStream.MoveNext()` は、サービスからストリーミングされたメッセージを読み取ります。 `ResponseStream.MoveNext()` が `false`を返すと、サーバーストリーミングの呼び出しが完了します。

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

8以降を使用C#している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。 `IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、応答ストリームからすべてのメッセージを読み取ります。

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

### <a name="client-streaming-call"></a>クライアントストリーミング呼び出し

クライアントのストリーミング呼び出しは、クライアントがメッセージを送信する*ことなく*開始されます。 クライアントは、`RequestStream.WriteAsync`を使用してメッセージを送信することを選択できます。 クライアントがメッセージの送信を完了したら `RequestStream.CompleteAsync` を呼び出してサービスに通知する必要があります。 サービスが応答メッセージを返すと、呼び出しが完了します。

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

### <a name="bi-directional-streaming-call"></a>双方向ストリーミング呼び出し

双方向のストリーミング呼び出しは、クライアントがメッセージを送信する*ことなく*開始されます。 クライアントは、`RequestStream.WriteAsync`を使用してメッセージを送信することを選択できます。 サービスからストリーミングされたメッセージには、`ResponseStream.MoveNext()` または `ResponseStream.ReadAllAsync()`でアクセスできます。 双方向ストリーミング呼び出しは、`ResponseStream` にメッセージがなくなったときに完了します。

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

双方向のストリーミング呼び出しでは、クライアントとサービスはいつでもメッセージを相互に送信できます。 双方向呼び出しとの対話に最適なクライアントロジックは、サービスロジックによって異なります。

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
