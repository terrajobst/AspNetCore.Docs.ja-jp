---
title: ASP.NET Core 構成用 gRPC
author: jamesnk
description: ASP.NET Core アプリ用に gRPC を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/21/2019
uid: grpc/configuration
ms.openlocfilehash: 34eb598211c87fbb2c68ae5e041da50d02f543f7
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310313"
---
# <a name="grpc-for-aspnet-core-configuration"></a>ASP.NET Core 構成用 gRPC

## <a name="configure-services-options"></a>サービスオプションの構成

次の表では、gRPC サービスを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `MaxSendMessageSize` | `null` | サーバーから送信できる最大メッセージサイズ (バイト単位)。 構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。 |
| `MaxReceiveMessageSize` | 4 MB | サーバーが受信できる最大メッセージサイズ (バイト単位)。 サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。 この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。 |
| `EnableDetailedErrors` | `false` | の`true`場合、サービスメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値は `false` です。 を`EnableDetailedErrors`に`true`設定すると、機密情報が漏洩する可能性があります。 |
| `CompressionProviders` | gzip、deflate | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。 カスタム圧縮プロバイダーを作成してコレクションに追加することができます。 既定で構成されているプロバイダーは、 **gzip**および**deflate**圧縮をサポートしています。 |
| `ResponseCompressionAlgorithm` | `null` | サーバーから送信されたメッセージの圧縮に使用される圧縮アルゴリズム。 アルゴリズムは、の`CompressionProviders`圧縮プロバイダーと一致している必要があります。 アルゴリズムで応答を圧縮するには、クライアントは、 **grpc-accept-encoding**ヘッダーでアルゴリズムを送信することによって、アルゴリズムをサポートしていることを示す必要があります。 |
| `ResponseCompressionLevel` | `null` | サーバーから送信されたメッセージの圧縮に使用される圧縮レベルです。 |

`AddGrpc` の`Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのサービスに対してオプションを構成できます。

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

1つのサービスのオプションは、で`AddGrpc`提供されるグローバルオプションを上書きします。また、次のものを使用し`AddServiceOptions<TService>`て構成できます。

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>クライアントオプションを構成する

次の表では、gRPC チャネルを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `HttpClient` | 新しいインスタンス | Grpc 呼び出しを行うために使用する。`HttpClient` クライアントを設定してカスタム`HttpClientHandler`を構成することも、grpc 呼び出し用に HTTP パイプラインにハンドラーを追加することもできます。 既定では、 `HttpClient`新しいインスタンスが作成されます。 |
| `MaxSendMessageSize` | `null` | クライアントから送信できる最大メッセージサイズ (バイト単位)。 構成された最大メッセージサイズを超えるメッセージを送信しようとすると、例外が発生します。 |
| `MaxReceiveMessageSize` | 4 MB | クライアントが受信できる最大メッセージサイズ (バイト単位)。 サーバーがこの制限を超えるメッセージを受信すると、例外がスローされます。 この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。 |
| `TransportOptions` | `null` | トランスポートオプションチャネルが gRPC サービスを呼び出す方法を構成します。 現在、唯一の実装`HttpClientTransport`オプションでは、 `HttpClient` grpc で使用するを指定できます。 |
| `Credentials` | `null` | `ChannelCredentials` のインスタンス。 資格情報は、認証メタデータを gRPC 呼び出しに追加するために使用されます。 |
| `CompressionProviders` | gzip、deflate | メッセージの圧縮と圧縮解除に使用される圧縮プロバイダーのコレクション。 カスタム圧縮プロバイダーを作成してコレクションに追加することができます。 既定で構成されているプロバイダーは、 **gzip**および**deflate**圧縮をサポートしています。 |

コード例を次に示します。

* チャネルの送信メッセージと受信メッセージの最大サイズを設定します。
* クライアントを作成します。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:tutorials/grpc/grpc-start>
