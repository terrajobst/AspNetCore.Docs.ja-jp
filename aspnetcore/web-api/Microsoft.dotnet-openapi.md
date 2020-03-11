---
title: OpenAPI を使用した ASP.NET Core アプリの開発
author: ryanbrandenburg
description: Microsoft.dotnet-openapi ツールを使用して OpenAPI ファイルへの参照を追加する方法を説明します。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 079e36511b63c186ffa7726bdb1e3c3bcbda9d34
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651248"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>OpenAPI ツールを使用した ASP.NET Core アプリの開発

作成者: Ryan Brandenburg

[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) は、プロジェクト内での [OpenAPI](/dotnet/core/tools/global-tools) 参照を管理するための [.NET Core グローバル ツール](https://github.com/OAI/OpenAPI-Specification)です。

## <a name="installation"></a>インストール

`Microsoft.dotnet-openapi` をインストールするには次のコマンドを実行します。

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>追加

このページのコマンドのいずれかを使用して OpenAPI 参照を追加すると、次のような `<OpenApiReference />` 要素が *.csproj* ファイルに追加されます。

```xml
<OpenApiReference Include="openapi.json" />
```

前の参照は、生成されるクライアント コードをアプリが呼び出すために必要です。

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>ファイルの追加

#### <a name="options"></a>オプション

| 短いオプション| 長いオプション| 説明 | 例 |
|-------|------|-------|---------|
| -p|--updateProject | 操作対象のプロジェクト。 |dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--code-generator| 参照に適用するコード ジェネレーター。 オプションは `NSwagCSharp` と `NSwagTypeScript` です。 `--code-generator` を指定しない場合、ツールは既定で `NSwagCSharp` になります。|dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|ヘルプ情報を表示します。|dotnet openapi add file --help|

#### <a name="arguments"></a>引数

|  引数  | 説明 | 例 |
|-------------|-------------|---------|
| source-file | 参照の作成元のソース。 OpenAPI ファイルである必要があります。 |dotnet openapi add file *.\OpenAPI.json* |

### <a name="add-url"></a>URL の追加

#### <a name="options"></a>オプション

| 短いオプション| 長いオプション| 説明 | 例 |
|-------|------|-------------|---------|
| -p|--updateProject | 操作対象のプロジェクト。 |dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -o|--output-file | OpenAPI ファイルのローカル コピーを配置する場所。 |dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json* |
| -c|--code-generator| 参照に適用するコード ジェネレーター。 オプションは `NSwagCSharp` と `NSwagTypeScript` です。 |dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|ヘルプ情報を表示します。|dotnet openapi add url --help|

#### <a name="arguments"></a>引数

|  引数  | 説明 | 例 |
|-------------|-------------|---------|
| source-URL | 参照の作成元のソース。 URL である必要があります。 |dotnet openapi add url `https://contoso.com/openapi.json` |

## <a name="remove"></a>削除

指定したファイル名と一致する OpenAPI 参照を *.csproj* ファイルから削除します。 OpenAPI 参照が削除されると、クライアントは生成されません。 ローカルの *.json* および *.yaml* ファイルは削除されます。

### <a name="options"></a>オプション

| 短いオプション| 長いオプション| 説明| 例 |
|-------|------|------------|---------|
| -p|--updateProject | 操作対象のプロジェクト。 |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -h|--help|ヘルプ情報を表示します。|dotnet openapi remove --help|

### <a name="arguments"></a>引数

|  引数  | 説明| 例 |
| ------------|------------|---------|
| source-file | 削除する参照のソース。 |dotnet openapi remove *.\OpenAPI.json* |

## <a name="refresh"></a>更新

ダウンロード URL の最新のコンテンツを使用してダウンロードされたファイルのローカル バージョンを更新します。

### <a name="options"></a>オプション

| 短いオプション| 長いオプション| 説明 | 例 |
|-------|------|-------------|---------|
| -p|--updateProject | 操作対象のプロジェクト。 | dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -h|--help|ヘルプ情報を表示します。|dotnet openapi refresh --help|

### <a name="arguments"></a>引数

|  引数  | 説明 | 例 |
| ------------|-------------|---------|
| source-URL | 最新状態にする参照の URL。 | dotnet openapi refresh `https://contoso.com/openapi.json` |
