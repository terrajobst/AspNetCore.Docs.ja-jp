---
title: IIS での ASP.NET Core のトラブルシューティング
author: guardrex
description: ASP.NET Core アプリのインターネット インフォメーション サービス (IIS) の展開に関する問題を診断する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313653"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="6b3e1-103">IIS での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6b3e1-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="6b3e1-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6b3e1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b3e1-105">この記事では、[インターネット インフォメーション サービス (IIS)](/iis) を使用してホストしている場合に、ASP.NET Core アプリの起動時に関する問題を診断する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="6b3e1-106">この記事の情報は、Windows Server および Windows デスクトップ上の IIS でのホスティングに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

<span data-ttu-id="6b3e1-107">その他のトラブルシューティング トピック:</span><span class="sxs-lookup"><span data-stu-id="6b3e1-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="6b3e1-108">Azure App Service では、アプリをホストするために [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)と IIS も使用されています。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-108">Azure App Service also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps.</span></span> <span data-ttu-id="6b3e1-109">Azure App Service に特に関連するトラブルシューティング上のアドバイスについては、<xref:host-and-deploy/azure-apps/troubleshoot> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-109">For troubleshooting advice that pertains specifically to Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
* <span data-ttu-id="6b3e1-110"><xref:fundamentals/error-handling> では、ローカル システム上で開発しているときに発生する ASP.NET Core アプリのエラーを処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="6b3e1-111">[Visual Studio を使用したデバッグ方法の学習](/visualstudio/debugger/getting-started-with-the-debugger)に関するページでは、Visual Studio デバッガーの機能を紹介しています。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="6b3e1-112">[Visual Studio Code でのデバッグ](https://code.visualstudio.com/docs/editor/debugging)に関するページでは、Visual Studio Code に組み込まれているデバッグのサポートについて説明します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="6b3e1-113">アプリの起動エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6b3e1-113">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="6b3e1-114">ASP.NET Core モジュールのデバッグ ログを有効にする</span><span class="sxs-lookup"><span data-stu-id="6b3e1-114">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="6b3e1-115">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-115">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="6b3e1-116">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-116">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="6b3e1-117">アプリケーション イベント ログ</span><span class="sxs-lookup"><span data-stu-id="6b3e1-117">Application Event Log</span></span>

<span data-ttu-id="6b3e1-118">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-118">Access the Application Event Log:</span></span>

1. <span data-ttu-id="6b3e1-119">[スタート] メニューを開き、**イベント ビューアー**を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-119">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="6b3e1-120">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-120">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="6b3e1-121">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-121">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="6b3e1-122">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-122">Search for errors associated with the failing app.</span></span> <span data-ttu-id="6b3e1-123">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-123">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="6b3e1-124">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="6b3e1-124">Run the app at a command prompt</span></span>

<span data-ttu-id="6b3e1-125">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-125">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="6b3e1-126">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-126">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="6b3e1-127">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="6b3e1-127">Framework-dependent deployment</span></span>

<span data-ttu-id="6b3e1-128">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="6b3e1-128">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="6b3e1-129">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-129">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="6b3e1-130">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-130">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="6b3e1-131">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-131">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="6b3e1-132">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-132">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="6b3e1-133">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-133">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="6b3e1-134">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-134">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="6b3e1-135">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="6b3e1-135">Self-contained deployment</span></span>

<span data-ttu-id="6b3e1-136">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="6b3e1-136">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="6b3e1-137">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-137">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="6b3e1-138">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-138">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="6b3e1-139">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-139">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="6b3e1-140">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-140">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="6b3e1-141">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-141">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="6b3e1-142">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-142">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="6b3e1-143">ASP.NET Core モジュールの stdout ログ</span><span class="sxs-lookup"><span data-stu-id="6b3e1-143">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="6b3e1-144">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="6b3e1-144">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="6b3e1-145">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-145">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="6b3e1-146">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-146">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="6b3e1-147">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-147">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="6b3e1-148">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-148">Edit the *web.config* file.</span></span> <span data-ttu-id="6b3e1-149">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-149">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="6b3e1-150">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-150">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="6b3e1-151">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-151">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="6b3e1-152">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-152">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="6b3e1-153">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-153">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="6b3e1-154">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-154">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="6b3e1-155">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-155">Make a request to the app.</span></span>
1. <span data-ttu-id="6b3e1-156">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-156">Navigate to the *logs* folder.</span></span> <span data-ttu-id="6b3e1-157">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-157">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="6b3e1-158">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-158">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6b3e1-159">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-159">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="6b3e1-160">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-160">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="6b3e1-161">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-161">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="6b3e1-162">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-162">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="6b3e1-163">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-163">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="6b3e1-164">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-164">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="6b3e1-165">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-165">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6b3e1-166">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-166">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="6b3e1-167">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="6b3e1-167">Enable the Developer Exception Page</span></span>

<span data-ttu-id="6b3e1-168">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-168">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="6b3e1-169">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-169">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="6b3e1-170">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-170">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="6b3e1-171">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-171">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="6b3e1-172">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-172">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="6b3e1-173">起動時の一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="6b3e1-173">Common startup errors</span></span>

<span data-ttu-id="6b3e1-174">以下を参照してください。<xref:host-and-deploy/azure-iis-errors-reference></span><span class="sxs-lookup"><span data-stu-id="6b3e1-174">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="6b3e1-175">このリファレンス トピックでは、アプリの起動を妨げる一般的な問題のほとんどが説明されています。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-175">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="6b3e1-176">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="6b3e1-176">Obtain data from an app</span></span>

<span data-ttu-id="6b3e1-177">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-177">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="6b3e1-178">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-178">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="6b3e1-179">ダンプを作成する</span><span class="sxs-lookup"><span data-stu-id="6b3e1-179">Create a dump</span></span>

<span data-ttu-id="6b3e1-180">"*ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-180">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="6b3e1-181">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="6b3e1-181">App crashes or encounters an exception</span></span>

<span data-ttu-id="6b3e1-182">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-182">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="6b3e1-183">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-183">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="6b3e1-184">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-184">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="6b3e1-185">[EnableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-185">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="6b3e1-186">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-186">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="6b3e1-187">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-187">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="6b3e1-188">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-188">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="6b3e1-189">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-189">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="6b3e1-190">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-190">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="6b3e1-191">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-191">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="6b3e1-192">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-192">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="6b3e1-193">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-193">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="6b3e1-194">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-194">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="6b3e1-195">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="6b3e1-195">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="6b3e1-196">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-196">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="6b3e1-197">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="6b3e1-197">Analyze the dump</span></span>

<span data-ttu-id="6b3e1-198">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-198">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="6b3e1-199">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-199">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="6b3e1-200">リモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="6b3e1-200">Remote debugging</span></span>

<span data-ttu-id="6b3e1-201">Visual Studio ドキュメントの「[Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)」(Visual Studio 2017 でリモート IIS コンピューター上の ASP.NET Core をリモート デバッグする) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-201">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="6b3e1-202">Application Insights</span><span class="sxs-lookup"><span data-stu-id="6b3e1-202">Application Insights</span></span>

<span data-ttu-id="6b3e1-203">[Application Insights](/azure/application-insights/) は、IIS でホストされているアプリからのテレメトリを提供し、エラー ログやレポート機能を備えています。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-203">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="6b3e1-204">Application Insights は、アプリのログ機能が使用可能になってアプリが起動した後で発生したエラーのみをレポートできます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-204">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="6b3e1-205">詳細については、「[Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-205">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="6b3e1-206">その他の注意事項</span><span class="sxs-lookup"><span data-stu-id="6b3e1-206">Additional advice</span></span>

<span data-ttu-id="6b3e1-207">開発コンピューター上の .NET Core SDK またはアプリ内のパッケージ バージョンのいずれかをアップグレードした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-207">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="6b3e1-208">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-208">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="6b3e1-209">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-209">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="6b3e1-210">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-210">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="6b3e1-211">*%UserProfile%\\.nuget\\packages* と *%LocalAppData%\\Nuget\\v3-cache* のパッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-211">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="6b3e1-212">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-212">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="6b3e1-213">アプリを再展開する前に、サーバー上の以前の展開が完全に削除されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-213">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="6b3e1-214">パッケージ キャッシュをクリアするには、コマンド プロンプトから `dotnet nuget locals all --clear` を実行する方法が便利です。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-214">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="6b3e1-215">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-215">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="6b3e1-216">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b3e1-216">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b3e1-217">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6b3e1-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
