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
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a><span data-ttu-id="b23f7-103">ASP.NET Core で LibMan コマンドラインインターフェイス (CLI) を使用する</span><span class="sxs-lookup"><span data-stu-id="b23f7-103">Use the LibMan command-line interface (CLI) with ASP.NET Core</span></span>

<span data-ttu-id="b23f7-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b23f7-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b23f7-105">[Libman](xref:client-side/libman/index) CLI は、.net Core がサポートされているすべての場所でサポートされるクロスプラットフォームツールです。</span><span class="sxs-lookup"><span data-stu-id="b23f7-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b23f7-106">前提条件</span><span class="sxs-lookup"><span data-stu-id="b23f7-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="b23f7-107">インストール</span><span class="sxs-lookup"><span data-stu-id="b23f7-107">Installation</span></span>

<span data-ttu-id="b23f7-108">LibMan CLI をインストールするには:</span><span class="sxs-lookup"><span data-stu-id="b23f7-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="b23f7-109">[.Net Core グローバルツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、 [Microsoft の Web. librarymanager](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/)の NuGet パッケージからインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="b23f7-110">特定の NuGet パッケージソースから LibMan CLI をインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="b23f7-111">前の例では、.NET Core グローバルツールがローカル Windows コンピューターの*C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg*ファイルからインストールされています。</span><span class="sxs-lookup"><span data-stu-id="b23f7-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="b23f7-112">使用方法</span><span class="sxs-lookup"><span data-stu-id="b23f7-112">Usage</span></span>

<span data-ttu-id="b23f7-113">CLI が正常にインストールされたら、次のコマンドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="b23f7-114">インストールされている CLI のバージョンを表示するには:</span><span class="sxs-lookup"><span data-stu-id="b23f7-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="b23f7-115">使用可能な CLI コマンドを表示するには:</span><span class="sxs-lookup"><span data-stu-id="b23f7-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="b23f7-116">上記のコマンドでは、次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="b23f7-117">次のセクションでは、使用可能な CLI コマンドの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="b23f7-118">プロジェクト内の LibMan を初期化します</span><span class="sxs-lookup"><span data-stu-id="b23f7-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="b23f7-119">コマンド`libman init`を実行すると、 *libman. json*ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="b23f7-120">ファイルは、既定の項目テンプレートコンテンツを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-121">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="b23f7-122">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-122">Options</span></span>

<span data-ttu-id="b23f7-123">`libman init` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="b23f7-124">現在のフォルダーを基準とした相対パス。</span><span class="sxs-lookup"><span data-stu-id="b23f7-124">A path relative to the current folder.</span></span> <span data-ttu-id="b23f7-125">ライブラリファイルは、 *libman. json*の`destination`ライブラリに対してプロパティが定義されていない場合に、この場所にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="b23f7-126">値`<PATH>`は、 *libman. json*の`defaultDestination`プロパティに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="b23f7-127">指定したライブラリにプロバイダーが定義されていない場合に使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="b23f7-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="b23f7-128">値`<PROVIDER>`は、 *libman. json*の`defaultProvider`プロパティに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="b23f7-129">次`<PROVIDER>`のいずれかの値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-130">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-130">Examples</span></span>

<span data-ttu-id="b23f7-131">ASP.NET Core プロジェクトに*libman. json*ファイルを作成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="b23f7-132">プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-132">Navigate to the project root.</span></span>
* <span data-ttu-id="b23f7-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="b23f7-134">既定のプロバイダーの名前を入力するか、 `Enter`キーを押して既定の cdnjs プロバイダーを使用します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="b23f7-135">有効な値は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b23f7-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init コマンド-既定のプロバイダー](_static/libman-init-provider.png)

<span data-ttu-id="b23f7-137">*Libman. json*ファイルは、次の内容を含むプロジェクトルートに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="b23f7-138">ライブラリファイルの追加</span><span class="sxs-lookup"><span data-stu-id="b23f7-138">Add library files</span></span>

<span data-ttu-id="b23f7-139">コマンド`libman install`を実行すると、ライブラリファイルがダウンロードされ、プロジェクトにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="b23f7-140">*Libman. json*ファイルが存在しない場合は追加されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="b23f7-141">*Libman. json*ファイルは、ライブラリファイルの構成の詳細を保存するように変更されています。</span><span class="sxs-lookup"><span data-stu-id="b23f7-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-142">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="b23f7-143">引数</span><span class="sxs-lookup"><span data-stu-id="b23f7-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="b23f7-144">インストールするライブラリの名前。</span><span class="sxs-lookup"><span data-stu-id="b23f7-144">The name of the library to install.</span></span> <span data-ttu-id="b23f7-145">この名前には、バージョン番号の表記 (など`@1.2.0`) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="b23f7-146">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-146">Options</span></span>

