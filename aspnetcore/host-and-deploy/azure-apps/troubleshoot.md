---
title: Azure App Service での ASP.NET Core のトラブルシューティング
author: guardrex
description: ASP.NET Core Azure App Service の配置に関する問題を診断する方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: d78499c1a82a011239f6b62b546f304a5d5017e2
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313755"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="3461c-103">Azure App Service での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3461c-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="3461c-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3461c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="3461c-105">この記事では、Azure App Service の診断ツールを使って ASP.NET Core アプリの起動時の問題を診断する方法の手順を説明します。</span><span class="sxs-lookup"><span data-stu-id="3461c-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="3461c-106">追加のトラブルシューティングのアドバイスについては、Azure ドキュメントの「[Azure App Service 診断の概要](/azure/app-service/app-service-diagnostics)」と「[方法: Azure App Service のアプリの監視](/azure/app-service/web-sites-monitor)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3461c-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

<span data-ttu-id="3461c-107">その他のトラブルシューティング トピック:</span><span class="sxs-lookup"><span data-stu-id="3461c-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="3461c-108">IIS では、アプリをホストするために [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)も使用されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-108">IIS also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host apps.</span></span> <span data-ttu-id="3461c-109">IIS に特に関連するトラブルシューティング上のアドバイスについては、<xref:host-and-deploy/iis/troubleshoot> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3461c-109">For troubleshooting advice that pertains specifically to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>
* <span data-ttu-id="3461c-110"><xref:fundamentals/error-handling> では、ローカル システム上で開発しているときに発生する ASP.NET Core アプリのエラーを処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3461c-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="3461c-111">[Visual Studio を使用したデバッグ方法の学習](/visualstudio/debugger/getting-started-with-the-debugger)に関するページでは、Visual Studio デバッガーの機能を紹介しています。</span><span class="sxs-lookup"><span data-stu-id="3461c-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="3461c-112">[Visual Studio Code でのデバッグ](https://code.visualstudio.com/docs/editor/debugging)に関するページでは、Visual Studio Code に組み込まれているデバッグのサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3461c-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="3461c-113">アプリの起動エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3461c-113">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="3461c-114">アプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="3461c-114">Application Event Log</span></span>

<span data-ttu-id="3461c-115">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="3461c-115">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="3461c-116">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-116">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="3461c-117">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3461c-117">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="3461c-118">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="3461c-118">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="3461c-119">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3461c-119">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="3461c-120">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3461c-120">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="3461c-121">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="3461c-121">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="3461c-122">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-122">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3461c-123">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-123">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3461c-124">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-124">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3461c-125">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-125">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3461c-126">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-126">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3461c-127">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-127">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="3461c-128">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="3461c-128">Examine the log.</span></span> <span data-ttu-id="3461c-129">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="3461c-129">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="3461c-130">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3461c-130">Run the app in the Kudu console</span></span>

<span data-ttu-id="3461c-131">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3461c-131">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3461c-132">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="3461c-132">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="3461c-133">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-133">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3461c-134">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-134">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3461c-135">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-135">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3461c-136">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-136">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="3461c-137">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3461c-137">Test a 32-bit (x86) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="3461c-138">現在のリリース</span><span class="sxs-lookup"><span data-stu-id="3461c-138">Current release</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="3461c-139">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3461c-139">Run the app:</span></span>
   * <span data-ttu-id="3461c-140">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3461c-140">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="3461c-141">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3461c-141">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="3461c-142">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3461c-142">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="3461c-143">プレビュー リリース上で実行されているフレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="3461c-143">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="3461c-144">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3461c-144">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="3461c-145">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3461c-145">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3461c-146">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3461c-146">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3461c-147">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3461c-147">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="3461c-148">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3461c-148">Test a 64-bit (x64) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="3461c-149">現在のリリース</span><span class="sxs-lookup"><span data-stu-id="3461c-149">Current release</span></span>

* <span data-ttu-id="3461c-150">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3461c-150">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="3461c-151">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3461c-151">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="3461c-152">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3461c-152">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="3461c-153">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="3461c-153">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="3461c-154">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3461c-154">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="3461c-155">プレビュー リリース上で実行されているフレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="3461c-155">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="3461c-156">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3461c-156">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="3461c-157">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3461c-157">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3461c-158">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3461c-158">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3461c-159">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3461c-159">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="3461c-160">ASP.NET Core モジュールの stdout ログ</span><span class="sxs-lookup"><span data-stu-id="3461c-160">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="3461c-161">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="3461c-161">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="3461c-162">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3461c-162">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3461c-163">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3461c-163">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3461c-164">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-164">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="3461c-165">**[Suggested Solutions]\(推奨される解決方法\)**  >  **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、ボタンをクリックして **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-165">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="3461c-166">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-166">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3461c-167">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3461c-167">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3461c-168">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3461c-168">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3461c-169">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3461c-169">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3461c-170">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3461c-170">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3461c-171">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3461c-171">Make a request to the app.</span></span>
1. <span data-ttu-id="3461c-172">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="3461c-172">Return to the Azure portal.</span></span> <span data-ttu-id="3461c-173">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-173">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3461c-174">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-174">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3461c-175">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-175">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3461c-176">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-176">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3461c-177">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-177">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3461c-178">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="3461c-178">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="3461c-179">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-179">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="3461c-180">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3461c-180">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3461c-181">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3461c-181">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="3461c-182">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-182">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="3461c-183">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3461c-183">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3461c-184">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3461c-184">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="3461c-185">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3461c-185">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3461c-186">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3461c-186">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="3461c-187">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3461c-187">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3461c-188">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3461c-188">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3461c-189">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3461c-189">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="3461c-190">ASP.NET Core モジュール デバッグ ログ</span><span class="sxs-lookup"><span data-stu-id="3461c-190">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="3461c-191">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-191">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="3461c-192">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3461c-192">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3461c-193">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3461c-193">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="3461c-194">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="3461c-194">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="3461c-195">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="3461c-195">Redeploy the app.</span></span>
   * <span data-ttu-id="3461c-196">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="3461c-196">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="3461c-197">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-197">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3461c-198">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-198">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3461c-199">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-199">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="3461c-200">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-200">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="3461c-201">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-201">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3461c-202">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3461c-202">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="3461c-203">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="3461c-203">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="3461c-204">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3461c-204">Select the **Save** button.</span></span>
