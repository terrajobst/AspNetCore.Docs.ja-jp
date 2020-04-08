---
title: .NET クライアントを使用して gRPC サービスを呼び出す
author: jamesnk
description: .NET gRPC クライアントを使用して gRPC サービスを呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650804"
---
# <a name="call-grpc-services-with-the-net-client"></a>.NET クライアントを使用して gRPC サービスを呼び出す

.NET gRPC クライアント ライブラリは、[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet パッケージで提供されています。 このドキュメントでは、以下の方法について説明します。

* gRPC サービスを呼び出すように gRPC クライアントを構成します。
* unary、サーバー ストリーミング、クライアント ストリーミング、双方向ストリーミングの各メソッドに対する gRPC 呼び出しを行います。

## <a name="configure-grpc-client"></a>gRPC クライアントを構成する

gRPC クライアントは、[ *\*.proto* ファイルから生成される](xref:grpc/basics#generated-c-assets)具象的なクライアントの種類です。 具象 gRPC クライアントには、 *\*.proto* ファイル内の gRPC サービスに変換するためのメソッドが含まれます。

gRPC クライアントはチャネルから作成されます。 まず `GrpcChannel.ForAddress` を使用してチャネルを作成し、次にそのチャネルを使用して、gRPC クライアントを作成します。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

チャネルは、gRPC サービスへの有効期限が長い接続を表します。 チャネルが作成されるときには、サービスの呼び出しに関連するオプションによって構成されます。 たとえば呼び出しを行うために使用される `HttpClient` の場合、`GrpcChannelOptions` で送受信メッセージの最大サイズとログ記録を指定し、`GrpcChannel.ForAddress` と共に使用することができます。 オプションの完全な一覧については、[クライアント構成のオプション](xref:grpc/configuration#configure-client-options)に関するページを参照してください。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

チャネルおよびクライアントのパフォーマンスと使用状況:

* チャネルの作成は、コストのかかる操作となる場合があります。 チャネルを gRPC 呼び出しのために再利用すると、パフォーマンス上のメリットがあります。
* gRPC クライアントはチャネルを使用して作成されます。 gRPC クライアントは軽量なオブジェクトであり、キャッシュしたり再利用したりする必要はありません。
* 異なる種類のクライアントを含め、1 つチャネルから複数の gRPC クライアントを作成できます。
* チャネルおよびそのチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。
* チャネルから作成されたクライアントは、複数の同時呼び出しを行えます。

`GrpcChannel.ForAddress` は、gRPC クライアントを作成する際の唯一のオプションではありません。 ASP.NET Core アプリから gRPC サービスを呼び出そうとしている場合は、[gRPC クライアント ファクトリの統合](xref:grpc/clientfactory)を検討してください。 gRPC と `HttpClientFactory` の統合により、gRPC クライアントを一元的に作成する別の方法が得られます。

> [!NOTE]
> [.NET クライアントで安全でない gRPC サービスを呼び出す](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)には、追加の構成が必要です。

> [!NOTE]
> 現在のところ、`Grpc.Net.Client` による HTTP/2 を介した gRPC の呼び出しは、Xamarin ではサポートされていません。 将来の Xamarin のリリースで HTTP/2 のサポートを向上させるために作業を進めています。 [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) と [gRPC-Web](xref:grpc/browser) は、現在でも機能する実行可能な代替手段です。

## <a name="make-grpc-calls"></a>gRPC 呼び出しを行う

gRPC 呼び出しは、クライアントでメソッドを呼び出すことで開始されます。 gRPC クライアントは、メッセージのシリアル化と、gRPC 呼び出しの正しいサービスへのアドレス指定を処理します。

gRPC には、さまざまな種類のメソッドがあります。 クライアントを使用して gRPC 呼び出しを行う方法は、呼び出そうとしているメソッドの種類によって異なります。 gRPC のメソッドの種類は次のとおりです。

* 単項演算子
* サーバー ストリーミング。
* クライアント ストリーミング
* 双方向ストリーミング

### <a name="unary-call"></a>Unary 呼び出し

Unary 呼び出しは、要求メッセージを送信するクライアントで始まります。 サービスが終了すると応答メッセージが返されます。

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

*\*.proto* ファイル内の各 Unary サービス メソッドは、メソッドを呼び出すための具象 gRPC クライアント型に関する 2 つの .NET メソッドとなります。非同期のメソッドとブロックするメソッドです。 たとえば `GreeterClient` に関しては、`SayHello` を呼び出す方法は 2 つあります。

* `GreeterClient.SayHelloAsync` - `Greeter.SayHello` サービスを非同期に呼び出します。 待機可能です。
* `GreeterClient.SayHello` - `Greeter.SayHello` サービスを呼び出して、完了するまでブロックします。 非同期のコードでは使用しないでください。

### <a name="server-streaming-call"></a>サーバー ストリーミング呼び出し

サーバー ストリーミング呼び出しは、要求メッセージを送信するクライアントで始まります。 `ResponseStream.MoveNext()` は、サービスからストリーミングされたメッセージを読み取ります。 `ResponseStream.MoveNext()` が `false` を返すとサーバー ストリーミングの呼び出しが完了します。

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

C# 8 以降を使用している場合は、`await foreach` 構文を使用してメッセージを読み取ることができます。 `IAsyncStreamReader<T>.ReadAllAsync()` 拡張メソッドは、応答ストリームからすべてのメッセージを読み取ります。

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

### <a name="client-streaming-call"></a>クライアント ストリーミング呼び出し

クライアント ストリーミング呼び出しは、メッセージを送信するクライアント*なしで*始まります。 クライアントでは、`RequestStream.WriteAsync` を使用して、メッセージを送信することを選択できます。 クライアントがメッセージの送信を完了したら、`RequestStream.CompleteAsync` を呼び出してサービスに通知する必要があります。 サービスが応答メッセージを返すと呼び出しが完了します。

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

双方向ストリーミング呼び出しは、メッセージを送信するクライアント*なしで*始まります。 クライアントでは、`RequestStream.WriteAsync` を使用して、メッセージを送信することを選択できます。 サービスからストリーミングされたメッセージには、`ResponseStream.MoveNext()` または `ResponseStream.ReadAllAsync()` を使用してアクセスできます。 双方向ストリーミング呼び出しは、`ResponseStream` にメッセージがなくなると完了します。

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

双方向ストリーミング呼び出しの間、クライアントとサービスはいつでも相互にメッセージを送信できます。 双方向呼び出しの操作に最適なクライアント ロジックは、サービス ロジックによってさまざまです。

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