<span data-ttu-id="b23f7-147">`libman install` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="b23f7-148">ライブラリをインストールする場所。</span><span class="sxs-lookup"><span data-stu-id="b23f7-148">The location to install the library.</span></span> <span data-ttu-id="b23f7-149">指定しない場合は、既定の場所が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-149">If not specified, the default location is used.</span></span> <span data-ttu-id="b23f7-150">`defaultDestination` *Libman. json*でプロパティが指定されていない場合、このオプションは必須です。</span><span class="sxs-lookup"><span data-stu-id="b23f7-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="b23f7-151">ライブラリからインストールするファイルの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="b23f7-152">指定しない場合、ライブラリのすべてのファイルがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="b23f7-153">インストールする`--files`ファイルごとに1つのオプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="b23f7-154">相対パスもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b23f7-154">Relative paths are supported too.</span></span> <span data-ttu-id="b23f7-155">たとえば、`--files dist/browser/signalr.js` のように指定します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="b23f7-156">ライブラリの取得に使用するプロバイダーの名前。</span><span class="sxs-lookup"><span data-stu-id="b23f7-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="b23f7-157">次`<PROVIDER>`のいずれかの値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="b23f7-158">指定しない場合は`defaultProvider` 、 *libman. json*のプロパティが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="b23f7-159">`defaultProvider` *Libman. json*でプロパティが指定されていない場合、このオプションは必須です。</span><span class="sxs-lookup"><span data-stu-id="b23f7-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-160">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-160">Examples</span></span>

<span data-ttu-id="b23f7-161">次の*libman. json*ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="b23f7-162">CDNJS プロバイダーを使用して jQuery バージョン 3.2.1 *jquery*ファイルを*wwwroot/scripts/jQuery*フォルダーにインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="b23f7-163">*Libman. json*ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b23f7-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="b23f7-164">ファイルシステムプロバイダーを使用して、 *C\\: temp\\contosoCalendar\\* から*calendar*ファイルと*calendar .css*ファイルをインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="b23f7-165">次の2つの理由で、次のプロンプトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="b23f7-166">*Libman. json*ファイルには`defaultDestination`プロパティが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="b23f7-167">コマンド`libman install`に`-d|--destination`オプションが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman install コマンド-destination](_static/libman-install-destination.png)

<span data-ttu-id="b23f7-169">既定の保存先を受け入れると、 *libman. json*ファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b23f7-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="b23f7-170">ライブラリファイルの復元</span><span class="sxs-lookup"><span data-stu-id="b23f7-170">Restore library files</span></span>

<span data-ttu-id="b23f7-171">コマンド`libman restore`は、 *libman. json*で定義されているライブラリファイルをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="b23f7-172">次の規則が適用されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-172">The following rules apply:</span></span>

* <span data-ttu-id="b23f7-173">プロジェクトルートに*libman. json*ファイルが存在しない場合は、エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="b23f7-174">ライブラリがプロバイダーを指定して`defaultProvider`いる場合、 *libman. json*のプロパティは無視されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="b23f7-175">ライブラリが変換先を指定すると`defaultDestination` 、 *libman. json*のプロパティは無視されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-176">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="b23f7-177">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-177">Options</span></span>

<span data-ttu-id="b23f7-178">`libman restore` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-179">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-179">Examples</span></span>

<span data-ttu-id="b23f7-180">*Libman. json*で定義されているライブラリファイルを復元するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="b23f7-181">ライブラリファイルの削除</span><span class="sxs-lookup"><span data-stu-id="b23f7-181">Delete library files</span></span>

<span data-ttu-id="b23f7-182">この`libman clean`コマンドは、libman を使用して以前に復元されたライブラリファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="b23f7-183">この操作の後に空になるフォルダーは削除されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="b23f7-184">`libraries` *Libman. json*のプロパティのライブラリファイルに関連付けられている構成は削除されません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-185">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="b23f7-186">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-186">Options</span></span>

<span data-ttu-id="b23f7-187">`libman clean` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-188">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-188">Examples</span></span>

<span data-ttu-id="b23f7-189">LibMan を使用してインストールされたライブラリファイルを削除するには</span><span class="sxs-lookup"><span data-stu-id="b23f7-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="b23f7-190">ライブラリファイルのアンインストール</span><span class="sxs-lookup"><span data-stu-id="b23f7-190">Uninstall library files</span></span>

<span data-ttu-id="b23f7-191">次`libman uninstall`のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="b23f7-192">指定したライブラリに関連付けられているすべてのファイルを、 *libman. json*内のコピー先から削除します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="b23f7-193">関連付けられているライブラリ構成を*libman. json*から削除します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="b23f7-194">次の場合にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-194">An error occurs when:</span></span>

