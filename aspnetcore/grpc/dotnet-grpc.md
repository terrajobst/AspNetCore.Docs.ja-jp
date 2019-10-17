---
title: dotnet-grpc を使用して Protobuf 参照を管理する
author: juntaoluo
description: Dotnet-grpc グローバルツールを使用した Protobuf 参照の追加、更新、削除、および一覧表示について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519038"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>dotnet-grpc を使用して Protobuf 参照を管理する

作成者: [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` は、.NET gRPC プロジェクト内の[Protobuf (*proto*)](xref:grpc/basics#proto-file)参照を管理するための .net Core グローバルツールです。 このツールを使用すると、Protobuf 参照の追加、更新、削除、および一覧表示を行うことができます。

## <a name="installation"></a>インストール

@No__t 0 [.Net Core グローバルツール](/dotnet/core/tools/global-tools)をインストールするには、次のコマンドを実行します。

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>参照の追加

`dotnet-grpc` を使用すると、 *.csproj*ファイルに `<Protobuf />` 項目として Protobuf 参照を追加できます。

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf 参照は、 C#クライアントまたはサーバーの資産を生成するために使用されます。 @No__t 0 のツールでは、次のことができます。

* ディスク上のローカルファイルから Protobuf 参照を作成します。
* URL で指定されたリモートファイルから Protobuf 参照を作成します。
* 正しい gRPC パッケージの依存関係がプロジェクトに追加されていることを確認します。

たとえば、@no__t 0 のパッケージが web アプリに追加されます。 `Grpc.AspNetCore` には、gRPC サーバーとクライアントライブラリ、およびツールのサポートが含まれています。 また、gRPC クライアントライブラリとツールのサポートのみを含む、`Grpc.Net.Client`、`Grpc.Tools`、`Google.Protobuf` のパッケージがコンソールアプリに追加されます。

### <a name="add-file"></a>ファイルの追加

@No__t-0 コマンドを使用して、ディスク上のローカルファイルを Protobuf 参照として追加します。 指定されたファイルパス:

* 現在のディレクトリまたは絶対パスを基準とした相対パスを指定できます。
* パターンベースのファイル[グロビング](https://wikipedia.org/wiki/Glob_(programming))のワイルドカードを含めることができます。

ファイルがプロジェクトディレクトリの外部にある場合は、Visual Studio のフォルダー `Protos` の下にファイルを表示するために @no__t 0 要素が追加されます。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| ファイル | Protobuf ファイル参照。 ローカル protobuf ファイルの場合は、glob のパスを指定できます。 |

#### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクトファイルのパス。 ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。
| -s | --サービス | 生成する必要がある gRPC サービスの種類。 @No__t-0 が指定されている場合は、Web プロジェクトに対して `Both` が使用され、非 Web プロジェクトには `Client` が使用されます。 指定できる値は `Both`、`Client`、`Default`、`None`、`Server` です。
| -i | --追加-インポート-ディレクトリ | Protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。 これは、セミコロンで区切られたパスの一覧です。
| | --アクセス | 生成されC#たクラスに使用するアクセス修飾子。 既定値は `Public`です。 許容される値は `Internal` および `Public` です。

### <a name="add-url"></a>URL の追加

@No__t-0 コマンドを使用して、ソース URL で指定されたリモートファイルを Protobuf reference として追加します。 リモートファイルをダウンロードする場所を指定するには、ファイルパスを指定する必要があります。 ファイルパスは、現在のディレクトリまたは絶対パスを基準とした相対パスにすることができます。 ファイルパスがプロジェクトディレクトリの外部にある場合は、Visual Studio の仮想フォルダー `Protos` の下にファイルを表示するために @no__t 0 要素が追加されます。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| url | リモート protobuf ファイルの URL。 |

#### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -o | --出力 | リモート protobuf ファイルのダウンロードパスを指定します。 これは必須オプションです。
| -p | --project | 操作するプロジェクトファイルのパス。 ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。
| -s | --サービス | 生成する必要がある gRPC サービスの種類。 @No__t-0 が指定されている場合は、Web プロジェクトに対して `Both` が使用され、非 Web プロジェクトには `Client` が使用されます。 指定できる値は `Both`、`Client`、`Default`、`None`、`Server` です。
| -i | --追加-インポート-ディレクトリ | Protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。 これは、セミコロンで区切られたパスの一覧です。
| | --アクセス | 生成されC#たクラスに使用するアクセス修飾子。 既定値は `Public`にする必要があります。 許容される値は `Internal` および `Public` です。

## <a name="remove"></a>削除

@No__t-0 コマンドを使用して、 *.csproj*ファイルから Protobuf 参照を削除します。 コマンドは、引数としてパス引数とソース Url を受け取ります。 ツール:

* Protobuf 参照のみを削除します。
* は、最初にリモート URL からダウンロードされた場合でも、*プロトコル*ファイルを削除しません。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| 参照 | 削除する protobuf 参照の Url またはファイルパス。 |

### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクトファイルのパス。 ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。

## <a name="refresh"></a>最新の情報に更新

@No__t-0 コマンドは、ソース URL の最新のコンテンツを使用してリモート参照を更新するために使用されます。 ダウンロードファイルパスとソース URL の両方を使用して、更新する参照を指定できます。 メモ:

* ファイルの内容のハッシュは、ローカルファイルを更新する必要があるかどうかを判断するために比較されます。
* タイムスタンプ情報は比較されません。

更新が必要な場合、ツールは常にローカルファイルをリモートファイルに置き換えます。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| 参照 | 更新する必要があるリモート protobuf 参照への Url またはファイルパス。 すべてのリモート参照を更新するには、この引数を空のままにします。 |

### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクトファイルのパス。 ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。
| | --ドライ-実行 | 新しいコンテンツをダウンロードせずに更新されるファイルの一覧を出力します。

## <a name="list"></a>リスト

@No__t-0 コマンドは、プロジェクトファイル内のすべての Protobuf 参照を表示するために使用されます。 列のすべての値が既定値の場合、列は省略される可能性があります。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクトファイルのパス。 ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
