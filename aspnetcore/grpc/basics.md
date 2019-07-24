---
title: C# を使用した gRPC サービス
author: juntaoluo
description: で gRPC サービスをC#作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 700fe9463317f9ee30dfe4ebf5201c7b9c0c5ad6
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412475"
---
# <a name="grpc-services-with-c"></a>gRPC サービスと C\#

このドキュメントでは、でC# [grpc](https://grpc.io/docs/guides/)アプリを作成するために必要な概念の概要を説明します。 ここで説明するトピックは、 [C コア](https://grpc.io/blog/grpc-stacks)ベースと ASP.NET Core ベースの grpc アプリの両方に適用されます。

## <a name="proto-file"></a>プロトコルファイル

gRPC は、API 開発に対してコントラクト優先のアプローチを使用します。 プロトコルバッファー (protobuf) は、既定ではインターフェイスデザイン言語 (IDL) として使用されます。 *プロトコル*ファイルには次のものが含まれます。

* GRPC サービスの定義。
* クライアントとサーバーの間で送信されるメッセージ。

Protobuf ファイルの構文の詳細については、[公式のドキュメント (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)を参照してください。

たとえば、「 [gRPC サービスの](xref:tutorials/grpc/grpc-start)使用」で使用されている*あいさつ*ファイルについて考えてみます。

* サービスを`Greeter`定義します。
* サービス`Greeter`は呼び出しを`SayHello`定義します。
* `SayHello`メッセージを送信し、メッセージ`HelloReply`を受信します。 `HelloRequest`

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>プロトコルファイルを C\#アプリに追加する

*Proto*ファイルは、 `<Protobuf>`項目グループに追加することによってプロジェクトに含まれます。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>C#Proto ファイルのツールサポート

*ファイル*からC#アセットを生成するには、ツールパッケージ[grpc. ツール](https://www.nuget.org/packages/Grpc.Tools/)が必要です。 生成されたアセット (ファイル):

* は、プロジェクトがビルドされるたびに、必要に応じて生成されます。
* プロジェクトに追加されていないか、ソース管理にチェックインされていません。
* は、 *obj*ディレクトリに含まれるビルド成果物です。

このパッケージは、サーバープロジェクトとクライアントプロジェクトの両方で必要です。 メタパッケージ`Grpc.AspNetCore`には、へ`Grpc.Tools`の参照が含まれています。 サーバープロジェクトは、 `Grpc.AspNetCore` Visual Studio のパッケージマネージャーを使用するか、プロジェクト`<PackageReference>`ファイルにを追加することによって追加できます。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

クライアントプロジェクトは、 `Grpc.Tools`直接参照する必要があります。 ツールパッケージは実行時に必須ではないため、依存関係`PrivateAssets="All"`は次のようにマークされます。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=1&range=11)]

## <a name="generated-c-assets"></a>生成C#されたアセット

ツールパッケージは、含まC#れている*プロトコル*ファイルで定義されているメッセージを表す型を生成します。

サーバー側アセットの場合、抽象サービスの基本型が生成されます。 基本型には、 *proto*ファイルに含まれるすべての grpc 呼び出しの定義が含まれています。 この基本型から派生し、gRPC 呼び出しのロジックを実装する具象サービス実装を作成します。 では、前に説明した例で`GreeterBase`は、仮想`SayHello`メソッドを含む抽象型が生成されます。 `greet.proto` 具象実装`GreeterService`は、メソッドをオーバーライドし、grpc 呼び出しを処理するロジックを実装します。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

クライアント側の資産の場合は、具象的なクライアントの種類が生成されます。 *Proto*ファイル内の grpc 呼び出しは、具象型のメソッドに変換されます。これは、を呼び出すことができます。 前に説明した例では、具象`GreeterClient`型が生成されています。 `greet.proto` を`GreeterClient.SayHello`呼び出して、サーバーに対する grpc 呼び出しを開始します。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?highlight=3-6&name=snippet)]

既定では、サーバーとクライアントのアセットは、  `<Protobuf>`項目グループに含まれる各プロトコルファイルに対して生成されます。 サーバープロジェクト`GrpcServices`でサーバー資産のみが生成されるようにするには、属性を`Server`に設定します。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

同様に、クライアントプロジェクトでは`Client` 、属性はに設定されます。

## <a name="additional-resources"></a>その他の資料

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
