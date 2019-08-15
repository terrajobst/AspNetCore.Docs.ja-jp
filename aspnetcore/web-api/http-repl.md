---
title: HTTP REPL を使用して Web API をテストする
author: scottaddie
description: HTTP REPL .NET Core グローバル ツールを使用して、ASP.NET Core Web API を参照およびテストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/25/2019
uid: web-api/http-repl
ms.openlocfilehash: 0e80fcd76a4d3efcd35140c52e0f6f0ae0f27932
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862958"
---
# <a name="test-web-apis-with-the-http-repl"></a>HTTP REPL を使用して Web API をテストする

作成者: [Scott Addie](https://twitter.com/Scott_Addie)

HTTP Read-Eval-Print Loop (REPL) は:

* .NET Core がサポートされているすべての場所でサポートされている、軽量なクロスプラットフォーム コマンドライン ツールです。
* ASP.NET Core Web API (および ASP.NET 以外の Core Web API) をテストし、その結果を表示する HTTP 要求を作成するために使用されます。
* localhost や Azure App Service などの任意の環境でホストされている Web API をテストすることができます。

次の [HTTP 動詞](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)がサポートされています。

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [OPTIONS](#test-http-options-requests)
* [PATCH](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

先に進むには、[サンプル ASP.NET Core Web API を表示またはダウンロードします](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必須コンポーネント

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>インストール

HTTP REPL をインストールするには、次のコマンドを実行します。

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

[.Net Core グローバル ツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、[Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet パッケージからインストールされます。

## <a name="usage"></a>使用法

ツールのインストールが正常に完了したら、次のコマンドを実行して HTTP REPL を開始します。

```console
dotnet httprepl
```

使用可能な HTTP REPL コマンドを表示するには、次のコマンドのいずれかを実行します。

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

次のような出力が表示されます。

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Sets the swagger document to use for information about the current server
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

HTTP REPL では、コマンド補完が提供されています。 <kbd>Tab</kbd> キーを押すと、入力した文字または API エンドポイントを補完するコマンドの一覧が反復処理されます。 次のセクションでは、使用可能な CLI コマンドの概要を示します。

## <a name="connect-to-the-web-api"></a>Web API に接続する

次のコマンドを実行して、Web API に接続します。

```console
dotnet httprepl <BASE URI>
```

`<BASE URI>` は、Web API のベース URI です。 次に例を示します。

```console
dotnet httprepl https://localhost:5001
```

または、HTTP REPL の実行中に、次のコマンドをいつでも実行します。

```console
set base <BASE URI>
```

次に例を示します。

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a>Web API の Swagger ドキュメントをポイントする

Web API を適切に検査するには、相対 URI を Web API の Swagger ドキュメントに設定します。 次のコマンドを実行します。

```console
set swagger <RELATIVE URI>
```

次に例を示します。

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Web API 内を移動する

### <a name="view-available-endpoints"></a>使用可能なエンドポイントを表示する

Web API アドレスの現在のパスにあるさまざまなエンドポイント (コントローラー) を一覧表示するには、`ls` コマンドまたは `dir` コマンドを実行します。

```console
https://localhot:5001/~ ls
```

次の出力形式が表示されます。

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

前の出力では、`Fruits` と `People` の 2 つのコントローラーが使用可能であることが示されています。 どちらのコントローラーでも、パラメーターなしの HTTP GET 操作と POST 操作がサポートされます。

特定のコントローラーに移動すると、詳細がわかります。 たとえば、次のコマンドの出力は、`Fruits` コントローラーが HTTP GET、PUT、DELETE の各操作をサポートしていることを示しています。 これらの各操作には、ルートで `id` パラメーターが必要です。

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

または、`ui` コマンドを実行して、ブラウザーで Web API の Swagger UI ページを開きます。 次に例を示します。

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>エンドポイントに移動する

Web API の別のエンドポイントに移動するには、`cd` コマンドを実行します。

```console
https://localhost:5001/~ cd people
```

`cd` コマンドの後のパスでは大文字と小文字が区別されません。 次の出力形式が表示されます。

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>HTTP REPL をカスタマイズする

HTTP REPL の既定の[色](#set-color-preferences)はカスタマイズできます。 また、[既定のテキストエディター](#set-the-default-text-editor)を定義することもできます。 HTTP REPL の設定は、現在のセッションで永続化され、今後のセッションで受け入れられます。 変更した設定は、次のファイルに格納されます。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macostabmacos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

*.httpreplprefs* ファイルは起動時に読み込まれ、実行時の変更は監視されません。 ファイルに対する手動の変更は、ツールを再起動した後でのみ有効になります。

### <a name="view-the-settings"></a>設定を表示する

使用可能な設定を表示するには、`pref get` コマンドを実行します。 次に例を示します。

```console
https://localhost:5001/~ pref get
```

上記のコマンドでは、使用可能なキーと値のペアが表示されます。

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>色の設定を設定する

応答の色付けは、現在、JSON でのみサポートされています。 既定の HTTP REPL ツールの色分けをカスタマイズするには、変更する色に対応するキーを見つけます。 キーを検索する方法については、「[設定を表示する](#view-the-settings)」セクションを参照してください。 たとえば、次のように、`colors.json` キー値を `Green` から `White` に変更します。

```console
https://localhost:5001/people~ pref set colors.json White
```

使用できるのは、[許可されている色](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)だけです。 後続の HTTP 要求では、出力が新しい色で表示されます。

特定の色キーが設定されていない場合、より汎用的なキーが考慮されます。 このフォールバック動作を示すために、次の例を考えてみます。

* `colors.json.name` に値がない場合は、`colors.json.string` が使用されます。
* `colors.json.string` に値がない場合は、`colors.json.literal` が使用されます。
* `colors.json.literal` に値がない場合は、`colors.json` が使用されます。 
* `colors.json` 値がない場合は、コマンド シェルの既定のテキストの色 (`AllowedColors.None`) が使用されます。

### <a name="set-indentation-size"></a>インデントのサイズを設定する

応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。 既定のサイズは 2 つのスペースです。 次に例を示します。

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。 たとえば、常に 4 つのスペースを使用するには、次のようにします。

```console
pref set formatting.json.indentSize 4
```

後続の応答では、4 つのスペースの設定が優先されます。

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-indentation-size"></a>インデントのサイズを設定する

応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。 既定のサイズは 2 つのスペースです。 次に例を示します。

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。 たとえば、常に 4 つのスペースを使用するには、次のようにします。

```console
pref set formatting.json.indentSize 4
```

後続の応答では、4 つのスペースの設定が優先されます。

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>既定のテキスト エディターを設定する

既定では、HTTP REPL には、使用するように構成されたテキスト エディターはありません。 HTTP 要求本文を必要とする Web API メソッドをテストするには、既定のテキスト エディターを設定する必要があります。 HTTP REPL ツールでは、要求本文を作成する目的のためだけに構成されたテキスト エディターが起動されます。 次のコマンドを実行して、優先テキストエディターを既定値として設定します。

```console
pref set editor.command.default "<EXECUTABLE>"
```

上記のコマンドで、`<EXECUTABLE>` はテキスト エディターの実行可能ファイルへの完全なパスです。 たとえば、次のコマンドを実行して、既定のテキストエディターとして Visual Studio Code を設定します。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

特定の CLI 引数を使用して既定のテキスト エディターを起動するには、`editor.command.default.arguments` キーを設定します。 たとえば、Visual Studio Code が既定のテキスト エディターで、拡張機能が無効になっている新しいセッションでは HTTP REPL で常に Visual Studio Code を開きたいとします。 次のコマンドを実行します。

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a>HTTP GET 要求をテストする

### <a name="synopsis"></a>構文

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

`get` コマンドには以下のオプションを使用できます。

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>例

HTTP GET 要求を発行するには、次のようにします。

1. それをサポートしているエンドポイントで `get` コマンドを実行します。

    ```console
    https://localhost:5001/people~ get
    ```

    上記のコマンドでは、次の出力形式が表示されます。

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. `get` コマンドにパラメーターを渡すことによって、特定のレコードを取得します。

    ```console
    https://localhost:5001/people~ get 2
    ```

    上記のコマンドでは、次の出力形式が表示されます。

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a>HTTP POST 要求をテストする

### <a name="synopsis"></a>構文

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>例

HTTP POST 要求を発行するには、次のようにします。

1. それをサポートしているエンドポイントで `post` コマンドを実行します。

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。 既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。 次に例を示します。

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。

1. モデルの検証要件を満たすように JSON テンプレートを変更します。

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. *.tmp* ファイルを保存して、テキスト エディターを閉じます。 コマンド シェルに次の出力が表示されます。

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a>HTTP PUT 要求をテストする

### <a name="synopsis"></a>構文

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>例

HTTP PUT 要求を発行するには、次のようにします。

1. *省略可能*:変更前にデータを表示するには、`get` コマンドを実行します。

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。 既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。 次に例を示します。

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。

1. モデルの検証要件を満たすように JSON テンプレートを変更します。

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. *.tmp* ファイルを保存して、テキスト エディターを閉じます。 コマンド シェルに次の出力が表示されます。

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *省略可能*:変更を確認するには、`get` コマンドを実行します。 たとえば、テキスト エディターで「Cherry」と入力すると、`get` は次を返します。

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a>HTTP DELETE 要求をテストする

### <a name="synopsis"></a>構文

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>例

HTTP DELETE 要求を発行するには、次のようにします。

1. *省略可能*:変更前にデータを表示するには、`get` コマンドを実行します。

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    上記のコマンドでは、次の出力形式が表示されます。

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *省略可能*:変更を確認するには、`get` コマンドを実行します。 この例では、`get` は次を返します。

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a>HTTP PATCH 要求をテストする

### <a name="synopsis"></a>構文

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>HTTP HEAD 要求をテストする

### <a name="synopsis"></a>構文

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>HTTP OPTIONS 要求をテストする

### <a name="synopsis"></a>構文

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引数

`PARAMETER`

関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。

### <a name="options"></a>オプション

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>HTTP 要求ヘッダーを設定する

HTTP 要求ヘッダーを設定するには、次のいずれかの方法を使用します。

1. HTTP 要求でインラインを設定します。 次に例を示します。

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  上記の方法では、個別の HTTP 要求ヘッダーごとに独自の `-h` オプションが必要です。

1. HTTP 要求を送信する前に設定します。 次に例を示します。

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  要求を送信する前にヘッダーを設定すると、コマンド シェル セッションの間はヘッダーが設定されたままになります。 ヘッダーをクリアするには、空の値を指定します。 次に例を示します。

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a>HTTP 要求の表示を切り替える

既定では、送信中の HTTP 要求の表示は抑制されます。 コマンド シェル セッションの期間中は、対応する設定を変更できます。

### <a name="enable-request-display"></a>要求の表示を有効にする

`echo on` コマンドを実行して、送信中の HTTP 要求を表示します。 次に例を示します。

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

現在のセッションの後続の HTTP 要求では、要求ヘッダーが表示されます。 次に例を示します。

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a>要求の表示を無効にする

`echo off` コマンドを実行して、送信中の HTTP 要求の表示を抑制します。 次に例を示します。

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>スクリプトを実行する

HTTP REPL コマンドの同じセットを頻繁に実行する場合は、それらをテキスト ファイルに格納することを検討してください。 ファイル内のコマンドは、コマンド ラインで手動で実行した場合と同じ形式になります。 これらのコマンドは、`run` コマンドを使用してバッチ方式で実行できます。 次に例を示します。

1. 改行で区切られた一連のコマンドを含むテキスト ファイルを作成します。 例として、次のコマンドを含む *people-script.txt* ファイルについて考えてみましょう。

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. `run` コマンドを実行し、テキスト ファイルのパスを渡します。 次に例を示します。

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    次の出力が表示されます。

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a>出力を消去する

HTTP REPL ツールによってコマンド シェルに書き込まれたすべての出力を削除するには、`clear` コマンドまたは `cls` コマンドを実行します。 例として、コマンド シェルに次の出力が含まれているとします。

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

次のコマンドを実行して、出力を消去します。

```console
https://localhost:5001/~ clear
```

上記のコマンドを実行すると、コマンド シェルには次の出力のみが含まれます。

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>その他の技術情報

* [REST API 要求](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [HTTP REPL GitHub リポジトリ](https://github.com/aspnet/HttpRepl)
