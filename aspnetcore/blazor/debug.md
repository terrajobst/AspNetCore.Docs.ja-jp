---
title: デバッグ ASP.NET Core Blazor
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: blazor/debug
ms.openlocfilehash: 3096ad9b3a6904804f239d61f374adcd4d851978
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963148"
---
# <a name="debug-aspnet-core-opno-locblazor"></a><span data-ttu-id="add5d-103">デバッグ ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="add5d-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="add5d-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="add5d-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="add5d-105">Chrome では、WebAssembly 実行されるアプリ Blazor デバッグのための*早期*サポートが存在します。</span><span class="sxs-lookup"><span data-stu-id="add5d-105">*Early* support exists for debugging Blazor WebAssembly apps running on WebAssembly in Chrome.</span></span>

<span data-ttu-id="add5d-106">デバッガーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="add5d-106">Debugger capabilities are limited.</span></span> <span data-ttu-id="add5d-107">次のようなシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="add5d-107">Available scenarios include:</span></span>

* <span data-ttu-id="add5d-108">ブレークポイントを設定および削除します。</span><span class="sxs-lookup"><span data-stu-id="add5d-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="add5d-109">コードを使用するか、コードの実行を再開 (`F8`) する (`F10`)。</span><span class="sxs-lookup"><span data-stu-id="add5d-109">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="add5d-110">*[ローカル] 画面で*、型 `int`、`string`、`bool` のすべてのローカル変数の値を確認します。</span><span class="sxs-lookup"><span data-stu-id="add5d-110">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="add5d-111">呼び出し履歴を参照してください。これには、JavaScript から .NET への呼び出しチェーンや、.NET から JavaScript までの呼び出し履歴が含まれます。</span><span class="sxs-lookup"><span data-stu-id="add5d-111">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="add5d-112">次のこと*はできません*。</span><span class="sxs-lookup"><span data-stu-id="add5d-112">You *can't*:</span></span>

* <span data-ttu-id="add5d-113">`int`、`string`、`bool`ではないローカルの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="add5d-113">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="add5d-114">クラスのプロパティまたはフィールドの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="add5d-114">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="add5d-115">変数の上にマウスポインターを合わせると、値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="add5d-115">Hover over variables to see their values.</span></span>
* <span data-ttu-id="add5d-116">コンソールで式を評価します。</span><span class="sxs-lookup"><span data-stu-id="add5d-116">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="add5d-117">非同期呼び出し間でステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="add5d-117">Step across async calls.</span></span>
* <span data-ttu-id="add5d-118">その他の一般的なデバッグシナリオを実行します。</span><span class="sxs-lookup"><span data-stu-id="add5d-118">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="add5d-119">さらにデバッグを行うシナリオの開発は、エンジニアリングチームにとって重視されています。</span><span class="sxs-lookup"><span data-stu-id="add5d-119">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="add5d-120">必要条件</span><span class="sxs-lookup"><span data-stu-id="add5d-120">Prerequisites</span></span>

