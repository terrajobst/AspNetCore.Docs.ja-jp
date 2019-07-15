---
title: gRPC の ASP.NET Core の構成
author: jamesnk
description: GRPC の ASP.NET Core アプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/30/2019
uid: grpc/configuration
ms.openlocfilehash: e269d701f45c0b852a9006107f0162cc5af2c38a
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814926"
---
# <a name="grpc-for-aspnet-core-configuration"></a>gRPC の ASP.NET Core の構成

## <a name="configure-services-options"></a>サービスのオプションを構成します。

次の表では、gRPC サービスの構成オプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | サーバーから送信できるバイト単位で最大メッセージ サイズ。 例外で構成されている最大メッセージ サイズの結果を超えるメッセージを送信しようとしています。 |
| `ReceiveMaxMessageSize` | 4 MB | サーバーで受信できるをバイト単位で最大メッセージ サイズ。 サーバーは、この制限を超えるメッセージを受信した場合は、例外をスローします。 この値を増やすことによりより大きなメッセージを受信するサーバーが、メモリ消費量に悪影響を与えることができます。 |
| `EnableDetailedErrors` | `false` | 場合`true`、詳細なサービス メソッドで例外がスローされたときに、例外メッセージがクライアントに返されます。 既定値は `false` です。 設定`EnableDetailedErrors`に`true`機密情報が漏れることができます。 |
| `CompressionProviders` | gzip | 圧縮し、メッセージを圧縮解除に使用される圧縮プロバイダーのコレクション。 圧縮のカスタム プロバイダーを作成し、コレクションに追加できます。 既定値には、プロバイダーのサポートが構成されている**gzip**圧縮します。 |
| `ResponseCompressionAlgorithm` | `null` | 圧縮アルゴリズムは、サーバーから送信されたメッセージを圧縮するために使用します。 アルゴリズムで圧縮プロバイダーが一致する必要があります`CompressionProviders`します。 送信することによって、アルゴリズムをサポートの応答を圧縮する、アルゴリズムでは、クライアントが示す必要があります、 **grpc の同意のエンコード**ヘッダー。 |
| `ResponseCompressionLevel` | `null` | サーバーから送信されたメッセージを圧縮するために使用する圧縮レベルです。 |

オプションのデリゲートを提供することですべてのサービス オプションを構成でき、`AddGrpc`呼び出す`Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

1 つのサービスのオプションで提供されるグローバル オプションを上書き`AddGrpc`を使用して構成できると`AddServiceOptions<TService>`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>クライアント オプションを構成します。

次のコードでは、クライアントの最大送信を設定し、受信メッセージ サイズ。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-6)]

## <a name="additional-resources"></a>その他の資料

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
