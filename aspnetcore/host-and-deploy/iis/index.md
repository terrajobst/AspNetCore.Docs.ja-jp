---
title: IIS を使用した Windows での ASP.NET Core のホスト
author: guardrex
description: Windows Server インターネット インフォメーション サービス (IIS) での ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: bf035bc65f0f120f52e55effe4d413bfecdf735d
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952085"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="263c3-103">IIS を使用した Windows での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="263c3-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="263c3-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="263c3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="263c3-105">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="263c3-105">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="263c3-106">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="263c3-106">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="263c3-107">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="263c3-107">Supported operating systems</span></span>

<span data-ttu-id="263c3-108">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="263c3-108">The following operating systems are supported:</span></span>

* <span data-ttu-id="263c3-109">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="263c3-109">Windows 7 or later</span></span>
* <span data-ttu-id="263c3-110">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="263c3-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="263c3-111">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="263c3-111">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="263c3-112">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-112">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="263c3-113">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-113">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="263c3-114">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-114">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="263c3-115">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="263c3-115">Supported platforms</span></span>

<span data-ttu-id="263c3-116">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="263c3-116">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="263c3-117">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="263c3-117">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="263c3-118">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="263c3-118">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="263c3-119">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="263c3-119">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="263c3-120">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="263c3-120">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="263c3-121">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-121">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="263c3-122">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-122">A 64-bit runtime must be present on the host system.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-models"></a><span data-ttu-id="263c3-123">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="263c3-123">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="263c3-124">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="263c3-124">In-process hosting model</span></span>

<span data-ttu-id="263c3-125">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-125">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="263c3-126">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="263c3-126">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="263c3-127">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-127">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="263c3-128">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="263c3-128">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="263c3-129">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-129">Performs app initialization.</span></span>
  * <span data-ttu-id="263c3-130">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="263c3-130">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="263c3-131">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="263c3-131">Calls `Program.Main`.</span></span>
* <span data-ttu-id="263c3-132">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="263c3-132">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="263c3-133">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="263c3-133">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="263c3-134">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="263c3-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

<span data-ttu-id="263c3-136">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="263c3-137">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="263c3-138">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="263c3-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="263c3-139">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="263c3-140">IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-140">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="263c3-141">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="263c3-141">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="263c3-142">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-142">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="263c3-143">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="263c3-143">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="263c3-144">インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。</span><span class="sxs-lookup"><span data-stu-id="263c3-144">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="263c3-145">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="263c3-145">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="263c3-146">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) サーバーへのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="263c3-146">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!NOTE]
> <span data-ttu-id="263c3-147">単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="263c3-147">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="263c3-148">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="263c3-148">Out-of-process hosting model</span></span>

