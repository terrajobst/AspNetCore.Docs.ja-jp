---
title: ブラウザー アプリでの gRPC の使用
author: jamesnk
description: ASP.NET Core で、GRPC-Web を使用してブラウザー アプリから呼び出しできるように、gRPC サービスを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649418"
---
# <a name="use-grpc-in-browser-apps"></a>ブラウザー アプリでの gRPC の使用

作成者: [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **.NET での gRPC-Web のサポートは試験段階です**
>
> gRPC-Web for .NET は、コミットされた製品ではなく、試験段階のプロジェクトです。 次の目的があります。
>
> * gRPC-Web の実装方法が機能することをテストします。
> * .NET 開発者にとって、プロキシ経由で gRPC-Web を設定する従来の方法と比較して、この方法が有益であるかどうかについてのフィードバックを得ます。
>
> 開発者が望み、生産性が向上するものを構築するために、[https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) でフィードバックをお寄せください。

ブラウザーベースのアプリから HTTP/2 gRPC サービスを呼び出すことはできません。 [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) は、ブラウザーの JavaScript および Blazor アプリで gRPC サービスを呼び出せるようにするプロトコルです。 この記事では、.NET Core で gRPC-Web を使用する方法について説明します。

## <a name="configure-grpc-web-in-aspnet-core"></a>ASP.NET Core での gRPC-Web の構成

ASP.NET Core でホストされている gRPC サービスは、HTTP/2 gRPC と共に gRPC-Web をサポートするように構成できます。 gRPC-Web では、サービスを変更する必要はありません。 唯一の変更点はスタートアップ構成です。

ASP.NET Core gRPC サービスで gRPC-Web を有効にするには:

* [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) パッケージへの参照を追加します。
* アプリで gRPC-Web を使用するように構成するには、`AddGrpcWeb` と `UseGrpcWeb` を *Startup.cs* に追加します。

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

上記のコードでは次の操作が行われます。

* GRPC-Web ミドルウェアの `UseGrpcWeb` を、ルーティングの後かつエンドポイントの前に追加します。
* `endpoints.MapGrpcService<GreeterService>()` メソッドで、`EnableGrpcWeb` を使用して gRPC-Web をサポートすることを指定します。 

または、ConfigureServices に `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` を追加して、すべてのサービスで gRPC-Web をサポートするように構成します。

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a>gRPC-Web と CORS

ブラウザーのセキュリティにより、Web ページを提供したドメインと異なるドメインに対して、Web ページが要求を行うことはできません。 この制限は、ブラウザー アプリで gRPC-Web 呼び出しを行う場合に適用されます。 たとえば、`https://www.contoso.com` によって提供されるブラウザー アプリでは、`https://services.contoso.com` でホストされている gRPC-Web サービスの呼び出しがブロックされます。 この制限を緩和するには、クロス オリジン リソース共有 (CORS) を使用できます。

ブラウザー アプリでクロス オリジン gRPC-Web 呼び出しを行えるようにするには、[ASP.NET Core で CORS](xref:security/cors) を設定します。 組み込みの CORS サポートを使用し、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> で gRPC 固有のヘッダーを公開します。

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

上記のコードでは次の操作が行われます。

* `AddCors` を呼び出して CORS サービスを追加し、gRPC 固有のヘッダーを公開する CORS ポリシーを構成します。
* `UseCors` を呼び出して、ルーティングの後かつエンドポイントの前に CORS ミドルウェアを追加します。
* `endpoints.MapGrpcService<GreeterService>()` メソッドが `RequiresCors` で CORS をサポートすることを指定します。

## <a name="call-grpc-web-from-the-browser"></a>ブラウザーから gRPC-Web を呼び出す

ブラウザー アプリでは gRPC-Web を使用して gRPC サービスを呼び出すことができます。 ブラウザーから gRPC-Web を使用して gRPC サービスを呼び出す場合、いくつかの要件と制限があります。

* サーバーは、gRPC-Web をサポートするように構成されている必要があります。
* クライアント ストリーミングと双方向ストリーミングの呼び出しはサポートされていません。 サーバー ストリーミングはサポートされています。
* 別のドメインで gRPC サービスを呼び出すには、サーバーで [CORS](xref:security/cors) を構成する必要があります。

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web クライアント

JavaScript gRPC-Web クライアントがあります。 JavaScript から gRPC-Web を使用する方法については、[gRPC-Web を使用した JavaScript クライアント コードの作成](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)に関するページを参照してください。

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>.NET gRPC クライアントを使用して gRPC-Web を構成する

gRPC-Web 呼び出しを行うように .NET gRPC クライアントを構成できます。 これは、ブラウザーでホストされ、JavaScript コードの同じ HTTP 制限がある [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) アプリに役立ちます。 .NET クライアントによる gRPC-Web の呼び出しは、[HTTP/2 gRPC と同じ](xref:grpc/client)です。 唯一の変更点は、チャネルの作成方法です。

gRPC-Web を使用するには:

* [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) パッケージへの参照を追加します。
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) パッケージへの参照が 2.27.0 以上であることを確認します。
* `GrpcWebHandler` を使用するようにチャネルを構成します。

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

上記のコードでは次の操作が行われます。

* gRPC-Web を使用するようにチャネルを構成します。
* クライアントを作成し、チャネルを使用して呼び出しを行います。

作成時に、`GrpcWebHandler` には次の構成オプションがあります。

* **InnerHandler**:gRPC HTTP 要求を行う基になる <xref:System.Net.Http.HttpMessageHandler> (`HttpClientHandler` など)。
* **[モード]** :gRPC HTTP 要求の `Content-Type` が `application/grpc-web` または `application/grpc-web-text` であるかどうかを指定する列挙型。
    * `GrpcWebMode.GrpcWeb` は、コンテンツをエンコードせずに送信するように構成します。 既定値です。
    * `GrpcWebMode.GrpcWebText` は、コンテンツを base64 でエンコードするように構成します。 ブラウザーでのサーバー ストリーム呼び出しに必要です。
* **HttpVersion**:基になる gRPC HTTP 要求で、[HttpRequestMessage](xref:System.Net.Http.HttpRequestMessage.Version) を設定するために使用される HTTP プロトコル `Version`。 gRPC-Web では特定のバージョンを必要とせず、指定しない限り、既定値はオーバーライドされません。

> [!IMPORTANT]
> 生成された gRPC クライアントには、単項メソッドを呼び出すための同期メソッドと非同期メソッドがあります。 たとえば、`SayHello` は同期であり、`SayHelloAsync` は非同期です。 Blazor WebAssembly アプリで同期メソッドを呼び出すと、アプリが応答しなくなります。 Blazor WebAssembly では、常に非同期メソッドを使用する必要があります。

## <a name="additional-resources"></a>その他の技術情報

* [Web クライアント用 gRPC の GitHub プロジェクト](https://github.com/grpc/grpc-web)
* <xref:security/cors>
