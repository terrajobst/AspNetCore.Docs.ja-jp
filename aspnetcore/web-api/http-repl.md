---
title: HTTP REPL を使用して Web API をテストする
author: scottaddie
description: HTTP REPL .NET Core グローバル ツールを使用して、ASP.NET Core Web API を参照およびテストする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2019
uid: web-api/http-repl
ms.openlocfilehash: 1ceda6182c62bb1be06cd95f14e6a46a1809253e
ms.sourcegitcommit: 059ab380744fa3be3b69aa90d431b563c57092cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68410891"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="921f2-103">HTTP REPL を使用して Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="921f2-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="921f2-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="921f2-105">HTTP Read-Eval-Print Loop (REPL) は:</span><span class="sxs-lookup"><span data-stu-id="921f2-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="921f2-106">.NET Core がサポートされているすべての場所でサポートされている、軽量なクロスプラットフォーム コマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="921f2-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="921f2-107">ASP.NET Core Web API をテストし、その結果を表示する HTTP 要求を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-107">Used for making HTTP requests to test ASP.NET Core web APIs and view their results.</span></span>

<span data-ttu-id="921f2-108">次の [HTTP 動詞](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="921f2-108">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="921f2-109">DELETE</span><span class="sxs-lookup"><span data-stu-id="921f2-109">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="921f2-110">GET</span><span class="sxs-lookup"><span data-stu-id="921f2-110">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="921f2-111">HEAD</span><span class="sxs-lookup"><span data-stu-id="921f2-111">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="921f2-112">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="921f2-112">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="921f2-113">PATCH</span><span class="sxs-lookup"><span data-stu-id="921f2-113">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="921f2-114">POST</span><span class="sxs-lookup"><span data-stu-id="921f2-114">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="921f2-115">PUT</span><span class="sxs-lookup"><span data-stu-id="921f2-115">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="921f2-116">先に進むには、[サンプル ASP.NET Core Web API を表示またはダウンロードします](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="921f2-116">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="921f2-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="921f2-117">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="921f2-118">インストール</span><span class="sxs-lookup"><span data-stu-id="921f2-118">Installation</span></span>

<span data-ttu-id="921f2-119">HTTP REPL をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-119">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version 3.0.0-*
```

<span data-ttu-id="921f2-120">[.Net Core グローバル ツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、[Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet パッケージからインストールされます。</span><span class="sxs-lookup"><span data-stu-id="921f2-120">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="921f2-121">使用法</span><span class="sxs-lookup"><span data-stu-id="921f2-121">Usage</span></span>

<span data-ttu-id="921f2-122">ツールのインストールが正常に完了したら、次のコマンドを実行して HTTP REPL を開始します。</span><span class="sxs-lookup"><span data-stu-id="921f2-122">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="921f2-123">使用可能な HTTP REPL コマンドを表示するには、次のコマンドのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-123">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="921f2-124">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-124">The following output is displayed:</span></span>

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

<span data-ttu-id="921f2-125">HTTP REPL では、コマンド補完が提供されています。</span><span class="sxs-lookup"><span data-stu-id="921f2-125">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="921f2-126"><kbd>Tab</kbd> キーを押すと、入力した文字または API エンドポイントを補完するコマンドの一覧が反復処理されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-126">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="921f2-127">次のセクションでは、使用可能な CLI コマンドの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-127">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="921f2-128">Web API に接続する</span><span class="sxs-lookup"><span data-stu-id="921f2-128">Connect to the web API</span></span>

<span data-ttu-id="921f2-129">次のコマンドを実行して、Web API に接続します。</span><span class="sxs-lookup"><span data-stu-id="921f2-129">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="921f2-130">`<BASE URI>` は、Web API のベース URI です。</span><span class="sxs-lookup"><span data-stu-id="921f2-130">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="921f2-131">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-131">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="921f2-132">または、HTTP REPL の実行中に、次のコマンドをいつでも実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-132">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="921f2-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-133">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="921f2-134">Web API の Swagger ドキュメントをポイントする</span><span class="sxs-lookup"><span data-stu-id="921f2-134">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="921f2-135">Web API を適切に検査するには、相対 URI を Web API の Swagger ドキュメントに設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-135">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="921f2-136">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-136">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="921f2-137">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-137">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="921f2-138">Web API 内を移動する</span><span class="sxs-lookup"><span data-stu-id="921f2-138">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="921f2-139">使用可能なエンドポイントを表示する</span><span class="sxs-lookup"><span data-stu-id="921f2-139">View available endpoints</span></span>

<span data-ttu-id="921f2-140">Web API アドレスの現在のパスにあるさまざまなエンドポイント (コントローラー) を一覧表示するには、`ls` コマンドまたは `dir` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-140">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="921f2-141">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-141">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="921f2-142">前の出力では、`Fruits` と `People` の 2 つのコントローラーが使用可能であることが示されています。</span><span class="sxs-lookup"><span data-stu-id="921f2-142">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="921f2-143">どちらのコントローラーでも、パラメーターなしの HTTP GET 操作と POST 操作がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="921f2-143">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="921f2-144">特定のコントローラーに移動すると、詳細がわかります。</span><span class="sxs-lookup"><span data-stu-id="921f2-144">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="921f2-145">たとえば、次のコマンドの出力は、`Fruits` コントローラーが HTTP GET、PUT、DELETE の各操作をサポートしていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="921f2-145">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="921f2-146">これらの各操作には、ルートで `id` パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="921f2-146">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="921f2-147">または、`ui` コマンドを実行して、ブラウザーで Web API の Swagger UI ページを開きます。</span><span class="sxs-lookup"><span data-stu-id="921f2-147">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="921f2-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-148">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="921f2-149">エンドポイントに移動する</span><span class="sxs-lookup"><span data-stu-id="921f2-149">Navigate to an endpoint</span></span>

<span data-ttu-id="921f2-150">Web API の別のエンドポイントに移動するには、`cd` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-150">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="921f2-151">`cd` コマンドの後のパスでは大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="921f2-151">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="921f2-152">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-152">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="921f2-153">HTTP REPL をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="921f2-153">Customize the HTTP REPL</span></span>

<span data-ttu-id="921f2-154">HTTP REPL の既定の[色](#set-color-preferences)はカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="921f2-154">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="921f2-155">また、[既定のテキストエディター](#set-the-default-text-editor)を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="921f2-155">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="921f2-156">HTTP REPL の設定は、現在のセッションで永続化され、今後のセッションで受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="921f2-156">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="921f2-157">変更した設定は、次のファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-157">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="921f2-158">Linux</span><span class="sxs-lookup"><span data-stu-id="921f2-158">Linux</span></span>](#tab/linux)

<span data-ttu-id="921f2-159">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="921f2-159">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="921f2-160">macOS</span><span class="sxs-lookup"><span data-stu-id="921f2-160">macOS</span></span>](#tab/macos)

<span data-ttu-id="921f2-161">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="921f2-161">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="921f2-162">Windows</span><span class="sxs-lookup"><span data-stu-id="921f2-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="921f2-163">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="921f2-163">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="921f2-164">*.httpreplprefs* ファイルは起動時に読み込まれ、実行時の変更は監視されません。</span><span class="sxs-lookup"><span data-stu-id="921f2-164">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="921f2-165">ファイルに対する手動の変更は、ツールを再起動した後でのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="921f2-165">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="921f2-166">設定を表示する</span><span class="sxs-lookup"><span data-stu-id="921f2-166">View the settings</span></span>

<span data-ttu-id="921f2-167">使用可能な設定を表示するには、`pref get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-167">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="921f2-168">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-168">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="921f2-169">上記のコマンドでは、使用可能なキーと値のペアが表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-169">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="921f2-170">色の設定を設定する</span><span class="sxs-lookup"><span data-stu-id="921f2-170">Set color preferences</span></span>

<span data-ttu-id="921f2-171">応答の色付けは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="921f2-171">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="921f2-172">既定の HTTP REPL ツールの色分けをカスタマイズするには、変更する色に対応するキーを見つけます。</span><span class="sxs-lookup"><span data-stu-id="921f2-172">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="921f2-173">キーを検索する方法については、「[設定を表示する](#view-the-settings)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="921f2-173">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="921f2-174">たとえば、次のように、`colors.json` キー値を `Green` から `White` に変更します。</span><span class="sxs-lookup"><span data-stu-id="921f2-174">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="921f2-175">使用できるのは、[許可されている色](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)だけです。</span><span class="sxs-lookup"><span data-stu-id="921f2-175">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="921f2-176">後続の HTTP 要求では、出力が新しい色で表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-176">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="921f2-177">特定の色キーが設定されていない場合、より汎用的なキーが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-177">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="921f2-178">このフォールバック動作を示すために、次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="921f2-178">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="921f2-179">`colors.json.name` に値がない場合は、`colors.json.string` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-179">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="921f2-180">`colors.json.string` に値がない場合は、`colors.json.literal` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-180">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="921f2-181">`colors.json.literal` に値がない場合は、`colors.json` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-181">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="921f2-182">`colors.json` 値がない場合は、コマンド シェルの既定のテキストの色 (`AllowedColors.None`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-182">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="921f2-183">インデントのサイズを設定する</span><span class="sxs-lookup"><span data-stu-id="921f2-183">Set indentation size</span></span>

<span data-ttu-id="921f2-184">応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="921f2-184">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="921f2-185">既定のサイズは 2 つのスペースです。</span><span class="sxs-lookup"><span data-stu-id="921f2-185">The default size is two spaces.</span></span> <span data-ttu-id="921f2-186">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-186">For example:</span></span>

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

<span data-ttu-id="921f2-187">既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-187">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="921f2-188">たとえば、常に 4 つのスペースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="921f2-188">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="921f2-189">後続の応答では、4 つのスペースの設定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-189">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="921f2-190">インデントのサイズを設定する</span><span class="sxs-lookup"><span data-stu-id="921f2-190">Set indentation size</span></span>

<span data-ttu-id="921f2-191">応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="921f2-191">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="921f2-192">既定のサイズは 2 つのスペースです。</span><span class="sxs-lookup"><span data-stu-id="921f2-192">The default size is two spaces.</span></span> <span data-ttu-id="921f2-193">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-193">For example:</span></span>

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

<span data-ttu-id="921f2-194">既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-194">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="921f2-195">たとえば、常に 4 つのスペースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="921f2-195">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="921f2-196">後続の応答では、4 つのスペースの設定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-196">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="921f2-197">既定のテキスト エディターを設定する</span><span class="sxs-lookup"><span data-stu-id="921f2-197">Set the default text editor</span></span>

<span data-ttu-id="921f2-198">既定では、HTTP REPL には、使用するように構成されたテキスト エディターはありません。</span><span class="sxs-lookup"><span data-stu-id="921f2-198">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="921f2-199">HTTP 要求本文を必要とする Web API メソッドをテストするには、既定のテキスト エディターを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="921f2-199">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="921f2-200">HTTP REPL ツールでは、要求本文を作成する目的のためだけに構成されたテキスト エディターが起動されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-200">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="921f2-201">次のコマンドを実行して、優先テキストエディターを既定値として設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-201">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="921f2-202">上記のコマンドで、`<EXECUTABLE>` はテキスト エディターの実行可能ファイルへの完全なパスです。</span><span class="sxs-lookup"><span data-stu-id="921f2-202">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="921f2-203">たとえば、次のコマンドを実行して、既定のテキストエディターとして Visual Studio Code を設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-203">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="921f2-204">Linux</span><span class="sxs-lookup"><span data-stu-id="921f2-204">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="921f2-205">macOS</span><span class="sxs-lookup"><span data-stu-id="921f2-205">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="921f2-206">Windows</span><span class="sxs-lookup"><span data-stu-id="921f2-206">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="921f2-207">特定の CLI 引数を使用して既定のテキスト エディターを起動するには、`editor.command.default.arguments` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-207">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="921f2-208">たとえば、Visual Studio Code が既定のテキスト エディターで、拡張機能が無効になっている新しいセッションでは HTTP REPL で常に Visual Studio Code を開きたいとします。</span><span class="sxs-lookup"><span data-stu-id="921f2-208">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="921f2-209">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-209">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="921f2-210">HTTP GET 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-210">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-211">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-211">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-212">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-212">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-213">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-213">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-214">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-214">Options</span></span>

<span data-ttu-id="921f2-215">`get` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="921f2-215">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="921f2-216">例</span><span class="sxs-lookup"><span data-stu-id="921f2-216">Example</span></span>

<span data-ttu-id="921f2-217">HTTP GET 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="921f2-217">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="921f2-218">それをサポートしているエンドポイントで `get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-218">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="921f2-219">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-219">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="921f2-220">`get` コマンドにパラメーターを渡すことによって、特定のレコードを取得します。</span><span class="sxs-lookup"><span data-stu-id="921f2-220">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="921f2-221">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-221">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="921f2-222">HTTP POST 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-222">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-223">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-223">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-224">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-225">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-226">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-226">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="921f2-227">例</span><span class="sxs-lookup"><span data-stu-id="921f2-227">Example</span></span>

<span data-ttu-id="921f2-228">HTTP POST 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="921f2-228">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="921f2-229">それをサポートしているエンドポイントで `post` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-229">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="921f2-230">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="921f2-230">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="921f2-231">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="921f2-231">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="921f2-232">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-232">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="921f2-233">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="921f2-233">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="921f2-234">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="921f2-234">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="921f2-235">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="921f2-235">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="921f2-236">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-236">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="921f2-237">HTTP PUT 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-237">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-238">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-238">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-239">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-239">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-240">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-240">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-241">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-241">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="921f2-242">例</span><span class="sxs-lookup"><span data-stu-id="921f2-242">Example</span></span>

<span data-ttu-id="921f2-243">HTTP PUT 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="921f2-243">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="921f2-244">*省略可能*:変更前にデータを表示するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-244">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="921f2-245">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="921f2-245">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="921f2-246">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="921f2-246">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="921f2-247">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-247">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="921f2-248">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="921f2-248">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="921f2-249">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="921f2-249">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="921f2-250">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="921f2-250">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="921f2-251">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-251">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="921f2-252">*省略可能*:変更を確認するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-252">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="921f2-253">たとえば、テキスト エディターで「Cherry」と入力すると、`get` は次を返します。</span><span class="sxs-lookup"><span data-stu-id="921f2-253">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="921f2-254">HTTP DELETE 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-254">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-255">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-255">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-256">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-256">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-257">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-257">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-258">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-258">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="921f2-259">例</span><span class="sxs-lookup"><span data-stu-id="921f2-259">Example</span></span>

<span data-ttu-id="921f2-260">HTTP DELETE 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="921f2-260">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="921f2-261">*省略可能*:変更前にデータを表示するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-261">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="921f2-262">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-262">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="921f2-263">*省略可能*:変更を確認するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-263">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="921f2-264">この例では、`get` は次を返します。</span><span class="sxs-lookup"><span data-stu-id="921f2-264">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="921f2-265">HTTP PATCH 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-265">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-266">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-266">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-267">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-267">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-268">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-268">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-269">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-269">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="921f2-270">HTTP HEAD 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-270">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-271">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-271">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-272">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-272">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-273">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-273">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-274">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-274">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="921f2-275">HTTP OPTIONS 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="921f2-275">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="921f2-276">構文</span><span class="sxs-lookup"><span data-stu-id="921f2-276">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="921f2-277">引数</span><span class="sxs-lookup"><span data-stu-id="921f2-277">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="921f2-278">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="921f2-278">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="921f2-279">オプション</span><span class="sxs-lookup"><span data-stu-id="921f2-279">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="921f2-280">HTTP 要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="921f2-280">Set HTTP request headers</span></span>

<span data-ttu-id="921f2-281">HTTP 要求ヘッダーを設定するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="921f2-281">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="921f2-282">HTTP 要求でインラインを設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-282">Set inline with the HTTP request.</span></span> <span data-ttu-id="921f2-283">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-283">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="921f2-284">上記の方法では、個別の HTTP 要求ヘッダーごとに独自の `-h` オプションが必要です。</span><span class="sxs-lookup"><span data-stu-id="921f2-284">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="921f2-285">HTTP 要求を送信する前に設定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-285">Set before sending the HTTP request.</span></span> <span data-ttu-id="921f2-286">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-286">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="921f2-287">要求を送信する前にヘッダーを設定すると、コマンド シェル セッションの間はヘッダーが設定されたままになります。</span><span class="sxs-lookup"><span data-stu-id="921f2-287">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="921f2-288">ヘッダーをクリアするには、空の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="921f2-288">To clear the header, provide an empty value.</span></span> <span data-ttu-id="921f2-289">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-289">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="921f2-290">HTTP 要求の表示を切り替える</span><span class="sxs-lookup"><span data-stu-id="921f2-290">Toggle HTTP request display</span></span>

<span data-ttu-id="921f2-291">既定では、送信中の HTTP 要求の表示は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-291">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="921f2-292">コマンド シェル セッションの期間中は、対応する設定を変更できます。</span><span class="sxs-lookup"><span data-stu-id="921f2-292">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="921f2-293">要求の表示を有効にする</span><span class="sxs-lookup"><span data-stu-id="921f2-293">Enable request display</span></span>

<span data-ttu-id="921f2-294">`echo on` コマンドを実行して、送信中の HTTP 要求を表示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-294">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="921f2-295">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-295">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="921f2-296">現在のセッションの後続の HTTP 要求では、要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-296">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="921f2-297">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-297">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="921f2-298">要求の表示を無効にする</span><span class="sxs-lookup"><span data-stu-id="921f2-298">Disable request display</span></span>

<span data-ttu-id="921f2-299">`echo off` コマンドを実行して、送信中の HTTP 要求の表示を抑制します。</span><span class="sxs-lookup"><span data-stu-id="921f2-299">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="921f2-300">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-300">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="921f2-301">スクリプトを実行する</span><span class="sxs-lookup"><span data-stu-id="921f2-301">Run a script</span></span>

<span data-ttu-id="921f2-302">HTTP REPL コマンドの同じセットを頻繁に実行する場合は、それらをテキスト ファイルに格納することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="921f2-302">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="921f2-303">ファイル内のコマンドは、コマンド ラインで手動で実行した場合と同じ形式になります。</span><span class="sxs-lookup"><span data-stu-id="921f2-303">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="921f2-304">これらのコマンドは、`run` コマンドを使用してバッチ方式で実行できます。</span><span class="sxs-lookup"><span data-stu-id="921f2-304">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="921f2-305">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-305">For example:</span></span>

1. <span data-ttu-id="921f2-306">改行で区切られた一連のコマンドを含むテキスト ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="921f2-306">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="921f2-307">例として、次のコマンドを含む *people-script.txt* ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="921f2-307">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="921f2-308">`run` コマンドを実行し、テキスト ファイルのパスを渡します。</span><span class="sxs-lookup"><span data-stu-id="921f2-308">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="921f2-309">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="921f2-309">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="921f2-310">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="921f2-310">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="921f2-311">出力を消去する</span><span class="sxs-lookup"><span data-stu-id="921f2-311">Clear the output</span></span>

<span data-ttu-id="921f2-312">HTTP REPL ツールによってコマンド シェルに書き込まれたすべての出力を削除するには、`clear` コマンドまたは `cls` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="921f2-312">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="921f2-313">例として、コマンド シェルに次の出力が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="921f2-313">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="921f2-314">次のコマンドを実行して、出力を消去します。</span><span class="sxs-lookup"><span data-stu-id="921f2-314">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="921f2-315">上記のコマンドを実行すると、コマンド シェルには次の出力のみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="921f2-315">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="921f2-316">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="921f2-316">Additional resources</span></span>

* [<span data-ttu-id="921f2-317">REST API 要求</span><span class="sxs-lookup"><span data-stu-id="921f2-317">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="921f2-318">HTTP REPL GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="921f2-318">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
