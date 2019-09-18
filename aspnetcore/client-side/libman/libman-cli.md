---
title: ASP.NET Core で LibMan コマンドラインインターフェイス (CLI) を使用する
author: scottaddie
description: ASP.NET Core プロジェクトで LibMan コマンドラインインターフェイス (CLI) を使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: cf61bab2f0c3fc33d293968b8ac380cb56958d29
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080620"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>ASP.NET Core で LibMan コマンドラインインターフェイス (CLI) を使用する

作成者: [Scott Addie](https://twitter.com/Scott_Addie)

[Libman](xref:client-side/libman/index) CLI は、.net Core がサポートされているすべての場所でサポートされるクロスプラットフォームツールです。

## <a name="prerequisites"></a>前提条件

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>インストール

LibMan CLI をインストールするには:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[.Net Core グローバルツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、 [Microsoft の Web. librarymanager](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/)の NuGet パッケージからインストールされます。

特定の NuGet パッケージソースから LibMan CLI をインストールするには、次のようにします。

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

前の例では、.NET Core グローバルツールがローカル Windows コンピューターの*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*ファイルからインストールされています。

## <a name="usage"></a>使用方法

CLI が正常にインストールされたら、次のコマンドを使用できます。

```console
libman
```

インストールされている CLI のバージョンを表示するには:

```console
libman --version
```

使用可能な CLI コマンドを表示するには:

```console
libman --help
```

上記のコマンドでは、次のような出力が表示されます。

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

次のセクションでは、使用可能な CLI コマンドの概要を示します。

## <a name="initialize-libman-in-the-project"></a>プロジェクト内の LibMan を初期化します

コマンド`libman init`を実行すると、 *libman. json*ファイルが存在しない場合は作成されます。 ファイルは、既定の項目テンプレートコンテンツを使用して作成されます。

### <a name="synopsis"></a>構文

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>オプション

`libman init` コマンドには以下のオプションを使用できます。

* `-d|--default-destination <PATH>`

  現在のフォルダーを基準とした相対パス。 ライブラリファイルは、 *libman. json*の`destination`ライブラリに対してプロパティが定義されていない場合に、この場所にインストールされます。 値`<PATH>`は、 *libman. json*の`defaultDestination`プロパティに書き込まれます。

* `-p|--default-provider <PROVIDER>`

  指定したライブラリにプロバイダーが定義されていない場合に使用するプロバイダー。 値`<PROVIDER>`は、 *libman. json*の`defaultProvider`プロパティに書き込まれます。 次`<PROVIDER>`のいずれかの値に置き換えます。

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

ASP.NET Core プロジェクトに*libman. json*ファイルを作成するには、次のようにします。

* プロジェクトのルートに移動します。
* 次のコマンドを実行します。

  ```console
  libman init
  ```

* 既定のプロバイダーの名前を入力するか、 `Enter`キーを押して既定の cdnjs プロバイダーを使用します。 有効な値は次のとおりです。

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init コマンド-既定のプロバイダー](_static/libman-init-provider.png)

*Libman. json*ファイルは、次の内容を含むプロジェクトルートに追加されます。

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>ライブラリファイルの追加

コマンド`libman install`を実行すると、ライブラリファイルがダウンロードされ、プロジェクトにインストールされます。 *Libman. json*ファイルが存在しない場合は追加されます。 *Libman. json*ファイルは、ライブラリファイルの構成の詳細を保存するように変更されています。

### <a name="synopsis"></a>構文

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>引数

`LIBRARY`

インストールするライブラリの名前。 この名前には、バージョン番号の表記 (など`@1.2.0`) を含めることができます。

### <a name="options"></a>オプション

`libman install` コマンドには以下のオプションを使用できます。

* `-d|--destination <PATH>`

  ライブラリをインストールする場所。 指定しない場合は、既定の場所が使用されます。 `defaultDestination` *Libman. json*でプロパティが指定されていない場合、このオプションは必須です。

* `--files <FILE>`

  ライブラリからインストールするファイルの名前を指定します。 指定しない場合、ライブラリのすべてのファイルがインストールされます。 インストールする`--files`ファイルごとに1つのオプションを指定します。 相対パスもサポートされています。 たとえば、`--files dist/browser/signalr.js` のように指定します。

* `-p|--provider <PROVIDER>`

  ライブラリの取得に使用するプロバイダーの名前。 次`<PROVIDER>`のいずれかの値に置き換えます。
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  指定しない場合は`defaultProvider` 、 *libman. json*のプロパティが使用されます。 `defaultProvider` *Libman. json*でプロパティが指定されていない場合、このオプションは必須です。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

次の*libman. json*ファイルについて考えてみます。

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

CDNJS プロバイダーを使用して jQuery バージョン 3.2.1 *jquery*ファイルを*wwwroot/scripts/jQuery*フォルダーにインストールするには、次のようにします。

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

*Libman. json*ファイルは次のようになります。

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

ファイルシステムプロバイダーを使用して、 *C\\: temp\\contosoCalendar\\* から*calendar*ファイルと*calendar .css*ファイルをインストールするには、次のようにします。

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

次の2つの理由で、次のプロンプトが表示されます。

* *Libman. json*ファイルには`defaultDestination`プロパティが含まれていません。
* コマンド`libman install`に`-d|--destination`オプションが含まれていません。

![libman install コマンド-destination](_static/libman-install-destination.png)

既定の保存先を受け入れると、 *libman. json*ファイルは次のようになります。

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>ライブラリファイルの復元

コマンド`libman restore`は、 *libman. json*で定義されているライブラリファイルをインストールします。 次の規則が適用されます。

* プロジェクトルートに*libman. json*ファイルが存在しない場合は、エラーが返されます。
* ライブラリがプロバイダーを指定して`defaultProvider`いる場合、 *libman. json*のプロパティは無視されます。
* ライブラリが変換先を指定すると`defaultDestination` 、 *libman. json*のプロパティは無視されます。

### <a name="synopsis"></a>構文

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>オプション

`libman restore` コマンドには以下のオプションを使用できます。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

*Libman. json*で定義されているライブラリファイルを復元するには、次のようにします。

```console
libman restore
```

## <a name="delete-library-files"></a>ライブラリファイルの削除

この`libman clean`コマンドは、libman を使用して以前に復元されたライブラリファイルを削除します。 この操作の後に空になるフォルダーは削除されます。 `libraries` *Libman. json*のプロパティのライブラリファイルに関連付けられている構成は削除されません。

### <a name="synopsis"></a>構文

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>オプション

`libman clean` コマンドには以下のオプションを使用できます。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

LibMan を使用してインストールされたライブラリファイルを削除するには

```console
libman clean
```

## <a name="uninstall-library-files"></a>ライブラリファイルのアンインストール

次`libman uninstall`のコマンドを実行します。

* 指定したライブラリに関連付けられているすべてのファイルを、 *libman. json*内のコピー先から削除します。
* 関連付けられているライブラリ構成を*libman. json*から削除します。

次の場合にエラーが発生します。

* プロジェクトルートに*libman. json*ファイルが存在しません。
* 指定されたライブラリは存在しません。

同じ名前のライブラリが複数インストールされている場合は、1つを選択するように求められます。

### <a name="synopsis"></a>構文

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>引数

`LIBRARY`

アンインストールするライブラリの名前を指定します。 この名前には、バージョン番号の表記 (など`@1.2.0`) を含めることができます。

### <a name="options"></a>オプション

`libman uninstall` コマンドには以下のオプションを使用できます。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

次の*libman. json*ファイルについて考えてみます。

[!code-json[](samples/LibManSample/libman.json)]

* JQuery をアンインストールするには、次のいずれかのコマンドを正常に実行します。

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* `filesystem`プロバイダーを使用してインストールされた lodash ファイルをアンインストールするには、次のようにします。

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>ライブラリバージョンの更新

コマンド`libman update`は、libman を使用してインストールされたライブラリを、指定されたバージョンに更新します。

次の場合にエラーが発生します。

* プロジェクトルートに*libman. json*ファイルが存在しません。
* 指定されたライブラリは存在しません。

同じ名前のライブラリが複数インストールされている場合は、1つを選択するように求められます。

### <a name="synopsis"></a>構文

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>引数

`LIBRARY`

更新するライブラリの名前。

### <a name="options"></a>オプション

`libman update` コマンドには以下のオプションを使用できます。

* `-pre`

  ライブラリの最新のプレリリースバージョンを取得します。

* `--to <VERSION>`

  ライブラリの特定のバージョンを取得します。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

* JQuery を最新バージョンに更新するには、次のようにします。

  ```console
  libman update jquery
  ```

* JQuery をバージョン3.3.1 に更新するには:

  ```console
  libman update jquery --to 3.3.1
  ```

* JQuery を最新のプレリリースバージョンに更新するには:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>ライブラリキャッシュの管理

コマンド`libman cache`は、libman ライブラリキャッシュを管理します。 プロバイダー `filesystem`がライブラリキャッシュを使用していません。

### <a name="synopsis"></a>構文

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>引数

`PROVIDER`

`clean`コマンドでのみ使用されます。 消去するプロバイダーキャッシュを指定します。 有効な値は次のとおりです。

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>オプション

`libman cache` コマンドには以下のオプションを使用できます。

* `--files`

  キャッシュされているファイルの名前を一覧表示します。

* `--libraries`

  キャッシュされているライブラリの名前を一覧表示します。

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>使用例

* プロバイダーごとにキャッシュされたライブラリの名前を表示するには、次のコマンドのいずれかを使用します。

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  次のような出力が表示されます。

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* プロバイダーごとにキャッシュされたライブラリファイルの名前を表示するには、次のようにします。

  ```console
  libman cache list --files
  ```

  次のような出力が表示されます。

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  前の出力に、jQuery バージョン3.2.1 と3.3.1 が CDNJS プロバイダーでキャッシュされていることがわかります。

* CDNJS プロバイダーのライブラリキャッシュを空にするには、次のようにします。

  ```console
  libman cache clean cdnjs
  ```

  Cdnjs プロバイダーのキャッシュを空に`libman cache list`すると、次のようにコマンドが表示されます。

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* サポートされているすべてのプロバイダーのキャッシュを空にするには:

  ```console
  libman cache clean
  ```

  すべてのプロバイダーキャッシュを空に`libman cache list`すると、次のようなコマンドが表示されます。

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>その他の技術情報

* [グローバルツールをインストールする](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan の GitHub リポジトリ](https://github.com/aspnet/LibraryManager)
