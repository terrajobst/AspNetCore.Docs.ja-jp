---
title: C# を使用した gRPC サービス
author: juntaoluo
description: で gRPC サービスをC#作成する際の基本的な概念について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 8d99d036fd4b00fc4568e67ea5225dc006dea4b1
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925183"
---
# <a name="grpc-services-with-c"></a>gRPC サービスと C\#

このドキュメントでは、でC# [grpc](https://grpc.io/docs/guides/)アプリを作成するために必要な概念の概要を説明します。 ここで説明するトピックは、 [C コア](https://grpc.io/blog/grpc-stacks)ベースと ASP.NET Core ベースの grpc アプリの両方に適用されます。

## <a name="proto-file"></a>プロトコルファイル

gRPC では、API 開発に対してコントラクト優先のアプローチが使われます。 プロトコルバッファー (protobuf) は、既定ではインターフェイスデザイン言語 (IDL) として使用されます。 *\*プロトコル*ファイルには次のものが含まれます。

* GRPC サービスの定義。
* クライアントとサーバーの間で送信されるメッセージ。

Protobuf ファイルの構文の詳細については、[公式のドキュメント (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3)を参照してください。

たとえば、「 [gRPC サービスの](xref:tutorials/grpc/grpc-start)使用」で使用されている*あいさつ*ファイルについて考えてみます。

* `Greeter` サービスを定義します。
* `Greeter` サービスは `SayHello` 呼び出しを定義します。
* `SayHello` は `HelloRequest` メッセージを送信し、`HelloReply` メッセージを受信します。

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a>プロトコルファイルを C\# アプリに追加する

*\** ファイルは、`<Protobuf>` 項目グループに追加することで、プロジェクトに含まれます。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>.proto ファイルに対する C# ツール サポート

ファイルからC# [アセットを生成するには、ツールパッケージ ](https://www.nuget.org/packages/Grpc.Tools/)Grpc.Tools *が必要\*です。* 生成されたアセット (ファイル):

* は、プロジェクトがビルドされるたびに、必要に応じて生成されます。
* プロジェクトに追加されていないか、ソース管理にチェックインされていません。
* は、 *obj*ディレクトリに含まれるビルド成果物です。

このパッケージは、サーバープロジェクトとクライアントプロジェクトの両方で必要です。 `Grpc.AspNetCore` メタパッケージには、`Grpc.Tools`への参照が含まれています。 サーバープロジェクトは、Visual Studio のパッケージマネージャーを使用するか、プロジェクトファイルに `<PackageReference>` を追加することによって、`Grpc.AspNetCore` を追加できます。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

クライアントプロジェクトは、gRPC クライアントを使用するために必要な他のパッケージと共に、`Grpc.Tools` 直接参照する必要があります。 ツールパッケージは実行時には必要ないため、依存関係は `PrivateAssets="All"`でマークされます。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>生成C#されたアセット

ツールパッケージは、含まC#れている *\*プロトコル*ファイルで定義されているメッセージを表す型を生成します。

サーバー側アセットの場合、抽象サービスの基本型が生成されます。 基本型には、 *proto*ファイルに含まれるすべての grpc 呼び出しの定義が含まれています。 この基本型から派生し、gRPC 呼び出しのロジックを実装する具象サービス実装を作成します。 `greet.proto`の場合、前述の例では、仮想 `SayHello` メソッドを含む抽象 `GreeterBase` 型が生成されます。 具象実装 `GreeterService`、メソッドをオーバーライドし、gRPC 呼び出しを処理するロジックを実装します。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

クライアント側の資産の場合は、具象的なクライアントの種類が生成されます。 *Proto*ファイル内の grpc 呼び出しは、具象型のメソッドに変換されます。これは、を呼び出すことができます。 `greet.proto`の場合、前述の例では、具体的な `GreeterClient` 型が生成されます。 `GreeterClient.SayHelloAsync` を呼び出して、サーバーに対する gRPC 呼び出しを開始します。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

既定では、`<Protobuf>` 項目グループに含まれる各 *\*プロトコル*に対して、サーバーとクライアントの資産が生成されます。 サーバープロジェクトでサーバー資産のみが生成されるようにするには、`GrpcServices` 属性を `Server`に設定します。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

同様に、属性はクライアントプロジェクトで `Client` に設定されます。

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>その他のリソース

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
