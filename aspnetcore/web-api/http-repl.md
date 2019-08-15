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
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="230df-103">HTTP REPL を使用して Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="230df-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="230df-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="230df-105">HTTP Read-Eval-Print Loop (REPL) は:</span><span class="sxs-lookup"><span data-stu-id="230df-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="230df-106">.NET Core がサポートされているすべての場所でサポートされている、軽量なクロスプラットフォーム コマンドライン ツールです。</span><span class="sxs-lookup"><span data-stu-id="230df-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="230df-107">ASP.NET Core Web API (および ASP.NET 以外の Core Web API) をテストし、その結果を表示する HTTP 要求を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="230df-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="230df-108">localhost や Azure App Service などの任意の環境でホストされている Web API をテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="230df-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="230df-109">次の [HTTP 動詞](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="230df-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="230df-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="230df-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="230df-111">GET</span><span class="sxs-lookup"><span data-stu-id="230df-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="230df-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="230df-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="230df-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="230df-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="230df-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="230df-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="230df-115">POST</span><span class="sxs-lookup"><span data-stu-id="230df-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="230df-116">PUT</span><span class="sxs-lookup"><span data-stu-id="230df-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="230df-117">先に進むには、[サンプル ASP.NET Core Web API を表示またはダウンロードします](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="230df-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="230df-118">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="230df-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="230df-119">インストール</span><span class="sxs-lookup"><span data-stu-id="230df-119">Installation</span></span>

<span data-ttu-id="230df-120">HTTP REPL をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-120">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="230df-121">[.Net Core グローバル ツール](/dotnet/core/tools/global-tools#install-a-global-tool)は、[Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet パッケージからインストールされます。</span><span class="sxs-lookup"><span data-stu-id="230df-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="230df-122">使用法</span><span class="sxs-lookup"><span data-stu-id="230df-122">Usage</span></span>

<span data-ttu-id="230df-123">ツールのインストールが正常に完了したら、次のコマンドを実行して HTTP REPL を開始します。</span><span class="sxs-lookup"><span data-stu-id="230df-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="230df-124">使用可能な HTTP REPL コマンドを表示するには、次のコマンドのいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="230df-125">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-125">The following output is displayed:</span></span>

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

<span data-ttu-id="230df-126">HTTP REPL では、コマンド補完が提供されています。</span><span class="sxs-lookup"><span data-stu-id="230df-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="230df-127"><kbd>Tab</kbd> キーを押すと、入力した文字または API エンドポイントを補完するコマンドの一覧が反復処理されます。</span><span class="sxs-lookup"><span data-stu-id="230df-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="230df-128">次のセクションでは、使用可能な CLI コマンドの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="230df-129">Web API に接続する</span><span class="sxs-lookup"><span data-stu-id="230df-129">Connect to the web API</span></span>

<span data-ttu-id="230df-130">次のコマンドを実行して、Web API に接続します。</span><span class="sxs-lookup"><span data-stu-id="230df-130">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="230df-131">`<BASE URI>` は、Web API のベース URI です。</span><span class="sxs-lookup"><span data-stu-id="230df-131">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="230df-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-132">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="230df-133">または、HTTP REPL の実行中に、次のコマンドをいつでも実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="230df-134">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-134">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="230df-135">Web API の Swagger ドキュメントをポイントする</span><span class="sxs-lookup"><span data-stu-id="230df-135">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="230df-136">Web API を適切に検査するには、相対 URI を Web API の Swagger ドキュメントに設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-136">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="230df-137">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-137">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="230df-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-138">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="230df-139">Web API 内を移動する</span><span class="sxs-lookup"><span data-stu-id="230df-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="230df-140">使用可能なエンドポイントを表示する</span><span class="sxs-lookup"><span data-stu-id="230df-140">View available endpoints</span></span>

<span data-ttu-id="230df-141">Web API アドレスの現在のパスにあるさまざまなエンドポイント (コントローラー) を一覧表示するには、`ls` コマンドまたは `dir` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="230df-142">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="230df-143">前の出力では、`Fruits` と `People` の 2 つのコントローラーが使用可能であることが示されています。</span><span class="sxs-lookup"><span data-stu-id="230df-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="230df-144">どちらのコントローラーでも、パラメーターなしの HTTP GET 操作と POST 操作がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="230df-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="230df-145">特定のコントローラーに移動すると、詳細がわかります。</span><span class="sxs-lookup"><span data-stu-id="230df-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="230df-146">たとえば、次のコマンドの出力は、`Fruits` コントローラーが HTTP GET、PUT、DELETE の各操作をサポートしていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="230df-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="230df-147">これらの各操作には、ルートで `id` パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="230df-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="230df-148">または、`ui` コマンドを実行して、ブラウザーで Web API の Swagger UI ページを開きます。</span><span class="sxs-lookup"><span data-stu-id="230df-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="230df-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="230df-150">エンドポイントに移動する</span><span class="sxs-lookup"><span data-stu-id="230df-150">Navigate to an endpoint</span></span>

<span data-ttu-id="230df-151">Web API の別のエンドポイントに移動するには、`cd` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="230df-152">`cd` コマンドの後のパスでは大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="230df-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="230df-153">次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="230df-154">HTTP REPL をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="230df-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="230df-155">HTTP REPL の既定の[色](#set-color-preferences)はカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="230df-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="230df-156">また、[既定のテキストエディター](#set-the-default-text-editor)を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="230df-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="230df-157">HTTP REPL の設定は、現在のセッションで永続化され、今後のセッションで受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="230df-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="230df-158">変更した設定は、次のファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="230df-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="230df-159">Linux</span><span class="sxs-lookup"><span data-stu-id="230df-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="230df-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="230df-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="230df-161">macOS</span><span class="sxs-lookup"><span data-stu-id="230df-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="230df-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="230df-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="230df-163">Windows</span><span class="sxs-lookup"><span data-stu-id="230df-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="230df-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="230df-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="230df-165">*.httpreplprefs* ファイルは起動時に読み込まれ、実行時の変更は監視されません。</span><span class="sxs-lookup"><span data-stu-id="230df-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="230df-166">ファイルに対する手動の変更は、ツールを再起動した後でのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="230df-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="230df-167">設定を表示する</span><span class="sxs-lookup"><span data-stu-id="230df-167">View the settings</span></span>

<span data-ttu-id="230df-168">使用可能な設定を表示するには、`pref get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="230df-169">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="230df-170">上記のコマンドでは、使用可能なキーと値のペアが表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="230df-171">色の設定を設定する</span><span class="sxs-lookup"><span data-stu-id="230df-171">Set color preferences</span></span>

<span data-ttu-id="230df-172">応答の色付けは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="230df-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="230df-173">既定の HTTP REPL ツールの色分けをカスタマイズするには、変更する色に対応するキーを見つけます。</span><span class="sxs-lookup"><span data-stu-id="230df-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="230df-174">キーを検索する方法については、「[設定を表示する](#view-the-settings)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="230df-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="230df-175">たとえば、次のように、`colors.json` キー値を `Green` から `White` に変更します。</span><span class="sxs-lookup"><span data-stu-id="230df-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="230df-176">使用できるのは、[許可されている色](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)だけです。</span><span class="sxs-lookup"><span data-stu-id="230df-176">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="230df-177">後続の HTTP 要求では、出力が新しい色で表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="230df-178">特定の色キーが設定されていない場合、より汎用的なキーが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="230df-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="230df-179">このフォールバック動作を示すために、次の例を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="230df-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="230df-180">`colors.json.name` に値がない場合は、`colors.json.string` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="230df-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="230df-181">`colors.json.string` に値がない場合は、`colors.json.literal` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="230df-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="230df-182">`colors.json.literal` に値がない場合は、`colors.json` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="230df-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="230df-183">`colors.json` 値がない場合は、コマンド シェルの既定のテキストの色 (`AllowedColors.None`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="230df-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="230df-184">インデントのサイズを設定する</span><span class="sxs-lookup"><span data-stu-id="230df-184">Set indentation size</span></span>

<span data-ttu-id="230df-185">応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="230df-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="230df-186">既定のサイズは 2 つのスペースです。</span><span class="sxs-lookup"><span data-stu-id="230df-186">The default size is two spaces.</span></span> <span data-ttu-id="230df-187">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-187">For example:</span></span>

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

<span data-ttu-id="230df-188">既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="230df-189">たとえば、常に 4 つのスペースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="230df-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="230df-190">後続の応答では、4 つのスペースの設定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="230df-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="230df-191">インデントのサイズを設定する</span><span class="sxs-lookup"><span data-stu-id="230df-191">Set indentation size</span></span>

<span data-ttu-id="230df-192">応答インデント サイズのカスタマイズは、現在、JSON でのみサポートされています。</span><span class="sxs-lookup"><span data-stu-id="230df-192">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="230df-193">既定のサイズは 2 つのスペースです。</span><span class="sxs-lookup"><span data-stu-id="230df-193">The default size is two spaces.</span></span> <span data-ttu-id="230df-194">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-194">For example:</span></span>

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

<span data-ttu-id="230df-195">既定のサイズを変更するには、`formatting.json.indentSize` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-195">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="230df-196">たとえば、常に 4 つのスペースを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="230df-196">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="230df-197">後続の応答では、4 つのスペースの設定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="230df-197">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="230df-198">既定のテキスト エディターを設定する</span><span class="sxs-lookup"><span data-stu-id="230df-198">Set the default text editor</span></span>

<span data-ttu-id="230df-199">既定では、HTTP REPL には、使用するように構成されたテキスト エディターはありません。</span><span class="sxs-lookup"><span data-stu-id="230df-199">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="230df-200">HTTP 要求本文を必要とする Web API メソッドをテストするには、既定のテキスト エディターを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="230df-200">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="230df-201">HTTP REPL ツールでは、要求本文を作成する目的のためだけに構成されたテキスト エディターが起動されます。</span><span class="sxs-lookup"><span data-stu-id="230df-201">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="230df-202">次のコマンドを実行して、優先テキストエディターを既定値として設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-202">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="230df-203">上記のコマンドで、`<EXECUTABLE>` はテキスト エディターの実行可能ファイルへの完全なパスです。</span><span class="sxs-lookup"><span data-stu-id="230df-203">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="230df-204">たとえば、次のコマンドを実行して、既定のテキストエディターとして Visual Studio Code を設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-204">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="230df-205">Linux</span><span class="sxs-lookup"><span data-stu-id="230df-205">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="230df-206">macOS</span><span class="sxs-lookup"><span data-stu-id="230df-206">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="230df-207">Windows</span><span class="sxs-lookup"><span data-stu-id="230df-207">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="230df-208">特定の CLI 引数を使用して既定のテキスト エディターを起動するには、`editor.command.default.arguments` キーを設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-208">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="230df-209">たとえば、Visual Studio Code が既定のテキスト エディターで、拡張機能が無効になっている新しいセッションでは HTTP REPL で常に Visual Studio Code を開きたいとします。</span><span class="sxs-lookup"><span data-stu-id="230df-209">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="230df-210">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-210">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="230df-211">HTTP GET 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-211">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-212">構文</span><span class="sxs-lookup"><span data-stu-id="230df-212">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-213">引数</span><span class="sxs-lookup"><span data-stu-id="230df-213">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-214">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-214">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-215">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-215">Options</span></span>

<span data-ttu-id="230df-216">`get` コマンドには以下のオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="230df-216">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="230df-217">例</span><span class="sxs-lookup"><span data-stu-id="230df-217">Example</span></span>

<span data-ttu-id="230df-218">HTTP GET 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="230df-218">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="230df-219">それをサポートしているエンドポイントで `get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-219">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="230df-220">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-220">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="230df-221">`get` コマンドにパラメーターを渡すことによって、特定のレコードを取得します。</span><span class="sxs-lookup"><span data-stu-id="230df-221">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="230df-222">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-222">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="230df-223">HTTP POST 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-223">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-224">構文</span><span class="sxs-lookup"><span data-stu-id="230df-224">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-225">引数</span><span class="sxs-lookup"><span data-stu-id="230df-225">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-226">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-226">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-227">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-227">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="230df-228">例</span><span class="sxs-lookup"><span data-stu-id="230df-228">Example</span></span>

<span data-ttu-id="230df-229">HTTP POST 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="230df-229">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="230df-230">それをサポートしているエンドポイントで `post` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-230">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="230df-231">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="230df-231">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="230df-232">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="230df-232">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="230df-233">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-233">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="230df-234">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="230df-234">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="230df-235">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="230df-235">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="230df-236">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="230df-236">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="230df-237">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-237">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="230df-238">HTTP PUT 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-238">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-239">構文</span><span class="sxs-lookup"><span data-stu-id="230df-239">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-240">引数</span><span class="sxs-lookup"><span data-stu-id="230df-240">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-241">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-241">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-242">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-242">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="230df-243">例</span><span class="sxs-lookup"><span data-stu-id="230df-243">Example</span></span>

<span data-ttu-id="230df-244">HTTP PUT 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="230df-244">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="230df-245">*省略可能*:変更前にデータを表示するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-245">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="230df-246">前のコマンドでは、`Content-Type` HTTP 要求ヘッダーが JSON の要求本文メディアの種類を示すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="230df-246">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="230df-247">既定のテキスト エディターでは、HTTP 要求本文を表す JSON テンプレートを含む *.tmp* ファイルが開かれます。</span><span class="sxs-lookup"><span data-stu-id="230df-247">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="230df-248">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-248">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="230df-249">既定のテキスト エディターを設定するには、「[既定のテキスト エディターを設定する](#set-the-default-text-editor)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="230df-249">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="230df-250">モデルの検証要件を満たすように JSON テンプレートを変更します。</span><span class="sxs-lookup"><span data-stu-id="230df-250">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="230df-251">*.tmp* ファイルを保存して、テキスト エディターを閉じます。</span><span class="sxs-lookup"><span data-stu-id="230df-251">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="230df-252">コマンド シェルに次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-252">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="230df-253">*省略可能*:変更を確認するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-253">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="230df-254">たとえば、テキスト エディターで「Cherry」と入力すると、`get` は次を返します。</span><span class="sxs-lookup"><span data-stu-id="230df-254">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="230df-255">HTTP DELETE 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-255">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-256">構文</span><span class="sxs-lookup"><span data-stu-id="230df-256">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-257">引数</span><span class="sxs-lookup"><span data-stu-id="230df-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-258">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-259">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="230df-260">例</span><span class="sxs-lookup"><span data-stu-id="230df-260">Example</span></span>

<span data-ttu-id="230df-261">HTTP DELETE 要求を発行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="230df-261">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="230df-262">*省略可能*:変更前にデータを表示するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="230df-263">上記のコマンドでは、次の出力形式が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-263">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="230df-264">*省略可能*:変更を確認するには、`get` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-264">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="230df-265">この例では、`get` は次を返します。</span><span class="sxs-lookup"><span data-stu-id="230df-265">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="230df-266">HTTP PATCH 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-266">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-267">構文</span><span class="sxs-lookup"><span data-stu-id="230df-267">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-268">引数</span><span class="sxs-lookup"><span data-stu-id="230df-268">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-269">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-269">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-270">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-270">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="230df-271">HTTP HEAD 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-271">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-272">構文</span><span class="sxs-lookup"><span data-stu-id="230df-272">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-273">引数</span><span class="sxs-lookup"><span data-stu-id="230df-273">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-274">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-274">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-275">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-275">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="230df-276">HTTP OPTIONS 要求をテストする</span><span class="sxs-lookup"><span data-stu-id="230df-276">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="230df-277">構文</span><span class="sxs-lookup"><span data-stu-id="230df-277">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="230df-278">引数</span><span class="sxs-lookup"><span data-stu-id="230df-278">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="230df-279">関連付けられたコントローラー アクション メソッドで求められるルート パラメーター (存在する場合)。</span><span class="sxs-lookup"><span data-stu-id="230df-279">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="230df-280">オプション</span><span class="sxs-lookup"><span data-stu-id="230df-280">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="230df-281">HTTP 要求ヘッダーを設定する</span><span class="sxs-lookup"><span data-stu-id="230df-281">Set HTTP request headers</span></span>

<span data-ttu-id="230df-282">HTTP 要求ヘッダーを設定するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="230df-282">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="230df-283">HTTP 要求でインラインを設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-283">Set inline with the HTTP request.</span></span> <span data-ttu-id="230df-284">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-284">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="230df-285">上記の方法では、個別の HTTP 要求ヘッダーごとに独自の `-h` オプションが必要です。</span><span class="sxs-lookup"><span data-stu-id="230df-285">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="230df-286">HTTP 要求を送信する前に設定します。</span><span class="sxs-lookup"><span data-stu-id="230df-286">Set before sending the HTTP request.</span></span> <span data-ttu-id="230df-287">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-287">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="230df-288">要求を送信する前にヘッダーを設定すると、コマンド シェル セッションの間はヘッダーが設定されたままになります。</span><span class="sxs-lookup"><span data-stu-id="230df-288">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="230df-289">ヘッダーをクリアするには、空の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="230df-289">To clear the header, provide an empty value.</span></span> <span data-ttu-id="230df-290">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-290">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="230df-291">HTTP 要求の表示を切り替える</span><span class="sxs-lookup"><span data-stu-id="230df-291">Toggle HTTP request display</span></span>

<span data-ttu-id="230df-292">既定では、送信中の HTTP 要求の表示は抑制されます。</span><span class="sxs-lookup"><span data-stu-id="230df-292">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="230df-293">コマンド シェル セッションの期間中は、対応する設定を変更できます。</span><span class="sxs-lookup"><span data-stu-id="230df-293">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="230df-294">要求の表示を有効にする</span><span class="sxs-lookup"><span data-stu-id="230df-294">Enable request display</span></span>

<span data-ttu-id="230df-295">`echo on` コマンドを実行して、送信中の HTTP 要求を表示します。</span><span class="sxs-lookup"><span data-stu-id="230df-295">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="230df-296">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-296">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="230df-297">現在のセッションの後続の HTTP 要求では、要求ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-297">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="230df-298">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-298">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="230df-299">要求の表示を無効にする</span><span class="sxs-lookup"><span data-stu-id="230df-299">Disable request display</span></span>

<span data-ttu-id="230df-300">`echo off` コマンドを実行して、送信中の HTTP 要求の表示を抑制します。</span><span class="sxs-lookup"><span data-stu-id="230df-300">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="230df-301">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-301">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="230df-302">スクリプトを実行する</span><span class="sxs-lookup"><span data-stu-id="230df-302">Run a script</span></span>

<span data-ttu-id="230df-303">HTTP REPL コマンドの同じセットを頻繁に実行する場合は、それらをテキスト ファイルに格納することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="230df-303">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="230df-304">ファイル内のコマンドは、コマンド ラインで手動で実行した場合と同じ形式になります。</span><span class="sxs-lookup"><span data-stu-id="230df-304">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="230df-305">これらのコマンドは、`run` コマンドを使用してバッチ方式で実行できます。</span><span class="sxs-lookup"><span data-stu-id="230df-305">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="230df-306">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-306">For example:</span></span>

1. <span data-ttu-id="230df-307">改行で区切られた一連のコマンドを含むテキスト ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="230df-307">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="230df-308">例として、次のコマンドを含む *people-script.txt* ファイルについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="230df-308">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="230df-309">`run` コマンドを実行し、テキスト ファイルのパスを渡します。</span><span class="sxs-lookup"><span data-stu-id="230df-309">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="230df-310">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="230df-310">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="230df-311">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="230df-311">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="230df-312">出力を消去する</span><span class="sxs-lookup"><span data-stu-id="230df-312">Clear the output</span></span>

<span data-ttu-id="230df-313">HTTP REPL ツールによってコマンド シェルに書き込まれたすべての出力を削除するには、`clear` コマンドまたは `cls` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="230df-313">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="230df-314">例として、コマンド シェルに次の出力が含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="230df-314">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="230df-315">次のコマンドを実行して、出力を消去します。</span><span class="sxs-lookup"><span data-stu-id="230df-315">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="230df-316">上記のコマンドを実行すると、コマンド シェルには次の出力のみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="230df-316">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="230df-317">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="230df-317">Additional resources</span></span>

* [<span data-ttu-id="230df-318">REST API 要求</span><span class="sxs-lookup"><span data-stu-id="230df-318">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="230df-319">HTTP REPL GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="230df-319">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