<span data-ttu-id="add5d-121">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="add5d-121">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="add5d-122">Google Chrome (バージョン70以降)</span><span class="sxs-lookup"><span data-stu-id="add5d-122">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="add5d-123">Microsoft Edge preview ([エッジ開発チャネル](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="add5d-123">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="add5d-124">プロシージャ</span><span class="sxs-lookup"><span data-stu-id="add5d-124">Procedure</span></span>

1. <span data-ttu-id="add5d-125">`Debug` 構成で Blazor Webasアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="add5d-125">Run a Blazor WebAssembly app in `Debug` configuration.</span></span> <span data-ttu-id="add5d-126">`--configuration Debug` オプションを[dotnet run](/dotnet/core/tools/dotnet-run)コマンドに渡します。 `dotnet run --configuration Debug`。</span><span class="sxs-lookup"><span data-stu-id="add5d-126">Pass the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="add5d-127">ブラウザーでアプリにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="add5d-127">Access the app in the browser.</span></span>
1. <span data-ttu-id="add5d-128">開発者ツールパネルではなく、アプリにキーボードフォーカスを置きます。</span><span class="sxs-lookup"><span data-stu-id="add5d-128">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="add5d-129">[開発者ツール] パネルは、デバッグを開始したときに閉じることができます。</span><span class="sxs-lookup"><span data-stu-id="add5d-129">The developer tools panel can be closed when debugging is initiated.</span></span>
1. <span data-ttu-id="add5d-130">次の Blazor固有のキーボードショートカットを選択します。</span><span class="sxs-lookup"><span data-stu-id="add5d-130">Select the following Blazor-specific keyboard shortcut:</span></span>
   * <span data-ttu-id="add5d-131">`Shift+Alt+D` (Windows/Linux)</span><span class="sxs-lookup"><span data-stu-id="add5d-131">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="add5d-132">macOS の `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="add5d-132">`Shift+Cmd+D` on macOS</span></span>
1. <span data-ttu-id="add5d-133">画面に表示されている手順に従って、リモートデバッグが有効になっているブラウザーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="add5d-133">Follow the steps listed on the screen to restart the browser with remote debugging enabled.</span></span>
1. <span data-ttu-id="add5d-134">次の Blazor固有のキーボードショートカットをもう一度選択して、デバッグセッションを開始します。</span><span class="sxs-lookup"><span data-stu-id="add5d-134">Select the following Blazor-specific keyboard shortcut once again to start the debug session:</span></span>
   * <span data-ttu-id="add5d-135">`Shift+Alt+D` (Windows/Linux)</span><span class="sxs-lookup"><span data-stu-id="add5d-135">`Shift+Alt+D` on Windows/Linux</span></span>
   * <span data-ttu-id="add5d-136">macOS の `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="add5d-136">`Shift+Cmd+D` on macOS</span></span>

## <a name="enable-remote-debugging"></a><span data-ttu-id="add5d-137">リモートデバッグを有効にする</span><span class="sxs-lookup"><span data-stu-id="add5d-137">Enable remote debugging</span></span>

<span data-ttu-id="add5d-138">リモートデバッグが無効になっている場合は、[デバッグ可能な**ブラウザー] タブ**のエラーページが Chrome によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="add5d-138">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="add5d-139">このエラーページには、デバッグポートを開いた状態で Chrome を実行し、Blazor デバッグプロキシがアプリに接続できるようにするための手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="add5d-139">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="add5d-140">*Chrome インスタンスをすべて閉じ*、指示に従って chrome を再起動します。</span><span class="sxs-lookup"><span data-stu-id="add5d-140">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="add5d-141">アプリをデバッグする</span><span class="sxs-lookup"><span data-stu-id="add5d-141">Debug the app</span></span>

<span data-ttu-id="add5d-142">リモートデバッグが有効になっている状態で Chrome を実行すると、デバッグ キーボードショートカットによって新しい デバッガー タブが開きます。しばらくすると、**ソース** タブにアプリ内の .net アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="add5d-142">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="add5d-143">各アセンブリを展開し、デバッグに使用できる *.cs*/ *. razor*ソースファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="add5d-143">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="add5d-144">ブレークポイントを設定し、アプリのタブに戻ります。ブレークポイントは、コードの実行時にヒットします。</span><span class="sxs-lookup"><span data-stu-id="add5d-144">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="add5d-145">ブレークポイントにヒットした後、コードを実行するか (`F8`) コードを正常に実行するかを、1ステップ (`F10`) にします。</span><span class="sxs-lookup"><span data-stu-id="add5d-145">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="add5d-146"> には、 [Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、でプロトコルを補強するデバッグプロキシが用意されています。NET 固有の情報。</span><span class="sxs-lookup"><span data-stu-id="add5d-146"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="add5d-147">デバッグ用のキーボードショートカットが押されると、Blazor は、プロキシで Chrome DevTools をポイントします。</span><span class="sxs-lookup"><span data-stu-id="add5d-147">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="add5d-148">プロキシは、デバッグしようとしているブラウザーウィンドウに接続します (したがって、リモートデバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="add5d-148">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="add5d-149">ブラウザーソースマップ</span><span class="sxs-lookup"><span data-stu-id="add5d-149">Browser source maps</span></span>

<span data-ttu-id="add5d-150">ブラウザーソースマップを使用すると、ブラウザーはコンパイルされたファイルを元のソースファイルにマップでき、クライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="add5d-150">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="add5d-151">ただし、現在、Blazor はC# JavaScript/wasm に直接マップされていません。</span><span class="sxs-lookup"><span data-stu-id="add5d-151">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="add5d-152">代わりに、Blazor はブラウザー内で IL を解釈するため、ソースマップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="add5d-152">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshooting-tip"></a><span data-ttu-id="add5d-153">トラブルシューティングのヒント</span><span class="sxs-lookup"><span data-stu-id="add5d-153">Troubleshooting tip</span></span>

<span data-ttu-id="add5d-154">エラーが発生した場合は、次のヒントを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="add5d-154">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="add5d-155">**[デバッガー]** タブで、ブラウザーで開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="add5d-155">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="add5d-156">コンソールで `localStorage.clear()` を実行して、ブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="add5d-156">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
