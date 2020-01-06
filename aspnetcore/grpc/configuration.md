---
title: gRPC for .NET 構成
author: jamesnk
description: .NET アプリ用に gRPC を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 09/05/2019
uid: grpc/configuration
ms.openlocfilehash: de478752f94713d7f3d55ed66e6410500c3a72e8
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355730"
---
# <a name="grpc-for-net-configuration"></a>gRPC for .NET 構成

## <a name="configure-services-options"></a>サービスオプションの構成

gRPC サービスは、 *Startup.cs*の `AddGrpc` で構成されます。 次の表では、gRPC サービスを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | サーバーから送信できる最大メッセージサイズ (バイト単位)。 構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。 |
| `MaxReceiveMessageSize` | 4 MB | サーバーが受信できる最大メッセージサイズ (バイト単位)。 サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。 この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。 |
| `EnableDetailedErrors` | `false` | `true`すると、サービスメソッドで例外がスローされた場合に、詳細な例外メッセージがクライアントに返されます。 既定値は、 `false`です。 `EnableDetailedErrors` を `true` に設定すると、機密情報が漏洩する可能性があります。 |
| `CompressionProviders` | gzip | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。 カスタム圧縮プロバイダーを作成してコレクションに追加することができます。 既定で構成されているプロバイダーは、 **gzip**圧縮をサポートしています。 |
| `ResponseCompressionAlgorithm` | `null` | サーバーから送信されたメッセージの圧縮に使用される圧縮アルゴリズム。 アルゴリズムは、`CompressionProviders`の圧縮プロバイダーと一致している必要があります。 アルゴリズムで応答を圧縮するには、クライアントは、 **grpc-accept-encoding**ヘッダーでアルゴリズムを送信することによって、アルゴリズムをサポートしていることを示す必要があります。 |
| `ResponseCompressionLevel` | `null` | サーバーから送信されたメッセージの圧縮に使用される圧縮レベルです。 |
| `Interceptors` | [なし] | 各 gRPC 呼び出しで実行されるインターセプターのコレクション。 インターセプターは、登録されている順序で実行されます。 グローバルに構成されたインターセプターは、1つのサービスに対してインターセプターが構成される前に実行されます。 GRPC インターセプターの詳細については、「 [Grpc インターセプターとミドルウェア](xref:grpc/migration#grpc-interceptors-vs-middleware)」を参照してください。 |

オプションは、`Startup.ConfigureServices`の `AddGrpc` 呼び出しに委任オプションを指定することで、すべてのサービスに対して構成できます。

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

1つのサービスのオプションは、`AddGrpc` で提供されるグローバルオプションを上書きし、`AddServiceOptions<TService>`を使用して構成できます。

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>クライアントオプションを構成する

gRPC クライアント構成は `GrpcChannelOptions`に設定されています。 次の表では、gRPC チャネルを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `HttpClient` | 新しいインスタンス | GRPC 呼び出しを行うために使用する `HttpClient`。 クライアントを設定して、カスタム `HttpClientHandler`を構成したり、gRPC 呼び出しの HTTP パイプラインにハンドラーを追加したりすることができます。 `HttpClient` が指定されていない場合は、チャネルに対して新しい `HttpClient` インスタンスが作成されます。 自動的に破棄されます。 |
| `DisposeHttpClient` | `false` | `true`、`HttpClient` が指定されている場合、`GrpcChannel` が破棄されると `HttpClient` インスタンスは破棄されます。 |
| `LoggerFactory` | `null` | GRPC 呼び出しに関する情報をログに記録するためにクライアントによって使用される `LoggerFactory`。 `LoggerFactory` インスタンスは、依存関係の挿入から解決することも、`LoggerFactory.Create`を使用して作成することもできます。 ログ記録を構成する例については、「<xref:grpc/diagnostics#grpc-client-logging>」を参照してください。 |
| `MaxSendMessageSize` | `null` | クライアントから送信できる最大メッセージサイズ (バイト単位)。 構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。 |
| `MaxReceiveMessageSize` | 4 MB | クライアントが受信できる最大メッセージサイズ (バイト単位)。 クライアントがこの制限を超えるメッセージを受信すると、例外がスローされます。 この値を大きくすると、クライアントはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を及ぼす可能性があります。 |
| `Credentials` | `null` | `ChannelCredentials` のインスタンス。 資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用されます。 |
| `CompressionProviders` | gzip | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。 カスタム圧縮プロバイダーを作成してコレクションに追加することができます。 既定で構成されているプロバイダーは、 **gzip**圧縮をサポートしています。 |

コード例を次に示します。

* チャネルの送信メッセージと受信メッセージの最大サイズを設定します。
* クライアントを作成します。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
