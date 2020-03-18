---
title: HTTP API を使用した gRPC サービスの比較
author: jamesnk
description: gRPC と HTTP API を比較し、どのようなシナリオが推奨されるかを説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 8935e665dfd5d8f9afa002f475c202ec0f0ee657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650792"
---
# <a name="compare-grpc-services-with-http-apis"></a>HTTP API を使用した gRPC サービスの比較

作成者: [James Newton-King](https://twitter.com/jamesnk)

この記事では、HTTP API (ASP.NET Core [Web API](xref:web-api/index) を含む) と対比させて [gRPC サービス](https://grpc.io/docs/guides/)について説明します。 アプリに API を提供するために使用するテクノロジは重要な選択であり、gRPC は、HTTP API と比較して特有の利点を備えています。 この記事では、gRPC の長所と短所について説明し、他のテクノロジよりも gRPC を使用するのが推奨されるシナリオを示します。

## <a name="high-level-comparison"></a>比較の概要

次の表は、gRPC と、JSON を使用する HTTP API の機能を比較した概要を示しています。

| 機能          | gRPC                                               | JSON を使用する HTTP API           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| コントラクト         | 必須 ( *.proto*)                                | オプション (OpenAPI)            |
| プロトコル         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (小、バイナリ)](#performance)           | JSON (大、人が判読できる)  |
| 規範的性質 | [厳密な仕様](#strict-specification)      | 制約が緩い。 どのような HTTP でも有効です。     |
| ストリーム        | [クライアント、サーバー、双方向](#streaming)       | クライアント、サーバー                |
| ブラウザーのサポート  | [いいえ (grpc-web が必要)](#limited-browser-support) | はい                           |
| セキュリティ         | トランスポート (TLS)                                    | トランスポート (TLS)               |
| クライアント コード生成 | [はい](#code-generation)                      | OpenAPI とサードパーティ製ツール |

## <a name="grpc-strengths"></a>gRPC の長所

### <a name="performance"></a>パフォーマンス

gRPC メッセージは、効率的なバイナリ メッセージ形式である [Protobuf](https://developers.google.com/protocol-buffers/docs/overview) を使用してシリアル化されます。 Protobuf はサーバーおよびクライアント上で、非常に高速にシリアル化を行います。 Protobuf のシリアル化では、メッセージ ペイロードが小さくなります。これはモバイル アプリのように帯域幅が限られるシナリオでは重要です。

gRPC は、HTTP のメジャー リビジョンである HTTP/2 向けに設計されており、パフォーマンスは HTTP 1.x よりも大幅に向上します。

* バイナリ フレームと圧縮。 HTTP/2 プロトコルは、送信と受信の両方においてコンパクトかつ効率的です。
* 単一 TCP 接続での複数の HTTP/2 呼び出しの多重化。 多重化により、[ヘッドオブライン ブロッキング](https://en.wikipedia.org/wiki/Head-of-line_blocking)が解消されます。

### <a name="code-generation"></a>コード生成

すべての gRPC フレームワークで、コード生成は最高レベルでサポートされています。 gRPC 開発の中核となるファイルは [.proto ファイル](https://developers.google.com/protocol-buffers/docs/proto3)であり、そこで gRPC のサービスとメッセージのコントラクトが定義されます。 このファイルから、gRPC フレームワークによって、サービス基本クラス、メッセージ、および完全なクライアントがコード生成されます。

サーバーとクライアントの間で *.proto* ファイルを共有すれば、端から端までメッセージとクライアント コードを生成できます。 クライアントのコード生成によって、クライアントとサーバー上でメッセージが重複しなくなり、厳密に型指定されたクライアントが自動的に作成されます。 クライアントを記述する必要がないので、多くのサービスを持つアプリケーションの開発時間が大幅に節約されます。

### <a name="strict-specification"></a>厳密な仕様

JSON を使用する HTTP API の公式仕様は存在しません。 URL、HTTP 動詞、および応答コードの最適な形式については、開発者が議論します。

[gRPC 仕様](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)では、gRPC サービスが従う必要がある形式の規範が示されています。 gRPC はプラットフォームや実装の全体にわたって一貫性があるため、議論は不要であり、開発者の時間が節約されます。

### <a name="streaming"></a>ストリーム

HTTP/2 では、有効期間が長いリアルタイム通信ストリームの基盤が提供されます。 gRPC では、HTTP/2 を介したストリーミングが最高レベルでサポートされています。

gRPC サービスでは、ストリーミングのすべての組み合わせがサポートされます。

* Unary (ストリーミングなし)
* サーバーからクライアントへのストリーミング
* クライアントからサーバーへのストリーミング
* 双方向ストリーミング

### <a name="deadlinetimeouts-and-cancellation"></a>期限/タイムアウトとキャンセル

gRPC を使用すると、クライアントが RPC の完了を待機する時間を指定できます。 [期限](https://grpc.io/blog/deadlines)がサーバーに送信され、期限を越えた場合にどのようなアクションを実行するかをサーバーで決定できます。 たとえば、タイムアウト時に、進行中の gRPC/HTTP/データベース要求をサーバーでキャンセルする場合があります。

子の gRPC 呼び出しを通して期限やキャンセルを伝達すると、リソース使用量の制限を適用する助けとなります。

## <a name="grpc-recommended-scenarios"></a>gRPC での推奨されるシナリオ

gRPC は以下のシナリオに適しています。

* **マイクロサービス** &ndash; gRPC は、待機時間が短く、スループットの高い通信のために設計されています。 gRPC は、効率が非常に重要となる軽量のマイクロサービスに適しています。
* **ポイント ツー ポイントのリアルタイム通信** &ndash; gRPC での双方向ストリーミングのサポートは非常に優秀です。 gRPC サービスでは、ポーリングせずにメッセージをリアルタイムにプッシュできます。
* **多言語環境** &ndash; gRPC ツールでは、一般的な開発言語がすべてサポートされているため、gRPC は多言語環境に適した選択肢となっています。
* **ネットワーク面の制約がある環境** &ndash; gRPC メッセージは、軽量なメッセージ形式である Protobuf でシリアル化されます。 gRPC メッセージは常に、同等の JSON メッセージよりも小さくなります。

## <a name="grpc-weaknesses"></a>gRPC の短所

### <a name="limited-browser-support"></a>ブラウザーのサポートが限定的

現在のところ、ブラウザーから gRPC サービスを直接呼び出すことはできません。 gRPC は HTTP/2 の機能を多用しており、gRPC クライアントをサポートするための Web 要求を通して必要とされるレベルの制御を提供するブラウザーがありません。 たとえば、ブラウザーでは呼び出し元に、HTTP/2 を使用するよう求めたり、基になる HTTP/2 フレームへのアクセスを提供したりすることを許可していません。

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) は、ブラウザーで限定的な gRPC サポートを実現する、gRPC チームが提供する追加のテクノロジです。 gRPC-Web は、最新のブラウザーをすべてサポートする JavaScript クライアントと、サーバー上の gRPC-Web プロキシの2つの部分で構成されています。 gRPC-Web クライアントはプロキシを呼び出し、プロキシは gRPC 要求を gRPC サーバーに転送します。

gRPC のすべての機能が gRPC-Web でサポートされているわけではありません。 クライアントと双方向ストリーミングはサポートされておらず、サーバー ストリーミングのサポートは限定的です。

### <a name="not-human-readable"></a>人が判読できない

HTTP API 要求はテキストとして送信され、人が読み取りと作成を行えます。

gRPC メッセージは、既定では Protobuf でエンコードされます。 Protobuf での送受信は効率的ですが、そのバイナリ形式は人が判読できません。 Protobuf では、正しく逆シリアル化するために、 *.proto* ファイルで指定されているメッセージのインターフェイスに関する説明を必要とします。 ネットワーク上の Protobuf ペイロードを分析し、手動で要求を作成するには、追加のツールが必要です。

バイナリの Protobuf メッセージをサポートするため、[サーバー リフレクション](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)や [gRPC コマンド ライン ツール](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)などの機能が存在します。 また、Protobuf メッセージでは、[JSON との間の変換](https://developers.google.com/protocol-buffers/docs/proto3#json)がサポートされています。 組み込みの JSON 変換によって、デバッグ時に Protobuf メッセージを人が判読できる形式に変換する効率的な方法が提供されます。

## <a name="alternative-framework-scenarios"></a>他のフレームワークのシナリオ

以下のシナリオでは、gRPC よりも他のフレームワークが推奨されます。

* **ブラウザーでアクセスできる API** &ndash; gRPC は、ブラウザーで完全にサポートされているわけではありません。 gRPC-Web でブラウザーのサポートを提供可能ですが、制限があり、サーバー プロキシが必要になります。
* **ブロードキャスト リアルタイム通信** &ndash; gRPC ではストリーミングによるリアルタイム通信がサポートされますが、登録済みの接続にメッセージをブロードキャストするという概念がありません。 たとえば、チャット ルーム内のすべてのクライアントに新しいチャット メッセージを送信する必要があるチャット ルーム シナリオでは、クライアントに新しいチャット メッセージを個々にストリーミングするため、それぞれに gRPC 呼び出しが必要です。 [SignalR](xref:signalr/introduction) は、このシナリオの場合に便利なフレームワークです。 SignalR には永続的な接続という概念があり、メッセージをブロードキャストするためのサポートが組み込まれています。
* **プロセス間通信** &ndash; 受信 gRPC 呼び出しを受け入れるため、プロセスで HTTP/2 サーバーをホストする必要があります。 Windows の場合、プロセス間通信[パイプ](/dotnet/standard/io/pipe-operations)が高速で軽量な通信方法です。

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
