---
title: ASP.NET Core Blazor をデバッグする
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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648314"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="84083-103">ASP.NET Core Blazor をデバッグする</span><span class="sxs-lookup"><span data-stu-id="84083-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="84083-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="84083-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="84083-105">Chromium ベースのブラウザー (Chrome/Edge) で、ブラウザー開発ツールを使用して Blazor WebAssembly をデバッグするために、*初期*のサポートが存在します。</span><span class="sxs-lookup"><span data-stu-id="84083-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="84083-106">以下のための作業が進行中です。</span><span class="sxs-lookup"><span data-stu-id="84083-106">Work is in progress to:</span></span>

* <span data-ttu-id="84083-107">Visual Studio でのデバッグを完全に可能にする。</span><span class="sxs-lookup"><span data-stu-id="84083-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="84083-108">Visual Studio Code でのデバッグを可能にする。</span><span class="sxs-lookup"><span data-stu-id="84083-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="84083-109">デバッガーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="84083-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="84083-110">利用可能なシナリオには以下が含まれます。</span><span class="sxs-lookup"><span data-stu-id="84083-110">Available scenarios include:</span></span>

* <span data-ttu-id="84083-111">ブレークポイントの設定と削除を行う。</span><span class="sxs-lookup"><span data-stu-id="84083-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="84083-112">コード全体でステップ実行する (`F10`)、コードの実行を再開 (`F8`) する。</span><span class="sxs-lookup"><span data-stu-id="84083-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="84083-113">*Locals* の表示で、`int`、`string`、`bool` 型の任意のローカル変数の値を観察する。</span><span class="sxs-lookup"><span data-stu-id="84083-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="84083-114">呼び出し履歴を表示する。これには、JavaScript から .NET への呼び出しチェーンと、.NET から JavaScript への呼び出しチェーンが含まれます。</span><span class="sxs-lookup"><span data-stu-id="84083-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="84083-115">以下のことは*行えません*。</span><span class="sxs-lookup"><span data-stu-id="84083-115">You *can't*:</span></span>

* <span data-ttu-id="84083-116">`int`、`string`、`bool` ではないローカルの値を観察する。</span><span class="sxs-lookup"><span data-stu-id="84083-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="84083-117">クラスのプロパティまたはフィールドの値を観察する。</span><span class="sxs-lookup"><span data-stu-id="84083-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="84083-118">変数をポイントしてその値を表示する。</span><span class="sxs-lookup"><span data-stu-id="84083-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="84083-119">コンソール内で式を評価する。</span><span class="sxs-lookup"><span data-stu-id="84083-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="84083-120">複数の非同期呼び出しにわたってステップ実行する。</span><span class="sxs-lookup"><span data-stu-id="84083-120">Step across async calls.</span></span>
* <span data-ttu-id="84083-121">その他の最も一般的なデバッグ シナリオを実行する。</span><span class="sxs-lookup"><span data-stu-id="84083-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="84083-122">エンジニアリング チームでは、これ以上のデバッグ シナリオの開発が引き続き重視されています。</span><span class="sxs-lookup"><span data-stu-id="84083-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84083-123">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84083-123">Prerequisites</span></span>

