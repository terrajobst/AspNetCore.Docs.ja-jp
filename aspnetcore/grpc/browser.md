---
title: ブラウザーアプリでの gRPC
author: jamesnk
description: GRPC-Web を使用してブラウザーアプリから呼び出すことができるように、ASP.NET Core で gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830634"
---
# <a name="grpc-in-browser-apps"></a>ブラウザーアプリでの gRPC

[James のニュートン-キング](https://twitter.com/jamesnk)別

> [!IMPORTANT]
> **gRPC-.NET での Web サポートは試験段階です**
>
> gRPC-Web for .NET は、コミットされた製品ではなく、試験的なプロジェクトです。 次のようにします。
>
> * GRPC の実装方法をテストします。
> * プロキシ経由で gRPC-Web を設定する従来の方法と比較して、.NET 開発者にとってこのアプローチが役立つかどうかについてのフィードバックをお寄せください。
>
> [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet)でフィードバックを残して、開発者が、で生産性を向上させることができることを確認してください。

ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。 [grpc-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md)は、ブラウザーの JavaScript および Blazor アプリが grpc サービスを呼び出すことができるようにするプロトコルです。 この記事では、.NET Core で gRPC-Web を使用する方法について説明します。

## <a name="configure-grpc-web-in-aspnet-core"></a>ASP.NET Core での gRPC-Web の構成

ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。 gRPC-Web では、サービスを変更する必要はありません。 唯一の変更はスタートアップ構成です。

ASP.NET Core gRPC サービスを使用して gRPC-Web を有効にするには:

* [AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore.Web)パッケージへの参照を追加します。
* `AddGrpcWeb` と `UseGrpcWeb` を*Startup.cs*に追加して grpc-Web を使用するようにアプリを構成します。

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

上のコードでは以下の操作が行われます。

* GRPC-Web ミドルウェア、`UseGrpcWeb`、およびエンドポイントの前に、を追加します。
* `endpoints.MapGrpcService<GreeterService>()` メソッドが `EnableGrpcWeb`で gRPC-Web をサポートすることを指定します。 

または、ConfigureServices に `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` を追加して、gRPC-Web をサポートするようにすべてのサービスを構成します。

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

CORS をサポートするように ASP.NET Core を構成するなど、ブラウザーから gRPC-Web を呼び出すには、追加の構成が必要になる場合があります。 詳細については、「 [CORS のサポート](xref:security/cors)」を参照してください。

## <a name="call-grpc-web-from-the-browser"></a>ブラウザーから gRPC-Web を呼び出す

ブラウザーアプリは gRPC-Web を使用して gRPC サービスを呼び出すことができます。 ブラウザーから gRPC-Web で gRPC サービスを呼び出すときには、いくつかの要件と制限があります。

* サーバーは、gRPC-Web をサポートするように構成されている必要があります。
* クライアントストリーミングと双方向のストリーミング呼び出しはサポートされていません。 サーバーストリーミングがサポートされています。
* 別のドメインで gRPC サービスを呼び出すには、サーバーで[CORS](xref:security/cors)を構成する必要があります。

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web クライアント

JavaScript gRPC-Web クライアントがあります。 JavaScript から gRPC-Web を使用する方法については、「 [grpc-web を使用して javascript クライアントコードを記述](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)する」を参照してください。

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>.NET gRPC クライアントを使用して gRPC-Web を構成する

.NET gRPC クライアントは、gRPC-Web 呼び出しを行うように構成できます。 これは、ブラウザーでホストされ、JavaScript コードの HTTP 制限が同じである[Blazor WebAssembly](xref:blazor/index#blazor-webassembly)に便利です。 .NET クライアントを使用した gRPC の呼び出しは[、HTTP/2 gRPC と同じ](xref:grpc/client)です。 唯一の変更は、チャネルの作成方法です。

GRPC-Web を使用するには:

* [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)パッケージへの参照を追加します。
* `GrpcWebHandler`を使用するようにチャネルを構成します。

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

上のコードでは以下の操作が行われます。

* GRPC-Web を使用するようにチャネルを構成します。
* クライアントを作成し、チャネルを使用して呼び出しを行います。

`GrpcWebHandler` の作成時には、次の構成オプションがあります。

* **Innerhandler**: HTTP 呼び出しを行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler`など)。
* **Mode**: `GrpcWebMode` 列挙型。 `GrpcWebMode.GrpcWebText` は、サーバーストリーミング呼び出しをサポートするために必要なコンテンツを base64 でエンコードするように構成します。
* **HttpVersion**: HTTP プロトコル `Version`。 gRPC-Web は特定のプロトコルを必要とせず、構成されていない限り、要求を行うときには指定しません。

## <a name="additional-resources"></a>その他の技術情報

* [Web クライアント用 gRPC GitHub プロジェクト](https://github.com/grpc/grpc-web)
* <xref:security/cors>
