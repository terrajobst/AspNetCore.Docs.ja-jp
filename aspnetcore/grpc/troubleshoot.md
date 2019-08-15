---
title: .NET Core での gRPC のトラブルシューティング
author: jamesnk
description: .NET Core で gRPC を使用しているときのエラーのトラブルシューティングを行います。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029035"
---
# <a name="troubleshoot-grpc-on-net-core"></a>.NET Core での gRPC のトラブルシューティング

[James のニュートン-キング](https://twitter.com/jamesnk)別

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>クライアントとサービスの SSL/TLS の構成が一致しません

GRPC テンプレートとサンプルでは、[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用して、既定で grpc サービスをセキュリティで保護しています。 gRPC クライアントはセキュリティで保護された接続を使用して、セキュリティで保護された gRPC サービスを正常に呼び出す必要があります。

アプリの起動時に書き込まれたログで、ASP.NET Core gRPC サービスが TLS を使用していることを確認できます。 サービスは、HTTPS エンドポイントでリッスンします。

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

.Net Core クライアントは、サーバー `https`アドレスでを使用して、セキュリティで保護された接続で呼び出しを行う必要があります。

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

すべての gRPC クライアント実装は TLS をサポートしています。 他の言語の gRPC クライアントには、通常、 `SslCredentials`で構成されたチャネルが必要です。 `SslCredentials`クライアントが使用する証明書を指定します。この証明書は、セキュリティで保護されていない資格情報の代わりに使用する必要があります。 異なる gRPC クライアント実装で TLS を使用するように構成する例については、「 [Grpc 認証](https://www.grpc.io/docs/guides/auth/)」を参照してください。

## <a name="call-insecure-grpc-services-with-net-core-client"></a>.NET Core クライアントを使用してセキュリティで保護される gRPC サービスを呼び出す

.NET Core クライアントでセキュリティで保護されていない gRPC サービスを呼び出すには、追加の構成が必要です。 Grpc クライアントでは、 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`スイッチをに`true`設定し、サーバーアドレスでを使用`http`する必要があります。

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>MacOS で gRPC アプリを開始できません ASP.NET Core

Kestrel では、macOS での TLS と Windows 7 などの古いバージョンの HTTP/2 はサポートされていません。 ASP.NET Core gRPC テンプレートとサンプルでは、既定で TLS が使用されます。 GRPC サーバーを起動しようとすると、次のエラーメッセージが表示されます。

> IPv4 ループバックインターフェイスの https://localhost:5001 にバインドできません。' HTTP/2 over TLS は、ALPN サポートがないため、macOS ではサポートされていません。 '。

この問題を回避するには、TLS を使用*せず*に HTTP/2 を使用するように Kestrel と grpc クライアントを構成します。 これは開発時にのみ実行してください。 TLS を使用しないと、gRPC メッセージが暗号化なしで送信されます。

Kestrel では、 *Program.cs*で TLS を使用せずに HTTP/2 エンドポイントを構成する必要があります。

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

GRPC クライアントは、TLS を使用しないように構成する必要もあります。 詳細については、「 [.Net Core クライアントを使用した安全でない gRPC サービスの呼び出し](#call-insecure-grpc-services-with-net-core-client)」を参照してください。

> [!WARNING]
> TLS を使用しない HTTP/2 は、アプリの開発中にのみ使用してください。 運用アプリでは、常にトランスポートセキュリティを使用する必要があります。 詳細については、「 [gRPC のセキュリティに関する考慮事項 ASP.NET Core](xref:grpc/security#transport-security)」を参照してください。

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>grpc C#資産は、  *\*proto*ファイルから生成されたコードではありません

具象クライアントとサービス基底クラスの gRPC コード生成には、protobuf ファイルとツールをプロジェクトから参照する必要があります。 次のものを含める必要があります。

* `<Protobuf>`項目グループで使用する*プロトコルファイル。* [インポートさ*れたプロトコル*ファイル](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)は、プロジェクトによって参照される必要があります。
* GRPC ツールパッケージ[grpc](https://www.nuget.org/packages/Grpc.Tools/)に対するパッケージリファレンス。

GRPC C#アセットの生成の詳細について<xref:grpc/basics>は、「」を参照してください。

既定では、 `<Protobuf>`参照によって具象クライアントとサービス基本クラスが生成されます。 参照要素の属性`GrpcServices`は、資産の生成をC#制限するために使用できます。 有効`GrpcServices`なオプションは次のとおりです。

* `Both`(存在しない場合の既定値)
* `Server`
* `Client`
* `None`

GRPC サービスをホストしている ASP.NET Core web アプリには、生成されたサービス基本クラスのみが必要です。

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

GRPC 呼び出しを行う gRPC クライアントアプリでは、具象クライアントのみが生成されます。

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```
