---
title: ASP.NET Core SignalR でのログ記録と診断
author: anurse
description: ASP.NET Core SignalR アプリケーションから診断を収集する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/19/2019
uid: signalr/diagnostics
ms.openlocfilehash: 69dbd057b3dcadeb3ca5d94ede1234530fb447db
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313710"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="b3eef-103">ASP.NET Core SignalR でのログ記録と診断</span><span class="sxs-lookup"><span data-stu-id="b3eef-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="b3eef-104">によって[Andrew Stanton-nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b3eef-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b3eef-105">この記事では、問題のトラブルシューティングに役立つ、ASP.NET Core SignalR アプリケーションから診断を収集するためのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="b3eef-106">サーバー側のログ記録</span><span class="sxs-lookup"><span data-stu-id="b3eef-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="b3eef-107">サーバー側のログは、アプリからの機密情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b3eef-108">実稼働アプリの未加工のログを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。</span><span class="sxs-lookup"><span data-stu-id="b3eef-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b3eef-109">SignalR は ASP.NET Core の一部であるため、ASP.NET Core のログ記録システムを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="b3eef-110">既定の構成、構成された SignalR のログはほとんどの情報が、このことができます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="b3eef-111">上のドキュメントを参照して[ASP.NET Core のログ記録](xref:fundamentals/logging/index#configuration)の ASP.NET Core のログ記録の構成の詳細について。</span><span class="sxs-lookup"><span data-stu-id="b3eef-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="b3eef-112">SignalR では、2 つのカテゴリのロガーを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="b3eef-113">`Microsoft.AspNetCore.SignalR` &ndash; ハブのプロトコルに関連するログには、ハブ、メソッド、およびその他のハブに関連するアクティビティの呼び出しをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="b3eef-114">`Microsoft.AspNetCore.Http.Connections` &ndash; ログ用には、Websocket、長いポーリング Server-Sent イベントおよび低レベルの SignalR インフラストラクチャなどのトランスポートに関連します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="b3eef-115">SignalR から詳細なログを有効にするには、前のプレフィックスの両方を構成、`Debug`レベル、 *appsettings.json*ファイルに次の項目を追加することで、`LogLevel`サブセクション内で`Logging`:</span><span class="sxs-lookup"><span data-stu-id="b3eef-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="b3eef-116">コード内で構成することもできます、`CreateWebHostBuilder`メソッド。</span><span class="sxs-lookup"><span data-stu-id="b3eef-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="b3eef-117">JSON ベースの構成を使用していない場合は、構成システムで、次の構成値を設定します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="b3eef-118">入れ子になった構成値を指定する方法を決定する、構成システムのマニュアルを確認してください。</span><span class="sxs-lookup"><span data-stu-id="b3eef-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="b3eef-119">例では、環境変数を使用する場合、2 つ`_`の代わりに文字が使用されて、 `:` (たとえば、 `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。</span><span class="sxs-lookup"><span data-stu-id="b3eef-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="b3eef-120">使用をお勧め、`Debug`レベルのアプリの診断の詳細を収集するときにします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="b3eef-121">`Trace`レベルが非常に低レベルの診断を生成し、アプリで問題を診断するために必要なことはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="b3eef-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="b3eef-122">サーバー側ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-122">Access server-side logs</span></span>

<span data-ttu-id="b3eef-123">サーバー側のログにアクセスする方法を実行している環境によって異なります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="b3eef-124">IIS の外部のコンソール アプリとして</span><span class="sxs-lookup"><span data-stu-id="b3eef-124">As a console app outside IIS</span></span>

<span data-ttu-id="b3eef-125">コンソール アプリケーションを実行する場合、[コンソール ロガー](xref:fundamentals/logging/index#console-provider)既定で有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="b3eef-126">SignalR のログは、コンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="b3eef-127">Visual Studio から IIS Express 内</span><span class="sxs-lookup"><span data-stu-id="b3eef-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="b3eef-128">Visual Studio でのログ出力が表示されます、**出力**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="b3eef-129">選択、 **ASP.NET Core Web サーバー**オプションをドロップダウンします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="b3eef-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b3eef-130">Azure App Service</span></span>

<span data-ttu-id="b3eef-131">有効にする、**アプリケーション ログ (ファイルシステム)** オプション、**診断ログ**Azure App Service ポータルのセクションを構成して、**レベル**に`Verbose`します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="b3eef-132">ログが表示されます、**ログのストリーミング**サービスと、App Service のファイル システムのログに記録します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="b3eef-133">詳細については、次を参照してください。 [Azure ログのストリーミング](xref:fundamentals/logging/index#azure-log-streaming)します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="b3eef-134">その他の環境</span><span class="sxs-lookup"><span data-stu-id="b3eef-134">Other environments</span></span>

<span data-ttu-id="b3eef-135">(たとえば、Docker、Kubernetes、または Windows サービス) の別の環境にアプリを展開する場合は、次を参照してください。 <xref:fundamentals/logging/index> 、環境に適したログ プロバイダーを構成する方法の詳細。</span><span class="sxs-lookup"><span data-stu-id="b3eef-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="b3eef-136">JavaScript クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="b3eef-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b3eef-137">クライアント側のログは、アプリからの機密情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b3eef-138">実稼働アプリの未加工のログを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。</span><span class="sxs-lookup"><span data-stu-id="b3eef-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b3eef-139">使用してログ記録オプションを構成するには、JavaScript クライアントを使用する場合、`configureLogging`メソッド`HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b3eef-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="b3eef-140">完全ログ記録を無効にするには指定`signalR.LogLevel.None`で、`configureLogging`メソッド。</span><span class="sxs-lookup"><span data-stu-id="b3eef-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b3eef-141">次の表では、JavaScript クライアントに利用可能なログ レベルを示します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="b3eef-142">これらの値のいずれかに、ログ レベルを設定すると、そのレベルとテーブルの上にすべてのレベルでログ記録が有効になります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="b3eef-143">レベル</span><span class="sxs-lookup"><span data-stu-id="b3eef-143">Level</span></span> | <span data-ttu-id="b3eef-144">説明</span><span class="sxs-lookup"><span data-stu-id="b3eef-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="b3eef-145">メッセージは記録されません。</span><span class="sxs-lookup"><span data-stu-id="b3eef-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="b3eef-146">アプリ全体で障害を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="b3eef-147">現在の操作の失敗を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="b3eef-148">致命的でない問題を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="b3eef-149">情報メッセージ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="b3eef-150">診断メッセージのデバッグに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="b3eef-151">非常に詳細な診断メッセージが特定の問題を診断するために設計されています。</span><span class="sxs-lookup"><span data-stu-id="b3eef-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="b3eef-152">詳細度を構成したら、ブラウザーのコンソール (または NodeJS アプリの標準出力) にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="b3eef-153">実装する JavaScript オブジェクトを指定するには、カスタムのログ記録システムにログを送信する場合、`ILogger`インターフェイス。</span><span class="sxs-lookup"><span data-stu-id="b3eef-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="b3eef-154">実装する必要がある唯一の方法が`log`イベントのレベルを取得して、イベントに関連付けられているメッセージ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="b3eef-155">例:</span><span class="sxs-lookup"><span data-stu-id="b3eef-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="b3eef-156">.NET クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="b3eef-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b3eef-157">クライアント側のログは、アプリからの機密情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b3eef-158">実稼働アプリの未加工のログを GitHub のようなパブリック フォーラムに **決して** 投稿しないでください。</span><span class="sxs-lookup"><span data-stu-id="b3eef-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b3eef-159">.NET クライアントからログを取得、使用することができます、`ConfigureLogging`メソッド`HubConnectionBuilder`します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="b3eef-160">これは、機能と同様、`ConfigureLogging`メソッド`WebHostBuilder`と`HostBuilder`します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="b3eef-161">ASP.NET Core で使用する同じログ プロバイダーを構成できます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="b3eef-162">ただし、手動でインストールし、個々 のログ プロバイダーの NuGet パッケージを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="b3eef-163">コンソールのログ記録</span><span class="sxs-lookup"><span data-stu-id="b3eef-163">Console logging</span></span>

<span data-ttu-id="b3eef-164">コンソールのログ記録を有効にするには追加、 [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="b3eef-165">次に、使用、`AddConsole`コンソール ロガーを構成する方法。</span><span class="sxs-lookup"><span data-stu-id="b3eef-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="b3eef-166">デバッグ出力ウィンドウのログ記録</span><span class="sxs-lookup"><span data-stu-id="b3eef-166">Debug output window logging</span></span>

<span data-ttu-id="b3eef-167">移動するログを構成することも、**出力**Visual Studio のウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="b3eef-168">インストール、 [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)パッケージ化し、使用、`AddDebug`メソッド。</span><span class="sxs-lookup"><span data-stu-id="b3eef-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="b3eef-169">その他のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="b3eef-169">Other logging providers</span></span>

<span data-ttu-id="b3eef-170">SignalR など、Serilog、Seq、NLog、またはその他のログ記録システムと統合するには、その他のログ記録プロバイダーをサポートしている`Microsoft.Extensions.Logging`します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="b3eef-171">ログ記録システムを提供する場合、`ILoggerProvider`に登録することができます`AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="b3eef-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="b3eef-172">コントロールの詳細度</span><span class="sxs-lookup"><span data-stu-id="b3eef-172">Control verbosity</span></span>

<span data-ttu-id="b3eef-173">アプリで他の場所からログインする場合に既定のレベルを変更する`Debug`すぎる冗長になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="b3eef-174">SignalR のログのログ記録レベルを構成するのにフィルターを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="b3eef-175">これ行う多くのコードで、サーバー上と同じ方法。</span><span class="sxs-lookup"><span data-stu-id="b3eef-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="b3eef-176">ネットワーク トレース</span><span class="sxs-lookup"><span data-stu-id="b3eef-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="b3eef-177">ネットワーク トレースには、アプリから送信されたすべてのメッセージの完全な内容が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b3eef-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="b3eef-178">**決して**未加工のネットワーク トレースを実稼働アプリを GitHub のようなパブリック フォーラムに投稿します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b3eef-179">問題が発生した場合ネットワーク トレースが役に立つ情報の多くをする場合があります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="b3eef-180">これは、問題の追跡ツールで問題を報告しようとしている場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="b3eef-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="b3eef-181">Fiddler (推奨されるオプション) でネットワーク トレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="b3eef-182">この方法は、すべてのアプリに対して機能します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-182">This method works for all apps.</span></span>

<span data-ttu-id="b3eef-183">Fiddler は、HTTP トレースを収集するための非常に強力なツールです。</span><span class="sxs-lookup"><span data-stu-id="b3eef-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="b3eef-184">インストール[telerik.com/fiddler](https://www.telerik.com/fiddler)それを起動し、アプリを実行し、問題を再現します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="b3eef-185">Fiddler は、Windows と macOS および Linux 用のベータ バージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="b3eef-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="b3eef-186">HTTPS を使用してを接続する場合は、Fiddler は HTTPS トラフィックを復号化できることを確認する特別な手順です。</span><span class="sxs-lookup"><span data-stu-id="b3eef-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="b3eef-187">詳細については、次を参照してください。、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="b3eef-188">選択して、トレースをエクスポートするには、トレースを収集したら、**ファイル** > **保存** > **すべてのセッション**メニュー バーから。</span><span class="sxs-lookup"><span data-stu-id="b3eef-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler からすべてのセッションをエクスポートします。](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="b3eef-190">Tcpdump (macOS および Linux のみ) とネットワーク トレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="b3eef-191">この方法は、すべてのアプリに対して機能します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-191">This method works for all apps.</span></span>

<span data-ttu-id="b3eef-192">コマンド シェルから次のコマンドを実行して tcpdump を使用して、生の TCP トレースを収集することができます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="b3eef-193">必要がある`root`コマンドにプレフィックスとしてまたは`sudo`アクセス許可エラーが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="b3eef-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="b3eef-194">置換`[interface]`でキャプチャするネットワーク インターフェイスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="b3eef-195">これは通常、ようになります`/dev/eth0`(用、標準のイーサネット インターフェイス) または`/dev/lo0`(localhost トラフィック用)。</span><span class="sxs-lookup"><span data-stu-id="b3eef-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="b3eef-196">詳細については、次を参照してください。、 `tcpdump` 、ホスト システム上の man ページ。</span><span class="sxs-lookup"><span data-stu-id="b3eef-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="b3eef-197">ブラウザーでネットワーク トレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="b3eef-198">このメソッドは、ブラウザー ベースのアプリにのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="b3eef-199">ほとんどのブラウザー開発者ツールでは、ブラウザーとサーバー間のネットワーク アクティビティをキャプチャすることを許可する"Network"タブが存在します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="b3eef-200">ただし、これらのトレースには、WebSocket と Server-Sent イベント メッセージが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="b3eef-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="b3eef-201">これらのトランスポートを使用している場合より優れたアプローチでは Fiddler または TcpDump (後述) などのツールを使用してます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="b3eef-202">Microsoft Edge や Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="b3eef-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="b3eef-203">(手順は、Edge や Internet Explorer の両方で同じは)</span><span class="sxs-lookup"><span data-stu-id="b3eef-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="b3eef-204">開発ツールを開く F12 キーを押します</span><span class="sxs-lookup"><span data-stu-id="b3eef-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b3eef-205">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-205">Click the Network Tab</span></span>
3. <span data-ttu-id="b3eef-206">(必要な) 場合は、ページを更新し、問題の再現</span><span class="sxs-lookup"><span data-stu-id="b3eef-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b3eef-207">トレースを"HAR"ファイルとしてエクスポートするには、ツールバーの [保存] アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![保存アイコンを Microsoft Edge 開発者ツールの [ネットワーク] タブ](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="b3eef-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="b3eef-209">Google Chrome</span></span>

1. <span data-ttu-id="b3eef-210">開発ツールを開く F12 キーを押します</span><span class="sxs-lookup"><span data-stu-id="b3eef-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b3eef-211">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-211">Click the Network Tab</span></span>
3. <span data-ttu-id="b3eef-212">(必要な) 場合は、ページを更新し、問題の再現</span><span class="sxs-lookup"><span data-stu-id="b3eef-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b3eef-213">任意の場所の要求の一覧でを右クリックし、選択「の内容を HAR として保存」します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome デベロッパー ツール ネットワーク タブで、"コンテンツを含む HAR として保存 オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="b3eef-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="b3eef-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="b3eef-216">開発ツールを開く F12 キーを押します</span><span class="sxs-lookup"><span data-stu-id="b3eef-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b3eef-217">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-217">Click the Network Tab</span></span>
3. <span data-ttu-id="b3eef-218">(必要な) 場合は、ページを更新し、問題の再現</span><span class="sxs-lookup"><span data-stu-id="b3eef-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b3eef-219">任意の場所の要求の一覧でを右クリックし、"保存すべてとして HAR"を選択</span><span class="sxs-lookup"><span data-stu-id="b3eef-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox デベロッパー ツール ネットワーク タブで HAR としてすべて"保存オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="b3eef-221">GitHub の問題を診断ファイルを添付します。</span><span class="sxs-lookup"><span data-stu-id="b3eef-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="b3eef-222">GitHub の問題を診断ファイルをアタッチするにはため、名前を変更して、`.txt`拡張機能と、ドラッグ アンド ドロップし、問題にします。</span><span class="sxs-lookup"><span data-stu-id="b3eef-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="b3eef-223">くださいしないと、GitHub の問題にネットワーク トレースまたはログ ファイルの内容を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="b3eef-224">これらのログとトレースは非常に大きく、でき、通常は GitHub で切り捨てます。</span><span class="sxs-lookup"><span data-stu-id="b3eef-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![GitHub の問題にログ ファイルをドラッグします。](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="b3eef-226">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b3eef-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