<span data-ttu-id="263c3-149">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-149">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="263c3-150">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-150">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="263c3-151">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="263c3-151">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="263c3-152">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="263c3-152">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="263c3-154">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="263c3-154">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="263c3-155">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="263c3-155">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="263c3-156">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-156">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="263c3-157">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能によって `http://localhost:{PORT}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-157">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="263c3-158">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-158">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="263c3-159">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-159">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="263c3-160">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-160">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="263c3-161">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="263c3-161">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="263c3-162">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-162">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="263c3-163">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="263c3-163">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="263c3-164">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="263c3-164">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="263c3-165">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](xref:fundamentals/servers/index#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます ("*プロセス外*")。</span><span class="sxs-lookup"><span data-stu-id="263c3-165">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="263c3-166">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-166">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="263c3-167">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-167">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="263c3-168">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="263c3-168">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="263c3-169">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="263c3-169">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="263c3-171">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="263c3-171">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="263c3-172">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="263c3-172">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="263c3-173">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-173">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="263c3-174">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-174">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="263c3-175">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-175">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="263c3-176">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-176">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="263c3-177">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-177">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="263c3-178">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="263c3-178">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="263c3-179">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-179">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="263c3-180">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="263c3-180">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="263c3-181">`CreateDefaultBuilder` は [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成し、ベース パスとポートを [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)に構成することで、IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-181">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="263c3-182">ASP.NET Core モジュールでは、バックエンド プロセスに割り当てる動的なポートが生成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-182">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="263c3-183">`CreateDefaultBuilder` では <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-183">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="263c3-184">`UseIISIntegration` では、localhost の IP アドレス (`127.0.0.1`) で動的なポートをリッスンするように Kestrel が構成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-184">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="263c3-185">動的なポートが 1234 である場合、Kestrel は `127.0.0.1:1234` でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="263c3-185">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="263c3-186">この構成によって、以下から提供されるその他の URL 構成が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="263c3-186">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="263c3-187">Kestrel の Listen API</span><span class="sxs-lookup"><span data-stu-id="263c3-187">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="263c3-188">[構成](xref:fundamentals/configuration/index) (または[コマンド ラインの --urls オプション](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="263c3-188">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="263c3-189">モジュールを使用する場合、`UseUrls` または Kestrel の `Listen` API を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="263c3-189">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="263c3-190">`UseUrls` または `Listen` が呼び出されると、IIS なしでアプリを実行しているときのみ、Kestrel で指定したポートがリッスンされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-190">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

<span data-ttu-id="263c3-191">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-191">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="263c3-192">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-192">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="263c3-193">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="263c3-193">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="263c3-194">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="263c3-194">Enable the IISIntegration components</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="263c3-195">`CreateHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-195">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="263c3-196">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-196">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="263c3-197">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-197">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="263c3-198">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-198">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

::: moniker-end

### <a name="iis-options"></a><span data-ttu-id="263c3-199">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="263c3-199">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="263c3-200">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="263c3-200">**In-process hosting model**</span></span>

<span data-ttu-id="263c3-201">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="263c3-201">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="263c3-202">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="263c3-202">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="263c3-203">オプション</span><span class="sxs-lookup"><span data-stu-id="263c3-203">Option</span></span>                         | <span data-ttu-id="263c3-204">既定値</span><span class="sxs-lookup"><span data-stu-id="263c3-204">Default</span></span> | <span data-ttu-id="263c3-205">設定</span><span class="sxs-lookup"><span data-stu-id="263c3-205">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="263c3-206">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-206">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="263c3-207">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="263c3-207">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="263c3-208">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-208">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="263c3-209">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-209">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="263c3-210">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-210">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="263c3-211">`HttpContext.Request` および `HttpContext.Response` に対して同期 IO が許可されるか。</span><span class="sxs-lookup"><span data-stu-id="263c3-211">Whether synchronous IO is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="263c3-212">`HttpRequest` の最大要求本文サイズを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-212">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="263c3-213">IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-213">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="263c3-214">`MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="263c3-214">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="263c3-215">`maxAllowedContentLength`を引き上げるには、*web.config* 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-215">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="263c3-216">詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-216">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="263c3-217">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="263c3-217">**Out-of-process hosting model**</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="263c3-218">オプション</span><span class="sxs-lookup"><span data-stu-id="263c3-218">Option</span></span>                         | <span data-ttu-id="263c3-219">既定値</span><span class="sxs-lookup"><span data-stu-id="263c3-219">Default</span></span> | <span data-ttu-id="263c3-220">設定</span><span class="sxs-lookup"><span data-stu-id="263c3-220">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="263c3-221">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-221">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="263c3-222">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="263c3-222">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="263c3-223">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-223">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="263c3-224">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-224">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="263c3-225">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-225">Sets the display name shown to users on login pages.</span></span> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="263c3-226">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="263c3-226">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="263c3-227">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="263c3-227">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="263c3-228">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="263c3-228">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="263c3-229">オプション</span><span class="sxs-lookup"><span data-stu-id="263c3-229">Option</span></span>                         | <span data-ttu-id="263c3-230">既定値</span><span class="sxs-lookup"><span data-stu-id="263c3-230">Default</span></span> | <span data-ttu-id="263c3-231">設定</span><span class="sxs-lookup"><span data-stu-id="263c3-231">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="263c3-232">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-232">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="263c3-233">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="263c3-233">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="263c3-234">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-234">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="263c3-235">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-235">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="263c3-236">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-236">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="263c3-237">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-237">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="263c3-238">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="263c3-238">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="263c3-239">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-239">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="263c3-240">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-240">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="263c3-241">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-241">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="263c3-242">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="263c3-242">web.config file</span></span>

<span data-ttu-id="263c3-243">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-243">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="263c3-244">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-244">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="263c3-245">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="263c3-245">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="263c3-246">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="263c3-246">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="263c3-247">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-247">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="263c3-248">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-248">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="263c3-249">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="263c3-249">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="263c3-250">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-250">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="263c3-251">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-251">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="263c3-252">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-252">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="263c3-253">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-253">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="263c3-254">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-254">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="263c3-255">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="263c3-255">web.config file location</span></span>

<span data-ttu-id="263c3-256">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-256">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="263c3-257">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="263c3-257">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="263c3-258">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="263c3-258">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="263c3-259">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="263c3-259">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="263c3-260">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="263c3-260">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="263c3-261">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-261">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="263c3-262">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="263c3-262">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="263c3-263">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="263c3-263">Transform web.config</span></span>

<span data-ttu-id="263c3-264">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-264">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="263c3-265">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="263c3-265">IIS configuration</span></span>

<span data-ttu-id="263c3-266">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="263c3-266">**Windows Server operating systems**</span></span>

<span data-ttu-id="263c3-267">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-267">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="263c3-268">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-268">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="263c3-269">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="263c3-269">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="263c3-271">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="263c3-271">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="263c3-272">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-272">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="263c3-274">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="263c3-274">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="263c3-275">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="263c3-275">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="263c3-276">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-276">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="263c3-277">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-277">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="263c3-278">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="263c3-278">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="263c3-279">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="263c3-279">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="263c3-280">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="263c3-280">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="263c3-281">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-281">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="263c3-282">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-282">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="263c3-283">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="263c3-283">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="263c3-284">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="263c3-284">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="263c3-285">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="263c3-285">**Windows desktop operating systems**</span></span>

<span data-ttu-id="263c3-286">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-286">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="263c3-287">**[コントロール パネル]**  >  **[プログラム]**  >  **[プログラムと機能]**  >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-287">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="263c3-288">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-288">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="263c3-289">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-289">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="263c3-290">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="263c3-290">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="263c3-291">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="263c3-291">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="263c3-292">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="263c3-292">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="263c3-293">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="263c3-293">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="263c3-294">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="263c3-294">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="263c3-295">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-295">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="263c3-296">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-296">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="263c3-297">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="263c3-297">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="263c3-298">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="263c3-298">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="263c3-299">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="263c3-299">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="263c3-300">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-300">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="263c3-301">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-301">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="263c3-302">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-302">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="263c3-304">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="263c3-304">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="263c3-305">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="263c3-305">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="263c3-306">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-306">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="263c3-307">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="263c3-307">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="263c3-308">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-308">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="263c3-309">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-309">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="263c3-310">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="263c3-310">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="263c3-311">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-311">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="263c3-312">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="263c3-312">Direct download (current version)</span></span>

<span data-ttu-id="263c3-313">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="263c3-313">Download the installer using the following link:</span></span>

[<span data-ttu-id="263c3-314">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="263c3-314">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="263c3-315">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="263c3-315">Earlier versions of the installer</span></span>

<span data-ttu-id="263c3-316">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="263c3-316">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="263c3-317">[.NET のダウンロードのアーカイブ](https://www.microsoft.com/net/download/archives)に移動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-317">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="263c3-318">**[.NET Core]** の下で、.NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-318">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="263c3-319">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="263c3-319">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="263c3-320">**[Runtime & Hosting Bundle]\(ランタイム & ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="263c3-320">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="263c3-321">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="263c3-321">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="263c3-322">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="263c3-322">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="263c3-323">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="263c3-323">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="263c3-324">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-324">Run the installer on the server.</span></span> <span data-ttu-id="263c3-325">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="263c3-325">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="263c3-326">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="263c3-326">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="263c3-327">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="263c3-327">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="263c3-328">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-328">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="263c3-329">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="263c3-329">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="263c3-330">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-330">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="263c3-331">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="263c3-331">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="263c3-332">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-332">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="263c3-333">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="263c3-333">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="263c3-334">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-334">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="263c3-335">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="263c3-335">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="263c3-336">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-336">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="263c3-337">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-337">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="263c3-338">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-338">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="263c3-339">ASP.NET Core の共有フレームワーク パッケージの修正プログラムのリリースでは、ロールフォワード動作は採用されていません。</span><span class="sxs-lookup"><span data-stu-id="263c3-339">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="263c3-340">新しいホスティング バンドルをインストールして共有フレームワークをアップグレードした後、システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-340">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="263c3-341">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="263c3-341">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="263c3-342">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-342">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="263c3-343">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-343">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="263c3-344">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="263c3-344">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="263c3-345">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="263c3-345">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="263c3-346">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-346">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="263c3-347">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-347">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="263c3-348">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="263c3-348">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="263c3-349">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="263c3-349">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="263c3-350">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="263c3-350">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="263c3-351">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="263c3-351">The preferred method is to use WebPI.</span></span> <span data-ttu-id="263c3-352">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="263c3-352">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="263c3-353">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="263c3-353">Create the IIS site</span></span>

1. <span data-ttu-id="263c3-354">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="263c3-354">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="263c3-355">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-355">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="263c3-356">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="263c3-356">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="263c3-357">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-357">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="263c3-358">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="263c3-358">Right-click the **Sites** folder.</span></span> <span data-ttu-id="263c3-359">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-359">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="263c3-360">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-360">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="263c3-361">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="263c3-361">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="263c3-363">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="263c3-363">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="263c3-364">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="263c3-364">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="263c3-365">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-365">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="263c3-366">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-366">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="263c3-367">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="263c3-367">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="263c3-368">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-368">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="263c3-369">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-369">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="263c3-370">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-370">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="263c3-371">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-371">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="263c3-373">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="263c3-373">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="263c3-374">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-374">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="263c3-375">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-375">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="263c3-376">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-376">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="263c3-377">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-377">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="263c3-378">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-378">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="263c3-379">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="263c3-379">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="263c3-380">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-380">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="263c3-381">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[ID]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-381">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="263c3-382">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="263c3-382">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="263c3-383">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="263c3-383">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="263c3-384">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-384">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="263c3-385">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="263c3-385">Deploy the app</span></span>

<span data-ttu-id="263c3-386">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="263c3-386">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="263c3-387">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="263c3-387">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="263c3-388">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="263c3-388">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="263c3-389">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-389">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="263c3-390">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="263c3-390">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="263c3-392">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="263c3-392">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="263c3-393">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="263c3-393">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="263c3-394">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-394">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="263c3-395">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="263c3-395">Alternatives to Web Deploy</span></span>

<span data-ttu-id="263c3-396">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-396">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="263c3-397">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-397">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="263c3-398">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="263c3-398">Browse the website</span></span>

<span data-ttu-id="263c3-399">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="263c3-399">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="263c3-400">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="263c3-400">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="263c3-401">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="263c3-401">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="263c3-403">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="263c3-403">Locked deployment files</span></span>

<span data-ttu-id="263c3-404">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-404">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="263c3-405">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="263c3-405">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="263c3-406">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="263c3-406">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="263c3-407">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="263c3-407">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="263c3-408">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-408">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="263c3-409">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="263c3-409">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="263c3-410">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-410">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="263c3-411">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="263c3-411">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="263c3-412">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="263c3-412">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="263c3-413">データの保護</span><span class="sxs-lookup"><span data-stu-id="263c3-413">Data protection</span></span>

<span data-ttu-id="263c3-414">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-414">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="263c3-415">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-415">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="263c3-416">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-416">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="263c3-417">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="263c3-417">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="263c3-418">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="263c3-418">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="263c3-419">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-419">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="263c3-420">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="263c3-420">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="263c3-421">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="263c3-421">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="263c3-422">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-422">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="263c3-423">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="263c3-423">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="263c3-424">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-424">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="263c3-425">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-425">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="263c3-426">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="263c3-426">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="263c3-427">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="263c3-427">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="263c3-428">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-428">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="263c3-429">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="263c3-429">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="263c3-430">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="263c3-430">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="263c3-431">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-431">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="263c3-432">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="263c3-432">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="263c3-433">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="263c3-433">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="263c3-434">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-434">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="263c3-435">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="263c3-435">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="263c3-436">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="263c3-436">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="263c3-437">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-437">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="263c3-438">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-438">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="263c3-439">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-439">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="263c3-440">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-440">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="263c3-441">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="263c3-441">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="263c3-442">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-442">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="263c3-443">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="263c3-443">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="263c3-444">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-444">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="263c3-445">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-445">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="263c3-446">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="263c3-446">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="263c3-447">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-447">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="263c3-448">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="263c3-448">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="263c3-449">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="263c3-449">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="263c3-450">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-450">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="263c3-451">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="263c3-451">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="263c3-452">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="263c3-452">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="263c3-453">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-453">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="263c3-454">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="263c3-454">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="263c3-455">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="263c3-455">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="263c3-456">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="263c3-456">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="263c3-457">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="263c3-457">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="263c3-458">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-458">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="263c3-459">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="263c3-459">Virtual Directories</span></span>

<span data-ttu-id="263c3-460">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="263c3-460">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="263c3-461">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="263c3-461">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="263c3-462">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="263c3-462">Sub-applications</span></span>

<span data-ttu-id="263c3-463">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="263c3-463">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="263c3-464">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="263c3-464">The sub-app's path becomes part of the root app's URL.</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="263c3-465">サブアプリに、ハンドラーとして ASP.NET Core モジュールを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="263c3-465">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="263c3-466">このモジュールをサブアプリの *web.config* ファイルにハンドラーとして追加すると、サブアプリを閲覧しようとすると、エラーのある構成ファイルを参照する *500.19 内部サーバー エラー* が返されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-466">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="263c3-467">次の例は、ASP.NET Core サブアプリの発行済み *web.config* ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="263c3-467">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="263c3-468">ASP.NET Core アプリの下に ASP.NET Core 以外のサブアプリをホスティングする場合は、サブアプリの *web.config* ファイルにある継承されたハンドラーを明示的に削除します。</span><span class="sxs-lookup"><span data-stu-id="263c3-468">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="263c3-469">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-469">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="263c3-470">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-470">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="263c3-471">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-471">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="263c3-472">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="263c3-472">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="263c3-473">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-473">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="263c3-474">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-474">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="263c3-475">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-475">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="263c3-476">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="263c3-476">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="263c3-477">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="263c3-477">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="263c3-478">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-478">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="263c3-479">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-479">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="263c3-480">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-480">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="263c3-481">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="263c3-481">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="263c3-482">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-482">Select **OK**.</span></span>

<span data-ttu-id="263c3-483">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="263c3-483">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="263c3-484">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="263c3-484">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="263c3-485">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="263c3-485">Configuration of IIS with web.config</span></span>

<span data-ttu-id="263c3-486">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="263c3-486">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="263c3-487">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="263c3-487">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="263c3-488">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="263c3-488">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="263c3-489">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-489">For more information, see the following topics:</span></span>

* [<span data-ttu-id="263c3-490">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="263c3-490">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="263c3-491">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-491">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="263c3-492">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="263c3-492">Configuration sections of web.config</span></span>

<span data-ttu-id="263c3-493">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="263c3-493">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="263c3-494">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-494">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="263c3-495">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-495">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="263c3-496">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="263c3-496">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="263c3-497">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-497">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="263c3-498">インプロセス ホスティング &ndash; 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-498">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="263c3-499">アウトプロセス ホスティング &ndash; アプリケーションをそれぞれ専用のアプリケーションプールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="263c3-499">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="263c3-500">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="263c3-500">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="263c3-501">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-501">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="263c3-502">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-502">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="263c3-503">サーバーで複数の Web サイトをホストする場合は、アプリをそれぞれ専用のアプリ プールで実行して、アプリを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="263c3-503">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="263c3-504">IIS の **[Web サイトの追加]** ダイアログはこの構成の既定です。</span><span class="sxs-lookup"><span data-stu-id="263c3-504">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="263c3-505">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-505">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="263c3-506">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-506">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="263c3-507">アプリケーション プール ID</span><span class="sxs-lookup"><span data-stu-id="263c3-507">Application Pool Identity</span></span>

<span data-ttu-id="263c3-508">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="263c3-508">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="263c3-509">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="263c3-509">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="263c3-510">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ID** が **ApplicationPoolIdentity** を使用するように設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-510">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="263c3-512">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="263c3-512">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="263c3-513">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="263c3-513">Resources can be secured using this identity.</span></span> <span data-ttu-id="263c3-514">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="263c3-514">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="263c3-515">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="263c3-515">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="263c3-516">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-516">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="263c3-517">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-517">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="263c3-518">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="263c3-518">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="263c3-519">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-519">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="263c3-520">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="263c3-520">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="263c3-521">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-521">Select the **Check Names** button.</span></span> <span data-ttu-id="263c3-522">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-522">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="263c3-523">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-523">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="263c3-524">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="263c3-524">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="263c3-525">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-525">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="263c3-527">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-527">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="263c3-529">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="263c3-529">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="263c3-530">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="263c3-530">Provide additional permissions as needed.</span></span>

<span data-ttu-id="263c3-531">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="263c3-531">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="263c3-532">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-532">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="263c3-533">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-533">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="263c3-534">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="263c3-534">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="263c3-535">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-535">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="263c3-536">インプロセス</span><span class="sxs-lookup"><span data-stu-id="263c3-536">In-process</span></span>
  * <span data-ttu-id="263c3-537">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="263c3-537">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="263c3-538">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="263c3-538">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="263c3-539">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="263c3-539">Out-of-process</span></span>
  * <span data-ttu-id="263c3-540">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="263c3-540">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="263c3-541">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-541">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="263c3-542">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="263c3-542">TLS 1.2 or later connection</span></span>

<span data-ttu-id="263c3-543">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-543">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="263c3-544">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="263c3-544">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="263c3-545">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="263c3-545">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="263c3-546">次の基本要件を満たすアウトプロセスの展開には、[HTTP/2](https://httpwg.org/specs/rfc7540.html) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="263c3-546">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="263c3-547">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="263c3-547">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="263c3-548">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="263c3-548">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="263c3-549">ターゲット フレームワーク:HTTP/2 接続は IIS によって完全に処理されるため、アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="263c3-549">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="263c3-550">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="263c3-550">TLS 1.2 or later connection</span></span>

<span data-ttu-id="263c3-551">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="263c3-551">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="263c3-552">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="263c3-552">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="263c3-553">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="263c3-553">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="263c3-554">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="263c3-554">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="263c3-555">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="263c3-555">CORS preflight requests</span></span>

<span data-ttu-id="263c3-556">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="263c3-556">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="263c3-557">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="263c3-557">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="263c3-558">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="263c3-558">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="263c3-559">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="263c3-559">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="263c3-560">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="263c3-560">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="263c3-561">[Application Initialization モジュール](#application-initialization-module) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="263c3-561">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="263c3-562">[アイドル タイムアウト](#idle-timeout) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="263c3-562">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="263c3-563">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="263c3-563">Application Initialization Module</span></span>

<span data-ttu-id="263c3-564">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="263c3-564">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="263c3-565">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="263c3-565">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="263c3-566">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="263c3-566">The request triggers the app to start.</span></span> <span data-ttu-id="263c3-567">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="263c3-567">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="263c3-568">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="263c3-568">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="263c3-569">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="263c3-569">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="263c3-570">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="263c3-570">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="263c3-571">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-571">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="263c3-572">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="263c3-572">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="263c3-573">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="263c3-573">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="263c3-574">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-574">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="263c3-575">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-575">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="263c3-576">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="263c3-576">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="263c3-577">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="263c3-577">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="263c3-578">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="263c3-578">Using IIS Manager:</span></span>

  1. <span data-ttu-id="263c3-579">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-579">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="263c3-580">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-580">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="263c3-581">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="263c3-581">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="263c3-582">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-582">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="263c3-583">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-583">Select **OK**.</span></span>
  1. <span data-ttu-id="263c3-584">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="263c3-584">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="263c3-585">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-585">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="263c3-586">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="263c3-586">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="263c3-587">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-587">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="263c3-588">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-588">Select **OK**.</span></span>

* <span data-ttu-id="263c3-589">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="263c3-589">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="263c3-590">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="263c3-590">Idle Timeout</span></span>

<span data-ttu-id="263c3-591">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="263c3-591">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="263c3-592">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-592">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="263c3-593">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-593">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="263c3-594">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-594">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="263c3-595">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="263c3-595">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="263c3-596">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="263c3-596">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="263c3-597">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="263c3-597">Select **OK**.</span></span>
1. <span data-ttu-id="263c3-598">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="263c3-598">Recycle the worker process.</span></span>

<span data-ttu-id="263c3-599">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="263c3-599">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="263c3-600">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="263c3-600">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="263c3-601">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="263c3-601">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="263c3-602">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="263c3-602">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="263c3-603">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="263c3-603">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="263c3-604">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="263c3-604">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="263c3-605">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="263c3-605">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

::: moniker-end

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="263c3-606">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="263c3-606">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="263c3-607">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="263c3-607">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="263c3-608">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="263c3-608">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="263c3-609">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="263c3-609">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="263c3-610">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="263c3-610">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="263c3-611">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="263c3-611">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="263c3-612">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="263c3-612">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="263c3-613">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="263c3-613">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>
