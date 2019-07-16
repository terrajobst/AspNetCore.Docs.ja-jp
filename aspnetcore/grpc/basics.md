---
title: C# を使用した gRPC サービス
author: juntaoluo
description: GRPC サービスを記述する場合は、基本的な概念を説明しますC#します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 78d744d641396c449a142375c69730333f8183cd
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223885"
---
# <a name="grpc-services-with-c"></a>C と gRPC サービス\#

このドキュメントでは、書き込みに必要な概念を示しています。 [gRPC](https://grpc.io/docs/guides/)アプリC#します。 ここで取り上げるトピックは、両方に適用[C core](https://grpc.io/blog/grpc-stacks)-gRPC のベースと ASP.NET Core ベースのアプリ。

## <a name="proto-file"></a>proto ファイル

gRPC は、API の開発をコントラクト優先のアプローチを使用します。 プロトコル バッファー (protobuf) は、既定ではインターフェイスのデザイン言語 (IDL) としてを使用します。 *.Proto*ファイルが含まれます。

* GRPC サービスの定義。
* クライアントとサーバー間で送信されるメッセージ。

Protobuf ファイルの構文の詳細については、次を参照してください。、[公式ドキュメント (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)します。

たとえば、 *greet.proto*ファイルで使用される[gRPC service の概要](xref:tutorials/grpc/grpc-start):

* 定義、`Greeter`サービス。
* `Greeter`サービスを定義、`SayHello`呼び出します。
* `SayHello` 送信、`HelloRequest`メッセージの送受信、`HelloReply`メッセージ。

[!code-protobuf[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>C に .proto ファイルを追加\#アプリ

*.Proto*プロジェクトに追加することによってファイルが含まれて、`<Protobuf>`項目グループ。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#.Proto ファイルのツールのサポート

ツール パッケージ[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)を生成するために必要なC#から資産 *.proto*ファイル。 生成された資産 (ファイル):

* ときに生成されます - 必要な場合に各プロジェクトをビルドします。
* プロジェクトに追加またはソース管理にチェックインされません。
* ビルドの成果物に含まれているは、 *obj*ディレクトリ。

このパッケージは、サーバーとクライアントの両方のプロジェクトで必要です。 `Grpc.Tools` Visual Studio でパッケージ マネージャーを使用または追加して追加することができます、`<PackageReference>`プロジェクト ファイル。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=15)]

ツール パッケージは実行時に不要であり、依存関係には `PrivateAssets="All"` のマークが付きます。

## <a name="generated-c-assets"></a>生成されたC#資産

ツール パッケージを生成、C#型が含まれるで定義されているメッセージを表す *.proto*ファイル。

サーバー側の資産に対して、サービスの抽象基本型が生成されます。 基本データ型には、gRPC の呼び出しに含まれるすべての定義が含まれています、 *.proto*ファイル。 この基本型から派生し、gRPC の呼び出しのロジックを実装するサービスの具象実装を作成します。 `greet.proto`、抽象前に説明した例では、`GreeterBase`バーチャル マシンを含む型`SayHello`メソッドが生成されます。 具象実装`GreeterService`メソッドをオーバーライドし、gRPC の呼び出しを処理するロジックを実装します。

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

クライアント側の資産に対してクライアントを具象型が生成されます。 呼び出し、gRPC、 *.proto*ファイルは、メソッドを呼び出すことができる、具象型に変換されます。 `greet.proto`、先ほどを具体的に説明した例では、`GreeterClient`型が生成されます。 呼び出す`GreeterClient.SayHello`gRPC の呼び出し、サーバーを開始します。

[!code-csharp[](~/tutorials//grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

既定では、サーバーとクライアントの資産を生成して、各 *.proto*ファイルに含まれる、`<Protobuf>`項目グループ。 サーバー資産のみがサーバー プロジェクトで生成されたことを確認する、`GrpcServices`属性に設定されて`Server`します。

[!code-xml[](~/tutorials//grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

同様に、属性に設定されて`Client`クライアント プロジェクトでします。

## <a name="additional-resources"></a>その他の資料

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