<span data-ttu-id="84083-124">デバッグには、次のいずれかのブラウザーが必要です。</span><span class="sxs-lookup"><span data-stu-id="84083-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="84083-125">Google Chrome (バージョン 70 以降)</span><span class="sxs-lookup"><span data-stu-id="84083-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="84083-126">Microsoft Edge プレビュー ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span><span class="sxs-lookup"><span data-stu-id="84083-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="84083-127">プロシージャ</span><span class="sxs-lookup"><span data-stu-id="84083-127">Procedure</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84083-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84083-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="84083-129">Visual Studio でのデバッグのサポートは、開発の初期段階にあります。</span><span class="sxs-lookup"><span data-stu-id="84083-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="84083-130">**F5** でのデバッグは現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="84083-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="84083-131">デバッグを行わずに `Debug` 構成で Blazor WebAssembly アプリを実行します (**F5** ではなく **Ctrl**+**F5**)。</span><span class="sxs-lookup"><span data-stu-id="84083-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="84083-132">アプリの [デバッグ] プロパティ ( **[デバッグ]** メニューの最後のエントリ) を開き、HTTP の **[アプリ URL]** をコピーします。</span><span class="sxs-lookup"><span data-stu-id="84083-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="84083-133">Chromium ベースのブラウザー (Edge ベータまたは Chrome) を使用して、アプリの HTTP アドレス (HTTPS アドレスではありません) を参照します。</span><span class="sxs-lookup"><span data-stu-id="84083-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="84083-134">開発者ツール パネルではなく、ブラウザーのウィンドウで、キーボードのフォーカスをアプリに合わせます。</span><span class="sxs-lookup"><span data-stu-id="84083-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="84083-135">この手順では、開発者ツール パネルを閉じたままにしておくのが適切です。</span><span class="sxs-lookup"><span data-stu-id="84083-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="84083-136">デバッグが開始された後で、開発者ツール パネルを再び開くことができます。</span><span class="sxs-lookup"><span data-stu-id="84083-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="84083-137">以下の Blazor 固有のキーボード ショートカットを選択します。</span><span class="sxs-lookup"><span data-stu-id="84083-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="84083-138">Windows の場合: `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="84083-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="84083-139">macOS の場合: `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="84083-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="84083-140">**[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** と表示される場合は、「[リモート デバッグの有効化](#enable-remote-debugging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84083-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="84083-141">リモート デバッグの有効化後:</span><span class="sxs-lookup"><span data-stu-id="84083-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="84083-142">1\.</span><span class="sxs-lookup"><span data-stu-id="84083-142">1\.</span></span> <span data-ttu-id="84083-143">新しいブラウザー ウィンドウが開きます。</span><span class="sxs-lookup"><span data-stu-id="84083-143">A new browser window opens.</span></span> <span data-ttu-id="84083-144">前のウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="84083-144">Close the prior window.</span></span>

   <span data-ttu-id="84083-145">2\.</span><span class="sxs-lookup"><span data-stu-id="84083-145">2\.</span></span> <span data-ttu-id="84083-146">ブラウザーのウィンドウで、キーボードのフォーカスをアプリに合わせます。</span><span class="sxs-lookup"><span data-stu-id="84083-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="84083-147">3\.</span><span class="sxs-lookup"><span data-stu-id="84083-147">3\.</span></span> <span data-ttu-id="84083-148">新しいブラウザー ウィンドウで Blazor 固有のキーボード ショートカットを選択します。Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。</span><span class="sxs-lookup"><span data-stu-id="84083-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="84083-149">4\.</span><span class="sxs-lookup"><span data-stu-id="84083-149">4\.</span></span> <span data-ttu-id="84083-150">ブラウザーに **[DevTools]** タブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="84083-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="84083-151">**ブラウザー ウィンドウでアプリのタブを再度選択します。**</span><span class="sxs-lookup"><span data-stu-id="84083-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="84083-152">アプリを Visual Studio にアタッチするには、「[Visual Studio でプロセスにアタッチする](#attach-to-process-in-visual-studio)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="84083-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="84083-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="84083-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="84083-154">`--configuration Debug` オプションを [dotnet run](/dotnet/core/tools/dotnet-run) コマンドに渡すことによって、`Debug` 構成で Blazor WebAssembly アプリを実行します: `dotnet run --configuration Debug`。</span><span class="sxs-lookup"><span data-stu-id="84083-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="84083-155">シェルのウィンドウに表示された HTTP URL にあるアプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="84083-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="84083-156">開発者ツール パネルではなく、アプリにキーボードのフォーカスを合わせます。</span><span class="sxs-lookup"><span data-stu-id="84083-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="84083-157">この手順では、開発者ツール パネルを閉じたままにしておくのが適切です。</span><span class="sxs-lookup"><span data-stu-id="84083-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="84083-158">デバッグが開始された後で、開発者ツール パネルを再び開くことができます。</span><span class="sxs-lookup"><span data-stu-id="84083-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="84083-159">以下の Blazor 固有のキーボード ショートカットを選択します。</span><span class="sxs-lookup"><span data-stu-id="84083-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="84083-160">Windows の場合: `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="84083-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="84083-161">macOS の場合: `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="84083-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="84083-162">**[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** と表示される場合は、「[リモート デバッグの有効化](#enable-remote-debugging)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="84083-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="84083-163">リモート デバッグの有効化後:</span><span class="sxs-lookup"><span data-stu-id="84083-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="84083-164">1\.</span><span class="sxs-lookup"><span data-stu-id="84083-164">1\.</span></span> <span data-ttu-id="84083-165">新しいブラウザー ウィンドウが開きます。</span><span class="sxs-lookup"><span data-stu-id="84083-165">A new browser window opens.</span></span> <span data-ttu-id="84083-166">前のウィンドウを閉じます。</span><span class="sxs-lookup"><span data-stu-id="84083-166">Close the prior window.</span></span>

   <span data-ttu-id="84083-167">2\.</span><span class="sxs-lookup"><span data-stu-id="84083-167">2\.</span></span> <span data-ttu-id="84083-168">開発者ツール パネルではなく、ブラウザーのウィンドウで、キーボードのフォーカスをアプリに合わせます。</span><span class="sxs-lookup"><span data-stu-id="84083-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="84083-169">3\.</span><span class="sxs-lookup"><span data-stu-id="84083-169">3\.</span></span> <span data-ttu-id="84083-170">新しいブラウザー ウィンドウで Blazor 固有のキーボード ショートカットを選択します。Windows の場合は `Shift+Alt+D`、macOS の場合は `Shift+Cmd+D` です。</span><span class="sxs-lookup"><span data-stu-id="84083-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="84083-171">リモート デバッグの有効化</span><span class="sxs-lookup"><span data-stu-id="84083-171">Enable remote debugging</span></span>

<span data-ttu-id="84083-172">リモートデバッグが無効になっている場合、 **[Unable to find debuggable browser tab] (デバッグ可能なブラウザー タブが見つかりません)** というエラー ページが Chrome によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="84083-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="84083-173">エラー ページには、Blazor デバッグ プロキシがアプリに接続できるように、デバッグ ポートを開いた状態で Chrome を実行するための手順が記載されています。</span><span class="sxs-lookup"><span data-stu-id="84083-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="84083-174">*すべての Chrome インスタンスを閉じ*、指示のとおり Chrome を再起動します。</span><span class="sxs-lookup"><span data-stu-id="84083-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="84083-175">アプリをデバッグする</span><span class="sxs-lookup"><span data-stu-id="84083-175">Debug the app</span></span>

<span data-ttu-id="84083-176">リモート デバッグが有効な状態で Chrome を実行すると、デバッグ用のキーボード ショートカットによって新しいデバッガー タブが開きます。少しすると、 **[ソース]** タブに、アプリ内の .NET アセンブリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84083-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="84083-177">各アセンブリを展開して、デバッグに使用できる *.cs*/ *.razor* ソース ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="84083-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="84083-178">ブレークポイントを設定し、アプリのタブに戻ります。コードの実行時にブレークポイントにヒットします。</span><span class="sxs-lookup"><span data-stu-id="84083-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="84083-179">ブレークポイントにヒットした後、コード全体をステップ実行する (`F10`) か、コードの実行を普通に再開 (`F8`) します。</span><span class="sxs-lookup"><span data-stu-id="84083-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="84083-180"> は、[Chrome DevTools プロトコル](https://chromedevtools.github.io/devtools-protocol/)を実装し、.NET 固有の情報によってプロトコルを拡張するデバッグ プロキシを備えています。</span><span class="sxs-lookup"><span data-stu-id="84083-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="84083-181">デバッグ用のキーボード ショートカットが押されると、Blazor はプロキシで Chrome DevTools を指し示します。</span><span class="sxs-lookup"><span data-stu-id="84083-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="84083-182">プロキシは、デバッグしようとしているブラウザー ウィンドウに接続します (そのためリモート デバッグを有効にする必要があります)。</span><span class="sxs-lookup"><span data-stu-id="84083-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="84083-183">Visual Studio でプロセスにアタッチする</span><span class="sxs-lookup"><span data-stu-id="84083-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="84083-184">Visual Studio でのアプリのプロセスへのアタッチは、Blazor WebAssembly 用の*一時的*なデバッグ シナリオです。一方、**F5** によるデバッグは開発中です。</span><span class="sxs-lookup"><span data-stu-id="84083-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="84083-185">実行中のアプリのプロセスを Visual Studio にアタッチするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="84083-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="84083-186">Visual Studio で、 **[デバッグ]**  >  **[プロセスにアタッチ]** と選択します。</span><span class="sxs-lookup"><span data-stu-id="84083-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="84083-187">**[接続の種類]** で、 **[Chrome devtools protocol websocket (no authentication)] (Chrome DevTools Protocol Websocket (認証なし))** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84083-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="84083-188">**[接続先]** には、アプリの HTTP アドレス (HTTPS アドレスではありません) を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="84083-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="84083-189">**[最新の情報に更新]** を選択し、 **[選択可能なプロセス]** のエントリを最新の情報に更新します。</span><span class="sxs-lookup"><span data-stu-id="84083-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="84083-190">デバッグするブラウザー プロセスを選択し、 **[アタッチ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84083-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="84083-191">**[コードの種類の選択]** ダイアログで、アタッチ先の特定ブラウザーのコードの種類 (Edge または Chrome) を選択し、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84083-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="84083-192">ブラウザー ソース マップ</span><span class="sxs-lookup"><span data-stu-id="84083-192">Browser source maps</span></span>

<span data-ttu-id="84083-193">ブラウザー ソース マップを使用すると、ブラウザーのコンパイルされたファイルを元のソース ファイルにマップし直すことができ、これはクライアント側のデバッグによく使用されます。</span><span class="sxs-lookup"><span data-stu-id="84083-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="84083-194">ただし Blazor では現在、C# は JavaScript/WASM に直接マップされません。</span><span class="sxs-lookup"><span data-stu-id="84083-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="84083-195">その代わり、Blazor はブラウザー内で中間言語の解釈を行うため、ソース マップは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="84083-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="84083-196">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="84083-196">Troubleshoot</span></span>

<span data-ttu-id="84083-197">エラーが発生している場合は、次のヒントが役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="84083-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="84083-198">**[デバッガー]** タブで、ブラウザー内の開発者ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="84083-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="84083-199">コンソールで `localStorage.clear()` を実行して、任意のブレークポイントを削除します。</span><span class="sxs-lookup"><span data-stu-id="84083-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
