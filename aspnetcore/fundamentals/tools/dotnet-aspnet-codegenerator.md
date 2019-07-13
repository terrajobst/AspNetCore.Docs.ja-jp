---
title: dotnet aspnet-codegenerator コマンド
author: rick-anderson
description: dotnet aspnet-codegenerator コマンドでは、ASP.NET Core プロジェクトがスキャフォールディングされます。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: c96362f320efd84c35dc07294a2968a2c687ee94
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596137"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` - ASP.NET Core のスキャフォールディング エンジンを実行します。 `dotnet aspnet-codegenerator` が必要になるのは、コマンド ラインからスキャフォールディングする場合のみです。Visual Studio でスキャフォールディングする場合は必要ありません。

この記事は [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) 以降に適用されます。

## <a name="installing-aspnet-codegenerator"></a>aspnet-codegenerator のインストール

`dotnet-aspnet-codegenerator` は[グローバル ツール](/dotnet/core/tools/global-tools)であり、インストールする必要があります。 次のコマンドを使うと、`dotnet-aspnet-codegenerator` ツールの最新の安定バージョンをインストールできます。

```console
dotnet tool install -g dotnet-aspnet-codegenerator
```

次のコマンドを使うと、インストール済みの .NET Core SDK から利用できる更新の安定バージョンに `dotnet-aspnet-codegenerator` を更新できます。

```console
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>構文

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>説明

`dotnet aspnet-codegenerator` グローバル コマンドを使うと、ASP.NET Core のコード ジェネレーターとスキャフォールディング エンジンが実行されます。

## <a name="arguments"></a>引数

`generator`

実行するコード ジェネレーター。 次のジェネレーターを使用できます。

| Generator | 操作 |
| ----------------- | ------------ | 
| area      | [区分のスキャフォールディング](/aspnet/core/mvc/controllers/areas) |
  コントローラー| [コントローラーのスキャフォールディング](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  identity  | [Identity のスキャフォールディング](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Razor Pages のスキャフォールディング](/aspnet/core/tutorials/razor-pages/model) |
  view      | [ビューのスキャフォールディング](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>オプション

`-n|--nuget-package-dir`

NuGet パッケージのディレクトリを指定します。

`-c|--configuration {Debug|Release}`

ビルド構成を定義します。 既定値は `Debug` です。

`-tfm|--target-framework`

使用するターゲット [フレームワーク](/dotnet/standard/frameworks)です。 たとえば、`net46` のようにします。

`-b|--build-base-path`

ビルドのベース パスです。

`-h|--help`

コマンドの短いヘルプを印刷します。

`--no-build`

実行前にプロジェクトをビルドしません。 また、`--no-restore` フラグが暗黙的に設定されます。

`-p|--project <PATH>`

実行するプロジェクト ファイルのパスを指定します (フォルダー名または完全なパス)。 指定しない場合は、既定で現在のディレクトリに設定されます。

## <a name="generator-options"></a>ジェネレーターのオプション

以下のセクションでは、次のサポートされているジェネレーターで使用できるオプションについて詳しく説明します。

* 区分
* コントローラー
* Identity  
* Razorpage
* 表示

<a name="area"></a>

### <a name="area-options"></a>Area のオプション

このツールは、コントローラーとビューを含む ASP.NET Core Web プロジェクト用のものです。 Razor Pages アプリ用ではありません。

使用方法: `dotnet aspnet-codegenerator area AreaNameToGenerate`

上のコマンドを実行すると、次のフォルダーが生成されます。

* *領域*
  * *AreaNameToGenerate*
    * *コントローラー*
    * *データ*
    * *Models*
    * *ビュー*

<a name="ctl"></a>

### <a name="controller-options"></a>Controller のオプション

`aspnet-codegenerator` `controller` および `razorpage` 用のオプションの一覧を次の表に示します。

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

`aspnet-codegenerator controller` に固有のオプションの一覧を次の表に示します。

| オプション               | 説明|
| ----------------- | ------------ |
| --controllerName または -name | コントローラーの名前です。 |
| --useAsyncActions または -async | 非同期コントローラー アクションを生成します。 |
| --noViews または -nv | ビューを生成**しません**。 |
| --restWithNoViews または -api  | REST スタイルの API でコントローラーを生成します。 `noViews` を前提とし、ビュー関連のオプションはすべて無視されます。 |
| --readWriteActions または -actions | モデルなしで読み取り/書き込みアクションを備えたコントローラーを生成します。 |

`aspnet-codegenerator controller` コマンドに関するヘルプを取得するには、`-h` スイッチを使います。

```console
dotnet aspnet-codegenerator controller -h
```

`dotnet aspnet-codegenerator controller` の例については、[映画モデルのスキャフォールディング](/aspnet/core/tutorials/razor-pages/model)に関する記事をご覧ください。

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

新しいページの名前と使用するテンプレートを指定すれば、Razor Pages を個別にスキャフォールディングできます。 サポートされているテンプレートは次のとおりです。

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

たとえば、次のコマンドでは、編集テンプレートを使って *MyEdit.cshtml* と *MyEdit.cshtml.cs* が生成されます。

```console
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

通常、テンプレートと生成されたファイル名は指定されず、次のテンプレートが作成されます。

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

`aspnet-codegenerator` `razorpage` および `controller` 用のオプションの一覧を次の表に示します。

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

`aspnet-codegenerator razorpage` に固有のオプションの一覧を次の表に示します。

| オプション               | 説明|
| ----------------- | ------------ |
|   --namespaceName または -namespace | 生成された PageModel に対して使用する名前空間の名前です |
| --partialView または -partial | 部分ビューを生成します。 これを指定した場合、レイアウトのオプション -l と -udl は無視されます。 |
| --noPageModel または -npm | 空のテンプレートの PageModel クラスを生成しない動作に切り替えます |

`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得するには、`-h` スイッチを使います。

```console
dotnet aspnet-codegenerator razorpage -h
```

`dotnet aspnet-codegenerator razorpage` の例については、[映画モデルのスキャフォールディング](/aspnet/core/tutorials/razor-pages/model)に関する記事をご覧ください。

### <a name="identity"></a>Identity

[Identity のスキャフォールディング](/aspnet/core/security/authentication/scaffold-identity)に関する記事をご覧ください