1. <span data-ttu-id="3461c-205">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-205">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3461c-206">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-206">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3461c-207">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-207">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3461c-208">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-208">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3461c-209">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-209">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3461c-210">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-210">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="3461c-211">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="3461c-211">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="3461c-212">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-212">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="3461c-213">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3461c-213">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3461c-214">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3461c-214">To disable the enhanced debug log, perform either of the following:</span></span>
   * <span data-ttu-id="3461c-215">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="3461c-215">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="3461c-216">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="3461c-216">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="3461c-217">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3461c-217">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="3461c-218">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3461c-218">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="3461c-219">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3461c-219">There's no limit on log file size.</span></span> <span data-ttu-id="3461c-220">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3461c-220">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3461c-221">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3461c-221">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3461c-222">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3461c-222">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="3461c-223">起動時の一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="3461c-223">Common startup errors</span></span>

<span data-ttu-id="3461c-224">以下を参照してください。<xref:host-and-deploy/azure-iis-errors-reference></span><span class="sxs-lookup"><span data-stu-id="3461c-224">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="3461c-225">このリファレンス トピックでは、アプリの起動を妨げる一般的な問題のほとんどが説明されています。</span><span class="sxs-lookup"><span data-stu-id="3461c-225">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="3461c-226">アプリの速度低下またはハング</span><span class="sxs-lookup"><span data-stu-id="3461c-226">Slow or hanging app</span></span>

<span data-ttu-id="3461c-227">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3461c-227">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="3461c-228">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3461c-228">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3461c-229">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="3461c-229">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a><span data-ttu-id="3461c-230">リモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3461c-230">Remote debugging</span></span>

<span data-ttu-id="3461c-231">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3461c-231">See the following topics:</span></span>

* <span data-ttu-id="3461c-232">[「Visual Studio を使用した Azure App Service のトラブルシューティング」の「Web アプリのリモート デバッグ」セクション](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="3461c-232">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="3461c-233">[Visual Studio 2017 を使用して Azure 内の IIS 上の ASP.NET Core をリモート デバッグする](/visualstudio/debugger/remote-debugging-azure) (Visual Studio ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="3461c-233">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="3461c-234">Application Insights</span><span class="sxs-lookup"><span data-stu-id="3461c-234">Application Insights</span></span>

<span data-ttu-id="3461c-235">[Application Insights](https://azure.microsoft.com/services/application-insights/) は、Azure App Service でホストされているアプリからのテレメトリを提供し、エラー ログやレポート機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="3461c-235">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="3461c-236">Application Insights は、アプリのログ機能が使用可能になってアプリが起動した後で発生したエラーのみをレポートできます。</span><span class="sxs-lookup"><span data-stu-id="3461c-236">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="3461c-237">詳しくは、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3461c-237">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="3461c-238">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="3461c-238">Monitoring blades</span></span>

<span data-ttu-id="3461c-239">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-239">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="3461c-240">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="3461c-240">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="3461c-241">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3461c-241">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="3461c-242">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="3461c-242">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="3461c-243">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-243">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="3461c-244">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-244">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="3461c-245">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-245">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="3461c-246">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-246">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="3461c-247">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="3461c-247">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="3461c-248">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-248">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="3461c-249">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-249">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="3461c-250">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="3461c-250">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="3461c-251">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-251">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3461c-252">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-252">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3461c-253">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3461c-253">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3461c-254">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-254">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3461c-255">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3461c-255">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3461c-256">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3461c-256">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3461c-257">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3461c-257">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3461c-258">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3461c-258">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="3461c-259">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="3461c-259">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="3461c-260">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-260">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="3461c-261">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-261">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="3461c-262">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-262">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="3461c-263">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-263">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="3461c-264">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-264">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="3461c-265">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3461c-265">Make a request to the app.</span></span>
1. <span data-ttu-id="3461c-266">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="3461c-266">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="3461c-267">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="3461c-267">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="3461c-268">「[ASP.NET Core モジュールの stdout ログ](#aspnet-core-module-stdout-log)」セクションの説明をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3461c-268">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="3461c-269">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="3461c-269">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="3461c-270">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3461c-270">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3461c-271">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3461c-271">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="3461c-272">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3461c-272">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="3461c-273">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3461c-273">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="3461c-274">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3461c-274">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3461c-275">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3461c-275">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3461c-276">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3461c-276">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3461c-277">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3461c-277">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3461c-278">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3461c-278">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="3461c-279">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3461c-279">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="3461c-280">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3461c-280">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="3461c-281">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3461c-281">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3461c-282">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="3461c-282">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="3461c-283">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="3461c-283">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="3461c-284">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="3461c-284">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
