---
title: dotnet-grpc を使用して Protobuf 参照を管理する
author: juntaoluo
description: dotnet-grpc グローバル ツールを使用して Protobuf 参照の追加、更新、削除、および一覧表示を行うことについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650900"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>dotnet-grpc を使用して Protobuf 参照を管理する

作成者: [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` は、.NET gRPC プロジェクト内の [Protobuf ( *.proto*)](xref:grpc/basics#proto-file) 参照を管理するための .NET Core グローバル ツールです。 このツールを使用して、Protobuf 参照の追加、更新、削除、および一覧表示を行うことができます。

## <a name="installation"></a>インストール

`dotnet-grpc` [.NET Core グローバル ツール](/dotnet/core/tools/global-tools)をインストールするには、次のコマンドを実行します。

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>参照の追加

`dotnet-grpc` を使用し、`<Protobuf />` 項目として Protobuf 参照を *.csproj* ファイルに追加できます。

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf 参照は、C# クライアントやサーバーの資産を生成するために使用されます。 `dotnet-grpc` ツールは以下のことが可能です。

* ディスク上のローカル ファイルから Protobuf 参照を作成します。
* URL で指定されたリモート ファイルから Protobuf 参照を作成します。
* プロジェクトに正しい gRPC パッケージの依存関係が追加されていることを確認します。

たとえば、`Grpc.AspNetCore` パッケージは Web アプリに追加されます。 `Grpc.AspNetCore` には、gRPC のサーバーおよびクライアントのライブラリと、ツールのサポートが含まれています。 または、gRPC クライアント ライブラリとツールのサポートのみが含まれる `Grpc.Net.Client`、`Grpc.Tools`、および `Google.Protobuf` パッケージは、コンソール アプリに追加されます。

### <a name="add-file"></a>ファイルの追加

`add-file` コマンドは、Protobuf 参照としてディスクにローカル ファイルを追加するために使用します。 指定するファイル パス:

* 現在のディレクトリに対する相対パスでも絶対パスでもかまいません。
* パターン ベースのファイル [glob](https://wikipedia.org/wiki/Glob_(programming)) のためにワイルド カードを含めることができます。

いずれかのファイルがプロジェクト ディレクトリの外部にある場合、Visual Studio で `Protos` フォルダーの下にファイルを表示するために `Link` 要素が追加されます。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| ファイル | protobuf ファイルの参照。 ローカル protobuf ファイルの場合は、glob へのパスを指定できます。 |

#### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクト ファイルへのパス。 ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。
| -S | --services | 生成する必要がある gRPC サービスの種類。 `Default` が指定された場合、Web プロジェクトには `Both` が使用され、Web プロジェクト以外には `Client` が使用されます。 指定できる値は、`Both`、`Client`、`Default`、`None`、`Server` です。
| -i | --additional-import-dirs | protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。 これはセミコロンで区切られたパスの一覧です。
| | --access | 生成される C# クラスに使用するアクセス修飾子。 既定値は `Public` です。 指定できる値は、`Internal` と `Public` です。

### <a name="add-url"></a>URL の追加

`add-url` コマンドは、ソース URL で指定されたリモート ファイルを Protobuf 参照として追加するために使用します。 ファイル パスを指定して、リモート ファイルをダウンロードする場所を指定する必要があります。 ファイル パスは、現在のディレクトリに対する相対パスでも絶対パスでもかまいません。 ファイル パスがプロジェクト ディレクトリの外部にある場合、Visual Studio で `Protos` 仮想フォルダーの下にファイルを表示するために `Link` 要素が追加されます。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| url | リモート protobuf ファイルへの URL。 |

#### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -o | --output | リモート protobuf ファイルのダウンロード パスを指定します。 これは必須オプションです。
| -p | --project | 操作するプロジェクト ファイルへのパス。 ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。
| -S | --services | 生成する必要がある gRPC サービスの種類。 `Default` が指定された場合、Web プロジェクトには `Both` が使用され、Web プロジェクト以外には `Client` が使用されます。 指定できる値は、`Both`、`Client`、`Default`、`None`、`Server` です。
| -i | --additional-import-dirs | protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。 これはセミコロンで区切られたパスの一覧です。
| | --access | 生成される C# クラスに使用するアクセス修飾子。 既定値は `Public`にする必要があります。 指定できる値は、`Internal` と `Public` です。

## <a name="remove"></a>削除

`remove` コマンドは、 *.csproj* ファイルから Protobuf 参照を削除するために使用します。 このコマンドでは、引数としてパス引数とソース URL を受け取ります。 ツール:

* Protobuf 参照のみが削除されます。
* 元はリモート URL からダウンロードされた場合でも *.proto* ファイルは削除されません。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| 参照 | 削除する protobuf 参照の URL またはファイル パス。 |

### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクト ファイルへのパス。 ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。

## <a name="refresh"></a>最新の情報に更新

`refresh` コマンドは、ソース URL の最新のコンテンツを使用してリモート参照を更新するために使用します。 ダウンロード ファイル パスとソース URL の両方を使用して、更新する参照を指定できます。 メモ:

* ローカル ファイルを更新する必要があるかどうかを特定するために、ファイル コンテンツのハッシュが比較されます。
* タイムスタンプ情報は比較されません。

更新が必要な場合、ツールの実行により、常にローカル ファイルがリモート ファイルに置き換えられます。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>引数

| 引数 | 説明 |
|-|-|
| 参照 | 更新する必要があるリモート protobuf 参照への URL またはファイル パス。 この引数を空のままにすると、すべてのリモート参照が更新されます。 |

### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクト ファイルへのパス。 ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。
| | --dry-run | どの新しいコンテンツもダウンロードせずに更新されるファイルの一覧を出力します。

## <a name="list"></a>リスト

`list` コマンドは、プロジェクト ファイル内のすべての Protobuf 参照を表示するために使用します。 列のすべての値が既定値であると、列が省略されることがあります。

### <a name="usage"></a>使用方法

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>オプション

| 短いオプション | 長いオプション | 説明 |
|-|-|-|
| -p | --project | 操作するプロジェクト ファイルへのパス。 ファイルが指定されていない場合、コマンドを実行すると現在のディレクトリが検索されます。

## <a name="additional-resources"></a>その他の技術情報

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