* <span data-ttu-id="b23f7-195">プロジェクトルートに*libman. json*ファイルが存在しません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="b23f7-196">指定されたライブラリは存在しません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="b23f7-197">同じ名前のライブラリが複数インストールされている場合は、1つを選択するように求められます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-198">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="b23f7-199">引数</span><span class="sxs-lookup"><span data-stu-id="b23f7-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="b23f7-200">アンインストールするライブラリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-200">The name of the library to uninstall.</span></span> <span data-ttu-id="b23f7-201">この名前には、バージョン番号の表記 (など`@1.2.0`) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="b23f7-202">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-202">Options</span></span>

<span data-ttu-id="b23f7-203">`libman uninstall` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-204">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-204">Examples</span></span>

<span data-ttu-id="b23f7-205">次の*libman. json*ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="b23f7-206">JQuery をアンインストールするには、次のいずれかのコマンドを正常に実行します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="b23f7-207">`filesystem`プロバイダーを使用してインストールされた lodash ファイルをアンインストールするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="b23f7-208">ライブラリバージョンの更新</span><span class="sxs-lookup"><span data-stu-id="b23f7-208">Update library version</span></span>

<span data-ttu-id="b23f7-209">コマンド`libman update`は、libman を使用してインストールされたライブラリを、指定されたバージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="b23f7-210">次の場合にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-210">An error occurs when:</span></span>

* <span data-ttu-id="b23f7-211">プロジェクトルートに*libman. json*ファイルが存在しません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="b23f7-212">指定されたライブラリは存在しません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="b23f7-213">同じ名前のライブラリが複数インストールされている場合は、1つを選択するように求められます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-214">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="b23f7-215">引数</span><span class="sxs-lookup"><span data-stu-id="b23f7-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="b23f7-216">更新するライブラリの名前。</span><span class="sxs-lookup"><span data-stu-id="b23f7-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="b23f7-217">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-217">Options</span></span>

<span data-ttu-id="b23f7-218">`libman update` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="b23f7-219">ライブラリの最新のプレリリースバージョンを取得します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="b23f7-220">ライブラリの特定のバージョンを取得します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-221">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-221">Examples</span></span>

* <span data-ttu-id="b23f7-222">JQuery を最新バージョンに更新するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="b23f7-223">JQuery をバージョン3.3.1 に更新するには:</span><span class="sxs-lookup"><span data-stu-id="b23f7-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="b23f7-224">JQuery を最新のプレリリースバージョンに更新するには:</span><span class="sxs-lookup"><span data-stu-id="b23f7-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="b23f7-225">ライブラリキャッシュの管理</span><span class="sxs-lookup"><span data-stu-id="b23f7-225">Manage library cache</span></span>

<span data-ttu-id="b23f7-226">コマンド`libman cache`は、libman ライブラリキャッシュを管理します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="b23f7-227">プロバイダー `filesystem`がライブラリキャッシュを使用していません。</span><span class="sxs-lookup"><span data-stu-id="b23f7-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="b23f7-228">構文</span><span class="sxs-lookup"><span data-stu-id="b23f7-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="b23f7-229">引数</span><span class="sxs-lookup"><span data-stu-id="b23f7-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="b23f7-230">`clean`コマンドでのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-230">Only used with the `clean` command.</span></span> <span data-ttu-id="b23f7-231">消去するプロバイダーキャッシュを指定します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="b23f7-232">有効な値は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b23f7-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="b23f7-233">オプション</span><span class="sxs-lookup"><span data-stu-id="b23f7-233">Options</span></span>

<span data-ttu-id="b23f7-234">`libman cache` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="b23f7-235">キャッシュされているファイルの名前を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="b23f7-236">キャッシュされているライブラリの名前を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="b23f7-237">使用例</span><span class="sxs-lookup"><span data-stu-id="b23f7-237">Examples</span></span>

* <span data-ttu-id="b23f7-238">プロバイダーごとにキャッシュされたライブラリの名前を表示するには、次のコマンドのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="b23f7-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="b23f7-239">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="b23f7-240">プロバイダーごとにキャッシュされたライブラリファイルの名前を表示するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="b23f7-241">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="b23f7-242">前の出力に、jQuery バージョン3.2.1 と3.3.1 が CDNJS プロバイダーでキャッシュされていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="b23f7-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="b23f7-243">CDNJS プロバイダーのライブラリキャッシュを空にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b23f7-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="b23f7-244">Cdnjs プロバイダーのキャッシュを空に`libman cache list`すると、次のようにコマンドが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="b23f7-245">サポートされているすべてのプロバイダーのキャッシュを空にするには:</span><span class="sxs-lookup"><span data-stu-id="b23f7-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="b23f7-246">すべてのプロバイダーキャッシュを空に`libman cache list`すると、次のようなコマンドが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b23f7-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="b23f7-247">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b23f7-247">Additional resources</span></span>

* [<span data-ttu-id="b23f7-248">グローバルツールをインストールする</span><span class="sxs-lookup"><span data-stu-id="b23f7-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="b23f7-249">LibMan の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="b23f7-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
