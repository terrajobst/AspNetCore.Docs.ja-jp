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
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core での gRPC クライアントファクトリの統合

grpc との`HttpClientFactory`統合により、grpc クライアントを一元的に作成することができます。 [スタンドアロンの gRPC クライアントインスタンスを構成](xref:grpc/client)する代わりに使用することもできます。 ファクトリ統合は、 [Grpc .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet パッケージで使用できます。

ファクトリには、次のような利点があります。

* 論理 gRPC クライアントインスタンスを構成するための一元的な場所を提供します
* 基になるの有効期間を管理します`HttpClientMessageHandler`
* ASP.NET Core gRPC サービスでの期限とキャンセルの自動伝達

## <a name="register-grpc-clients"></a>GRPC クライアントの登録

Grpc クライアントを登録するには、 `AddGrpcClient`内で`Startup.ConfigureServices`汎用拡張メソッドを使用し、grpc で型指定されたクライアントクラスとサービスアドレスを指定します。

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

GRPC クライアントの種類は、依存関係の注入 (DI) と共に一時的に登録されます。 これで、クライアントを DI によって作成された型に直接挿入して使用できるようになりました。 ASP.NET Core MVC コントローラー、SignalR hub、および gRPC サービスは、gRPC クライアントが自動的に挿入される場所です。

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

## <a name="configure-httpclient"></a>HttpClient の構成

`HttpClientFactory`grpc `HttpClient`クライアントによって使用されるを作成します。 標準`HttpClientFactory`メソッドを使用して、発信要求ミドルウェアを追加したり、 `HttpClientHandler` `HttpClient`の基になるを構成したりすることができます。

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

詳細については、「 [IHttpClientFactory を使用して HTTP 要求を作成する](xref:fundamentals/http-requests)」を参照してください。

## <a name="configure-channel-and-interceptors"></a>チャネルとインターセプターの構成

gRPC 固有のメソッドは、次の目的で使用できます。

* GRPC クライアントの基になるチャネルを構成します。
* Grpc 呼び出しを行うときにクライアントが使用するインスタンスを追加`Interceptor`します。

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

## <a name="deadline-and-cancellation-propagation"></a>期限と取り消しの反映

grpc サービスでファクトリによって作成された grpc クライアントは`EnableCallContextPropagation()` 、期限とキャンセルトークンを子呼び出しに自動的に反映するように、を使用して構成できます。 拡張メソッドは、[Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet パッケージに含まれています。`EnableCallContextPropagation()`

呼び出しコンテキストの伝達は、現在の gRPC 要求コンテキストから期限とキャンセルトークンを読み取り、gRPC クライアントによって行われた発信呼び出しに自動的に伝達することによって機能します。 呼び出しコンテキストの伝達は、複雑で入れ子になった gRPC のシナリオが常に期限とキャンセルを伝達することを保証する優れた方法です。

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

期限と RPC のキャンセルの詳細については、「 [rpc ライフサイクル](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
