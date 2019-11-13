---
title: ASP.NET Core SignalR でのログ記録と診断
author: anurse
description: ASP.NET Core SignalR アプリから診断情報を収集する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: c5bd2ac27f8ca486b0d75aed8439747f72448625
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963855"
---
# <a name="logging-and-diagnostics-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="f9d39-103">ASP.NET Core SignalR でのログ記録と診断</span><span class="sxs-lookup"><span data-stu-id="f9d39-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="f9d39-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="f9d39-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="f9d39-105">この記事では、ASP.NET Core SignalR アプリから診断情報を収集して問題のトラブルシューティングを行うためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="f9d39-106">サーバー側のログ記録</span><span class="sxs-lookup"><span data-stu-id="f9d39-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="f9d39-107">サーバー側のログには、アプリからの機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="f9d39-108">運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="f9d39-109">SignalR は ASP.NET Core の一部であるため、ASP.NET Core ログシステムを使用します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="f9d39-110">既定の構成では、SignalR はごくわずかな情報をログに記録しますが、これは構成できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="f9d39-111">ASP.NET Core ログの構成の詳細については、 [ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="f9d39-112"> では、次の2つの logger カテゴリを使用します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-112"> uses two logger categories:</span></span>

* <span data-ttu-id="f9d39-113">ハブプロトコル、ハブのアクティブ化、メソッドの呼び出し、およびその他のハブ関連アクティビティに関連するログの &ndash; を `Microsoft.AspNetCore.SignalR` します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="f9d39-114">Websocket、長いポーリング、サーバー送信イベント、低レベル SignalR インフラストラクチャなどのトランスポートに関連するログの &ndash; を `Microsoft.AspNetCore.Http.Connections` します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="f9d39-115">SignalRから詳細なログを有効にするには、`Logging`の `LogLevel` サブセクションに次の項目を追加して、前のプレフィックスの両方を*appsettings*ファイルの `Debug` レベルに構成します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="f9d39-116">また、`CreateWebHostBuilder` メソッドのコードでこれを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="f9d39-117">JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="f9d39-118">構成システムのドキュメントを参照して、入れ子になった構成値を指定する方法を確認してください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="f9d39-119">たとえば、環境変数を使用する場合、`:` の代わりに2つの `_` 文字 (たとえば、`Logging__LogLevel__Microsoft.AspNetCore.SignalR`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="f9d39-120">アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="f9d39-121">`Trace` レベルでは非常に低レベルの診断が生成されるため、アプリの問題を診断するためにはほとんど必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f9d39-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="f9d39-122">サーバー側のログへのアクセス</span><span class="sxs-lookup"><span data-stu-id="f9d39-122">Access server-side logs</span></span>

<span data-ttu-id="f9d39-123">サーバー側のログにアクセスする方法は、を実行している環境によって異なります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="f9d39-124">IIS の外部のコンソールアプリとして</span><span class="sxs-lookup"><span data-stu-id="f9d39-124">As a console app outside IIS</span></span>

<span data-ttu-id="f9d39-125">コンソールアプリでを実行している場合は、既定で[コンソール logger](xref:fundamentals/logging/index#console-provider)が有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> SignalR<span data-ttu-id="f9d39-126"> ログはコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-126"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="f9d39-127">Visual Studio から IIS Express 内</span><span class="sxs-lookup"><span data-stu-id="f9d39-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="f9d39-128">Visual Studio では、 **[出力]** ウィンドウにログ出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="f9d39-129">**[ASP.NET Core Web サーバー]** ドロップダウンオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="f9d39-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f9d39-130">Azure App Service</span></span>

<span data-ttu-id="f9d39-131">Azure App Service ポータルの **[診断ログ]** セクションで **[アプリケーションログ (ファイルシステム)]** オプションを有効にし、`Verbose`に**レベル**を構成します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="f9d39-132">ログは、**ログストリーミング**サービスおよび App Service のファイルシステムのログで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="f9d39-133">詳細については、「 [Azure ログストリーミング](xref:fundamentals/logging/index#azure-log-streaming)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="f9d39-134">その他の環境</span><span class="sxs-lookup"><span data-stu-id="f9d39-134">Other environments</span></span>

<span data-ttu-id="f9d39-135">アプリが別の環境 (Docker、Kubernetes、または Windows サービスなど) にデプロイされている場合は、「<xref:fundamentals/logging/index>」を参照して、環境に適したログプロバイダーを構成する方法についての詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="f9d39-136">JavaScript クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="f9d39-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="f9d39-137">クライアント側のログには、アプリからの機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="f9d39-138">運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="f9d39-139">JavaScript クライアントを使用する場合、`HubConnectionBuilder`の `configureLogging` メソッドを使用してログオプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="f9d39-140">完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="f9d39-141">次の表は、JavaScript クライアントで使用できるログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="f9d39-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="f9d39-142">ログレベルをこれらの値のいずれかに設定すると、そのレベルおよびテーブル内のすべてのレベルでログ記録が有効になります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="f9d39-143">レベル</span><span class="sxs-lookup"><span data-stu-id="f9d39-143">Level</span></span> | <span data-ttu-id="f9d39-144">説明</span><span class="sxs-lookup"><span data-stu-id="f9d39-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="f9d39-145">メッセージはログに記録されません。</span><span class="sxs-lookup"><span data-stu-id="f9d39-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="f9d39-146">アプリ全体でエラーが発生したことを示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="f9d39-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="f9d39-147">現在の操作でエラーが発生したことを示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="f9d39-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="f9d39-148">致命的ではない問題を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="f9d39-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="f9d39-149">情報メッセージ。</span><span class="sxs-lookup"><span data-stu-id="f9d39-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="f9d39-150">デバッグに役立つ診断メッセージ。</span><span class="sxs-lookup"><span data-stu-id="f9d39-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="f9d39-151">特定の問題を診断するために設計された、非常に詳細な診断メッセージ。</span><span class="sxs-lookup"><span data-stu-id="f9d39-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="f9d39-152">詳細設定を構成すると、ログはブラウザーコンソール (または NodeJS アプリの標準出力) に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="f9d39-153">カスタムログシステムにログを送信する場合は、`ILogger` インターフェイスを実装する JavaScript オブジェクトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="f9d39-154">実装する必要のあるメソッドは、イベントのレベルとイベントに関連付けられているメッセージを取得する `log`です。</span><span class="sxs-lookup"><span data-stu-id="f9d39-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="f9d39-155">(例:</span><span class="sxs-lookup"><span data-stu-id="f9d39-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="f9d39-156">.NET クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="f9d39-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="f9d39-157">クライアント側のログには、アプリからの機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="f9d39-158">運用アプリから GitHub などのパブリックフォーラムに未加工のログを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="f9d39-159">.NET クライアントからログを取得するには、`HubConnectionBuilder`で `ConfigureLogging` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="f9d39-160">これは `WebHostBuilder` と `HostBuilder`の `ConfigureLogging` メソッドと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="f9d39-161">ASP.NET Core で使用するのと同じログプロバイダーを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="f9d39-162">ただし、個々のログプロバイダーに対して NuGet パッケージを手動でインストールして有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="f9d39-163">コンソールのログ記録</span><span class="sxs-lookup"><span data-stu-id="f9d39-163">Console logging</span></span>

<span data-ttu-id="f9d39-164">コンソールのログ記録を有効にするには[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)追加します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="f9d39-165">次に、`AddConsole` メソッドを使用して、コンソールロガーを構成します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="f9d39-166">デバッグ出力ウィンドウのログ記録</span><span class="sxs-lookup"><span data-stu-id="f9d39-166">Debug output window logging</span></span>

<span data-ttu-id="f9d39-167">また、ログを構成して、Visual Studio の **[出力]** ウィンドウにアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="f9d39-168">`AddDebug` メソッドを使用して、次のようにして[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)インストールします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="f9d39-169">その他のログプロバイダー</span><span class="sxs-lookup"><span data-stu-id="f9d39-169">Other logging providers</span></span>

SignalR<span data-ttu-id="f9d39-170"> では、Serilog、Seq、NLog などの他のログ記録プロバイダーや、`Microsoft.Extensions.Logging`と統合されるその他のログ記録システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f9d39-170"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="f9d39-171">ログ記録システムに `ILoggerProvider`が提供されている場合は、`AddProvider`に登録できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="f9d39-172">コントロールの詳細度</span><span class="sxs-lookup"><span data-stu-id="f9d39-172">Control verbosity</span></span>

<span data-ttu-id="f9d39-173">アプリの他の場所からログを記録している場合は、既定のレベルを `Debug` に変更すると、詳細が表示されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="f9d39-174">フィルターを使用して、SignalR ログのログ記録レベルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="f9d39-175">これは、サーバーの場合とほぼ同じ方法でコードで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="f9d39-176">ネットワークトレース</span><span class="sxs-lookup"><span data-stu-id="f9d39-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="f9d39-177">ネットワークトレースには、アプリによって送信されたすべてのメッセージの完全な内容が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f9d39-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="f9d39-178">実稼働アプリから GitHub などのパブリックフォーラムに生のネットワークトレースを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="f9d39-179">問題が発生した場合は、ネットワークトレースを使用すると、役に立つ情報が得られることがあります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="f9d39-180">これは、問題の追跡ツールで問題をファイルする場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="f9d39-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="f9d39-181">Fiddler を使用してネットワークトレースを収集する (推奨オプション)</span><span class="sxs-lookup"><span data-stu-id="f9d39-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="f9d39-182">この方法は、すべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-182">This method works for all apps.</span></span>

<span data-ttu-id="f9d39-183">Fiddler は、HTTP トレースを収集するための非常に強力なツールです。</span><span class="sxs-lookup"><span data-stu-id="f9d39-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="f9d39-184">[Telerik.com/fiddler](https://www.telerik.com/fiddler)からインストールして起動し、アプリを実行して問題を再現します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="f9d39-185">Fiddler は Windows で使用でき、macOS と Linux のベータ版があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="f9d39-186">HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックの暗号化を解除できるようにするための追加の手順がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="f9d39-187">詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="f9d39-188">トレースを収集したら、[ > **ファイル**] を選択し、メニューバーから [**すべてのセッション** > **保存**] を選択して、トレースをエクスポートできます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler からすべてのセッションをエクスポートしています](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="f9d39-190">Tcpdump を使用してネットワークトレースを収集する (macOS および Linux のみ)</span><span class="sxs-lookup"><span data-stu-id="f9d39-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="f9d39-191">この方法は、すべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-191">This method works for all apps.</span></span>

<span data-ttu-id="f9d39-192">コマンドシェルから次のコマンドを実行して、tcpdump を使用して未加工の TCP トレースを収集できます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="f9d39-193">アクセス許可のエラーが表示された場合は、コマンドの `root` またはプレフィックスを `sudo` にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="f9d39-194">`[interface]` をキャプチャするネットワークインターフェイスに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="f9d39-195">通常は、`/dev/eth0` (標準イーサネットインターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。</span><span class="sxs-lookup"><span data-stu-id="f9d39-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="f9d39-196">詳細については、ホストシステムの `tcpdump` man ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="f9d39-197">ブラウザーでネットワークトレースを収集する</span><span class="sxs-lookup"><span data-stu-id="f9d39-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="f9d39-198">この方法は、ブラウザーベースのアプリに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="f9d39-199">ほとんどのブラウザーには、ブラウザーとサーバー間のネットワークアクティビティをキャプチャできる [ネットワーク] タブがあり開発者ツール。</span><span class="sxs-lookup"><span data-stu-id="f9d39-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="f9d39-200">ただし、これらのトレースには、WebSocket およびサーバー送信のイベントメッセージは含まれません。</span><span class="sxs-lookup"><span data-stu-id="f9d39-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="f9d39-201">これらのトランスポートを使用している場合は、Fiddler や TcpDump などのツールを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="f9d39-202">Microsoft Edge および Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="f9d39-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="f9d39-203">(この手順は、Edge と Internet Explorer の両方で同じです)。</span><span class="sxs-lookup"><span data-stu-id="f9d39-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="f9d39-204">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="f9d39-205">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-205">Click the Network Tab</span></span>
3. <span data-ttu-id="f9d39-206">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="f9d39-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="f9d39-207">ツールバーの [保存] アイコンをクリックして、トレースを "HAR" ファイルとしてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge Dev Tools の [ネットワーク] タブの [保存] アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="f9d39-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="f9d39-209">Google Chrome</span></span>

1. <span data-ttu-id="f9d39-210">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="f9d39-211">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-211">Click the Network Tab</span></span>
3. <span data-ttu-id="f9d39-212">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="f9d39-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="f9d39-213">要求の一覧の任意の場所を右クリックし、[コンテンツと共に HAR として保存] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome Dev ツールの [ネットワーク] タブの [HAR として保存] オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="f9d39-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="f9d39-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="f9d39-216">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="f9d39-217">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-217">Click the Network Tab</span></span>
3. <span data-ttu-id="f9d39-218">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="f9d39-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="f9d39-219">要求の一覧の任意の場所を右クリックし、[すべてを HAR として保存] を選択します。</span><span class="sxs-lookup"><span data-stu-id="f9d39-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox Dev Tools [ネットワーク] タブの [すべてを HAR として保存] オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="f9d39-221">診断ファイルを GitHub の問題にアタッチする</span><span class="sxs-lookup"><span data-stu-id="f9d39-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="f9d39-222">GitHub の問題に診断ファイルを添付するには、名前を変更して `.txt` 拡張機能を設定し、問題にドラッグアンドドロップします。</span><span class="sxs-lookup"><span data-stu-id="f9d39-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="f9d39-223">ログファイルやネットワークトレースの内容を GitHub の問題に貼り付けることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="f9d39-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="f9d39-224">これらのログとトレースは非常に大きくなる可能性があり、GitHub は通常、これらを切り捨てます。</span><span class="sxs-lookup"><span data-stu-id="f9d39-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![GitHub の問題にログファイルをドラッグする](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="f9d39-226">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f9d39-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
