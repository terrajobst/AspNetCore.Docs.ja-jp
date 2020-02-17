---
title: デバッグ ASP.NET Core Blazor
author: guardrex
description: Blazor アプリをデバッグする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159990"
---
# <a name="debug-aspnet-core-opno-locblazor"></a><span data-ttu-id="f88ec-103">デバッグ ASP.NET Core [!OP.NO-LOC(Blazor)]</span><span class="sxs-lookup"><span data-stu-id="f88ec-103">Debug ASP.NET Core [!OP.NO-LOC(Blazor)]</span></span>

[<span data-ttu-id="f88ec-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="f88ec-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f88ec-105">Chromium ベースのブラウザー (Chrome/Microsoft Edge) でブラウザー開発ツールを使用することにより、[!OP.NO-LOC(Blazor)] デバッグのための*早期*サポートが導入されています。</span><span class="sxs-lookup"><span data-stu-id="f88ec-105">*Early* support exists for debugging [!OP.NO-LOC(Blazor)] WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="f88ec-106">次の作業が進行中です:</span><span class="sxs-lookup"><span data-stu-id="f88ec-106">Work is in progress to:</span></span>

* <span data-ttu-id="f88ec-107">Visual Studio でのデバッグを完全に有効にします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="f88ec-108">Visual Studio Code でデバッグを有効にします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="f88ec-109">デバッガーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="f88ec-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="f88ec-110">次のようなシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="f88ec-110">Available scenarios include:</span></span>

* <span data-ttu-id="f88ec-111">ブレークポイントを設定および削除します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="f88ec-112">コードを使用するか、コードの実行を再開 (`F8`) する (`F10`)。</span><span class="sxs-lookup"><span data-stu-id="f88ec-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="f88ec-113">*[ローカル] 表示で*、`int`、`string`、および `bool`型のローカル変数の値を確認します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="f88ec-114">呼び出し履歴を参照してください。これには、JavaScript から .NET への呼び出しチェーンや、.NET から JavaScript までの呼び出し履歴が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="f88ec-115">次のこと*はできません*。</span><span class="sxs-lookup"><span data-stu-id="f88ec-115">You *can't*:</span></span>

* <span data-ttu-id="f88ec-116">`int`、`string`、`bool`ではないローカルの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="f88ec-117">クラスのプロパティまたはフィールドの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="f88ec-118">変数の上にマウスポインターを合わせると、値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="f88ec-119">コンソールで式を評価します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="f88ec-120">非同期呼び出し間でステップ実行します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-120">Step across async calls.</span></span>
* <span data-ttu-id="f88ec-121">その他の一般的なデバッグシナリオを実行します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="f88ec-122">さらにデバッグを行うシナリオの開発は、エンジニアリングチームにとって重視されています。</span><span class="sxs-lookup"><span data-stu-id="f88ec-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f88ec-123">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="f88ec-123">Prerequisites</span></span>

<span data-ttu-id="f88ec-124">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="f88ec-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="f88ec-125">Google Chrome (バージョン70以降)</span><span class="sxs-lookup"><span data-stu-id="f88ec-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="f88ec-126">Microsoft Edge preview ([エッジ開発チャネル](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="f88ec-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="f88ec-127">プロシージャ</span><span class="sxs-lookup"><span data-stu-id="f88ec-127">Procedure</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f88ec-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f88ec-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="f88ec-129">Visual Studio でのデバッグのサポートは、開発の初期段階です。</span><span class="sxs-lookup"><span data-stu-id="f88ec-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="f88ec-130">**F5**のデバッグは現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f88ec-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="f88ec-131">デバッグせずに `Debug` の構成で [!OP.NO-LOC(Blazor)] WebAssembly を実行します ( **F5**キーではなく、Ctrl+**F5** **キーを押し**ます)。</span><span class="sxs-lookup"><span data-stu-id="f88ec-131">Run a [!OP.NO-LOC(Blazor)] WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="f88ec-132">アプリのデバッグプロパティ ( **[デバッグ]** メニューの最後のエントリ) を開き、HTTP**アプリの URL**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="f88ec-133">Chromium ベースのブラウザー (Microsoft Edge ベータまたは Chrome) を使用して、アプリの HTTP アドレス (HTTPS アドレスではない) を参照します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="f88ec-134">開発者ツールパネルではなく、ブラウザーウィンドウでキーボードフォーカスをアプリに配置します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="f88ec-135">この手順では、開発者ツールパネルを閉じたままにしておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="f88ec-136">デバッグが開始されたら、[開発者ツール] パネルを再び開くことができます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="f88ec-137">次の [!OP.NO-LOC(Blazor)]固有のキーボードショートカットを選択します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-137">Select the following [!OP.NO-LOC(Blazor)]-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="f88ec-138">Windows での `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="f88ec-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="f88ec-139">macOS の `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="f88ec-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="f88ec-140">[デバッグ可能な**ブラウザーを見つけることができません] タブ**が表示される場合は、「[リモートデバッグの有効化](#enable-remote-debugging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f88ec-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="f88ec-141">リモートデバッグを有効にした後:</span><span class="sxs-lookup"><span data-stu-id="f88ec-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="f88ec-142">1\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-142">1\.</span></span> <span data-ttu-id="f88ec-143">新しいブラウザー ウィンドウが開きます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-143">A new browser window opens.</span></span> <span data-ttu-id="f88ec-144">前のウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-144">Close the prior window.</span></span>

   <span data-ttu-id="f88ec-145">2\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-145">2\.</span></span> <span data-ttu-id="f88ec-146">ブラウザーウィンドウで、キーボードフォーカスをアプリに配置します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="f88ec-147">3\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-147">3\.</span></span> <span data-ttu-id="f88ec-148">新しいブラウザーウィンドウで [!OP.NO-LOC(Blazor)]固有のキーボードショートカットを選択します。 Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。</span><span class="sxs-lookup"><span data-stu-id="f88ec-148">Select the [!OP.NO-LOC(Blazor)]-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="f88ec-149">4\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-149">4\.</span></span> <span data-ttu-id="f88ec-150">**[Devtools]** タブがブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="f88ec-151">**ブラウザーウィンドウでアプリのタブを再度選択します。**</span><span class="sxs-lookup"><span data-stu-id="f88ec-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="f88ec-152">アプリを Visual Studio にアタッチするには、「 [Visual studio でプロセスにアタッチ](#attach-to-process-in-visual-studio)する」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f88ec-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f88ec-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f88ec-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f88ec-154">`--configuration Debug` オプションを[dotnet run](/dotnet/core/tools/dotnet-run)コマンドに渡すことによって `Debug` 構成で [!OP.NO-LOC(Blazor)] WebAssembly を実行します: `dotnet run --configuration Debug`。</span><span class="sxs-lookup"><span data-stu-id="f88ec-154">Run a [!OP.NO-LOC(Blazor)] WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="f88ec-155">シェルのウィンドウに表示されている HTTP URL でアプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="f88ec-156">開発者ツールパネルではなく、アプリにキーボードフォーカスを置きます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="f88ec-157">この手順では、開発者ツールパネルを閉じたままにしておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="f88ec-158">デバッグが開始されたら、[開発者ツール] パネルを再び開くことができます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="f88ec-159">次の [!OP.NO-LOC(Blazor)]固有のキーボードショートカットを選択します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-159">Select the following [!OP.NO-LOC(Blazor)]-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="f88ec-160">Windows での `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="f88ec-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="f88ec-161">macOS の `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="f88ec-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="f88ec-162">[デバッグ可能な**ブラウザーを見つけることができません] タブ**が表示される場合は、「[リモートデバッグの有効化](#enable-remote-debugging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f88ec-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="f88ec-163">リモートデバッグを有効にした後:</span><span class="sxs-lookup"><span data-stu-id="f88ec-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="f88ec-164">1\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-164">1\.</span></span> <span data-ttu-id="f88ec-165">新しいブラウザー ウィンドウが開きます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-165">A new browser window opens.</span></span> <span data-ttu-id="f88ec-166">前のウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-166">Close the prior window.</span></span>

   <span data-ttu-id="f88ec-167">2\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-167">2\.</span></span> <span data-ttu-id="f88ec-168">開発者ツールパネルではなく、ブラウザーウィンドウでキーボードフォーカスをアプリに配置します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="f88ec-169">3\.</span><span class="sxs-lookup"><span data-stu-id="f88ec-169">3\.</span></span> <span data-ttu-id="f88ec-170">新しいブラウザーウィンドウで [!OP.NO-LOC(Blazor)]固有のキーボードショートカットを選択します。 Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。</span><span class="sxs-lookup"><span data-stu-id="f88ec-170">Select the [!OP.NO-LOC(Blazor)]-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="f88ec-171">リモート デバッグの有効化</span><span class="sxs-lookup"><span data-stu-id="f88ec-171">Enable remote debugging</span></span>

<span data-ttu-id="f88ec-172">リモートデバッグが無効になっている場合は、[デバッグ可能な**ブラウザー] タブ**のエラーページが Chrome によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="f88ec-173">このエラーページには、デバッグポートを開いた状態で Chrome を実行し、Blazor デバッグプロキシがアプリに接続できるようにするための手順が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f88ec-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="f88ec-174">*Chrome インスタンスをすべて閉じ*、指示に従って chrome を再起動します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="f88ec-175">アプリをデバッグする</span><span class="sxs-lookup"><span data-stu-id="f88ec-175">Debug the app</span></span>

<span data-ttu-id="f88ec-176">リモートデバッグが有効になっている状態で Chrome を実行すると、デバッグ キーボードショートカットによって新しい デバッガー タブが開きます。しばらくすると、**ソース** タブにアプリ内の .net アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="f88ec-177">各アセンブリを展開し、デバッグに使用できる *.cs*/のソースファイルを見つけ*ます。*</span><span class="sxs-lookup"><span data-stu-id="f88ec-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="f88ec-178">ブレークポイントを設定し、アプリのタブに戻ります。ブレークポイントは、コードの実行時にヒットします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="f88ec-179">ブレークポイントにヒットした後、コードを実行するか (`F8`) コードを正常に実行 (`F10`) します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="f88ec-180"> には、 [Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、でプロトコルを補強するデバッグプロキシが用意されています。NET 固有の情報。</span><span class="sxs-lookup"><span data-stu-id="f88ec-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="f88ec-181">デバッグ用のキーボードショートカットが押されると、Blazor は、プロキシで Chrome DevTools をポイントします。</span><span class="sxs-lookup"><span data-stu-id="f88ec-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="f88ec-182">プロキシは、デバッグしようとしているブラウザーウィンドウに接続します (したがって、リモートデバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="f88ec-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="f88ec-183">Visual Studio でプロセスにアタッチ</span><span class="sxs-lookup"><span data-stu-id="f88ec-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="f88ec-184">Visual Studio でアプリのプロセスにアタッチすることは、 **F5 キーを押し**てデバッグを実行しているときに Blazor WebAssembly*一時的*なデバッグシナリオです。</span><span class="sxs-lookup"><span data-stu-id="f88ec-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="f88ec-185">実行中のアプリのプロセスを Visual Studio にアタッチするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="f88ec-186">Visual Studio で、**デバッグ** > **プロセスにアタッチ** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="f88ec-187">**[接続の種類]** で、 **[Chrome devtools protocol websocket (認証なし)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="f88ec-188">**接続ターゲット**として、アプリの HTTP アドレス (HTTPS アドレスではない) を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="f88ec-189">**[更新]** を選択して **[利用可能なプロセス]** のエントリを更新します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="f88ec-190">デバッグするブラウザープロセスを選択し、 **[アタッチ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="f88ec-191">**[コードの種類の選択**] ダイアログボックスで、アタッチしている特定のブラウザー (Edge または Chrome) のコードの種類を選択し、[ **OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="f88ec-192">ブラウザーソースマップ</span><span class="sxs-lookup"><span data-stu-id="f88ec-192">Browser source maps</span></span>

<span data-ttu-id="f88ec-193">ブラウザーソースマップを使用すると、ブラウザーはコンパイルされたファイルを元のソースファイルにマップでき、クライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="f88ec-194">ただし、現在、Blazor はC# JavaScript/wasm に直接マップされていません。</span><span class="sxs-lookup"><span data-stu-id="f88ec-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="f88ec-195">代わりに、Blazor はブラウザー内で IL を解釈するため、ソースマップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="f88ec-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f88ec-196">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f88ec-196">Troubleshoot</span></span>

<span data-ttu-id="f88ec-197">エラーが発生した場合は、次のヒントを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="f88ec-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="f88ec-198">**[デバッガー]** タブで、ブラウザーで開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="f88ec-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="f88ec-199">コンソールで `localStorage.clear()` を実行して、ブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="f88ec-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
