---
title: ASP.NET Core モジュール
author: guardrex
description: ASP.NET Core アプリをホストするための ASP.NET Core モジュールを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 4a360023cc7fab2f066d490f7f368fc35815703a
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500450"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="dac4e-103">ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="dac4e-103">ASP.NET Core Module</span></span>

<span data-ttu-id="dac4e-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dac4e-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-105">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="dac4e-106">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="dac4e-107">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="dac4e-108">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="dac4e-108">Supported Windows versions:</span></span>

* <span data-ttu-id="dac4e-109">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="dac4e-109">Windows 7 or later</span></span>
* <span data-ttu-id="dac4e-110">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="dac4e-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="dac4e-111">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="dac4e-112">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="dac4e-113">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="dac4e-114">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="dac4e-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="dac4e-115">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="dac4e-115">In-process hosting model</span></span>

<span data-ttu-id="dac4e-116">アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="dac4e-117">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="dac4e-118">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="dac4e-119">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="dac4e-120">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="dac4e-121">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="dac4e-122">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="dac4e-123">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="dac4e-124">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="dac4e-125">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="dac4e-126">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="dac4e-127">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-127">Use one app pool per app.</span></span>

* <span data-ttu-id="dac4e-128">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="dac4e-129">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="dac4e-130">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="dac4e-131">([CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) ではなく) `WebHostBuilder` を使用してアプリのホストを手動で設定するときに、アプリがこれまで Kestrel サーバー上で直接実行されていた場合 (セルフホステッド)、`UseKestrel` を呼び出してから `UseIISIntegration` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="dac4e-132">順序を逆にすると、ホストは開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="dac4e-133">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-133">Client disconnects are detected.</span></span> <span data-ttu-id="dac4e-134">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="dac4e-135">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="dac4e-136">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="dac4e-137">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="dac4e-138"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="dac4e-139">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="dac4e-140">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="dac4e-141"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="dac4e-142">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="dac4e-142">Out-of-process hosting model</span></span>

<span data-ttu-id="dac4e-143">アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="dac4e-144">`<AspNetCoreHostingModel>` プロパティは指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="dac4e-145">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="dac4e-146">`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="dac4e-147">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="dac4e-148">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="dac4e-149">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="dac4e-150">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-150">Configure the host to capture startup errors.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dac4e-151">アウトプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-151">When hosting out-of-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="dac4e-152">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-152">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="dac4e-153"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-153">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
    services.AddAuthentication(IISDefaults.AuthenticationScheme);
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
}
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="hosting-model-changes"></a><span data-ttu-id="dac4e-154">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="dac4e-154">Hosting model changes</span></span>

<span data-ttu-id="dac4e-155">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-155">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="dac4e-156">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-156">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="dac4e-157">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-157">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="dac4e-158">プロセス名</span><span class="sxs-lookup"><span data-stu-id="dac4e-158">Process name</span></span>

<span data-ttu-id="dac4e-159">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-159">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="dac4e-160">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-160">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="dac4e-161">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="dac4e-161">Supported Windows versions:</span></span>

* <span data-ttu-id="dac4e-162">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="dac4e-162">Windows 7 or later</span></span>
* <span data-ttu-id="dac4e-163">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="dac4e-163">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="dac4e-164">モジュールは、Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-164">The module only works with Kestrel.</span></span> <span data-ttu-id="dac4e-165">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-165">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="dac4e-166">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-166">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="dac4e-167">モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-167">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="dac4e-168">この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-168">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="dac4e-169">次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-169">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core モジュール](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="dac4e-171">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-171">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="dac4e-172">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-172">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="dac4e-173">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-173">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="dac4e-174">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-174">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="dac4e-175">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-175">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="dac4e-176">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-176">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="dac4e-177">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-177">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="dac4e-178">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-178">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="dac4e-179">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-179">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="dac4e-180">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-180">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="dac4e-181">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-181">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="dac4e-182">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-182">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="dac4e-183">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-183">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="dac4e-184">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="dac4e-184">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="dac4e-185">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="dac4e-185">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="dac4e-186">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="dac4e-186">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="dac4e-187">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="dac4e-187">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="dac4e-188">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-188">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="dac4e-189">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="dac4e-189">Configuration with web.config</span></span>

<span data-ttu-id="dac4e-190">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-190">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="dac4e-191">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-191">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="dac4e-192">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-192">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="dac4e-193"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-193">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="dac4e-194">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-194">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="dac4e-195">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-195">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="dac4e-196">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-196">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="dac4e-197">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-197">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="dac4e-198">属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-198">Attribute</span></span> | <span data-ttu-id="dac4e-199">説明</span><span class="sxs-lookup"><span data-stu-id="dac4e-199">Description</span></span> | <span data-ttu-id="dac4e-200">既定値</span><span class="sxs-lookup"><span data-stu-id="dac4e-200">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="dac4e-201">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-201">Optional string attribute.</span></span></p><p><span data-ttu-id="dac4e-202">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-202">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="dac4e-203">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-203">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="dac4e-204">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-204">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="dac4e-205">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-205">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="dac4e-206">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-206">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="dac4e-207">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-207">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="dac4e-208">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-208">Optional string attribute.</span></span></p><p><span data-ttu-id="dac4e-209">ホスティング モデルをインプロセス (`InProcess`) またはアウト プロセス (`OutOfProcess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-209">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="dac4e-210">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-210">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-211">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-211">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="dac4e-212">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-212">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="dac4e-213">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-213">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="dac4e-214">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-214">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="dac4e-215">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="dac4e-215">Default: `1`</span></span><br><span data-ttu-id="dac4e-216">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="dac4e-216">Min: `1`</span></span><br><span data-ttu-id="dac4e-217">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="dac4e-217">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="dac4e-218">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-218">Required string attribute.</span></span></p><p><span data-ttu-id="dac4e-219">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-219">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="dac4e-220">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-220">Relative paths are supported.</span></span> <span data-ttu-id="dac4e-221">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-221">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="dac4e-222">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-223">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-223">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="dac4e-224">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-224">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="dac4e-225">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-225">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="dac4e-226">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="dac4e-226">Default: `10`</span></span><br><span data-ttu-id="dac4e-227">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="dac4e-227">Min: `0`</span></span><br><span data-ttu-id="dac4e-228">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="dac4e-228">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="dac4e-229">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-229">Optional timespan attribute.</span></span></p><p><span data-ttu-id="dac4e-230">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-230">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="dac4e-231">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-231">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="dac4e-232">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-232">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="dac4e-233">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-233">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="dac4e-234">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-234">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="dac4e-235">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-235">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="dac4e-236">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="dac4e-236">Default: `00:02:00`</span></span><br><span data-ttu-id="dac4e-237">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="dac4e-237">Min: `00:00:00`</span></span><br><span data-ttu-id="dac4e-238">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="dac4e-238">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="dac4e-239">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-239">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-240">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-240">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="dac4e-241">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="dac4e-241">Default: `10`</span></span><br><span data-ttu-id="dac4e-242">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="dac4e-242">Min: `0`</span></span><br><span data-ttu-id="dac4e-243">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="dac4e-243">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="dac4e-244">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-244">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-245">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-245">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="dac4e-246">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-246">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="dac4e-247">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-247">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="dac4e-248">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="dac4e-248">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="dac4e-249">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="dac4e-249">Default: `120`</span></span><br><span data-ttu-id="dac4e-250">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="dac4e-250">Min: `0`</span></span><br><span data-ttu-id="dac4e-251">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="dac4e-251">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="dac4e-252">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-252">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="dac4e-253">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-253">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="dac4e-254">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-254">Optional string attribute.</span></span></p><p><span data-ttu-id="dac4e-255">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-255">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="dac4e-256">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-256">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="dac4e-257">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-257">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="dac4e-258">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-258">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="dac4e-259">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-259">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="dac4e-260">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-260">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="dac4e-261">属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-261">Attribute</span></span> | <span data-ttu-id="dac4e-262">説明</span><span class="sxs-lookup"><span data-stu-id="dac4e-262">Description</span></span> | <span data-ttu-id="dac4e-263">既定値</span><span class="sxs-lookup"><span data-stu-id="dac4e-263">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="dac4e-264">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-264">Optional string attribute.</span></span></p><p><span data-ttu-id="dac4e-265">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-265">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="dac4e-266">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-266">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="dac4e-267">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-267">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="dac4e-268">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-268">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="dac4e-269">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-269">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="dac4e-270">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-270">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="dac4e-271">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-271">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-272">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-272">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="dac4e-273">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-273">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="dac4e-274">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-274">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="dac4e-275">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="dac4e-275">Default: `1`</span></span><br><span data-ttu-id="dac4e-276">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="dac4e-276">Min: `1`</span></span><br><span data-ttu-id="dac4e-277">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="dac4e-277">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="dac4e-278">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-278">Required string attribute.</span></span></p><p><span data-ttu-id="dac4e-279">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-279">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="dac4e-280">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-280">Relative paths are supported.</span></span> <span data-ttu-id="dac4e-281">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-281">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="dac4e-282">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-282">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-283">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-283">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="dac4e-284">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-284">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="dac4e-285">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="dac4e-285">Default: `10`</span></span><br><span data-ttu-id="dac4e-286">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="dac4e-286">Min: `0`</span></span><br><span data-ttu-id="dac4e-287">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="dac4e-287">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="dac4e-288">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-288">Optional timespan attribute.</span></span></p><p><span data-ttu-id="dac4e-289">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-289">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="dac4e-290">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-290">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="dac4e-291">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="dac4e-291">Default: `00:02:00`</span></span><br><span data-ttu-id="dac4e-292">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="dac4e-292">Min: `00:00:00`</span></span><br><span data-ttu-id="dac4e-293">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="dac4e-293">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="dac4e-294">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-294">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-295">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-295">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="dac4e-296">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="dac4e-296">Default: `10`</span></span><br><span data-ttu-id="dac4e-297">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="dac4e-297">Min: `0`</span></span><br><span data-ttu-id="dac4e-298">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="dac4e-298">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="dac4e-299">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="dac4e-299">Optional integer attribute.</span></span></p><p><span data-ttu-id="dac4e-300">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-300">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="dac4e-301">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-301">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="dac4e-302">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-302">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="dac4e-303">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="dac4e-303">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="dac4e-304">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="dac4e-304">Default: `120`</span></span><br><span data-ttu-id="dac4e-305">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="dac4e-305">Min: `0`</span></span><br><span data-ttu-id="dac4e-306">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="dac4e-306">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="dac4e-307">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-307">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="dac4e-308">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-308">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="dac4e-309">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="dac4e-309">Optional string attribute.</span></span></p><p><span data-ttu-id="dac4e-310">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-310">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="dac4e-311">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-311">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="dac4e-312">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-312">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="dac4e-313">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-313">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="dac4e-314">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-314">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="dac4e-315">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-315">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="dac4e-316">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="dac4e-316">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dac4e-317">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-317">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="dac4e-318">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-318">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="dac4e-319">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-319">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dac4e-320">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-320">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="dac4e-321">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-321">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="dac4e-322">このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-322">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="dac4e-323">*web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-323">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="dac4e-324">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-324">The following example sets two environment variables.</span></span> <span data-ttu-id="dac4e-325">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-325">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="dac4e-326">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-326">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="dac4e-327">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-327">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="dac4e-328">*web.config* 内で環境を直接設定する代わりに、発行プロファイル ( *.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-328">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="dac4e-329">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-329">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="dac4e-330">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="dac4e-330">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="dac4e-331">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="dac4e-331">app_offline.htm</span></span>

<span data-ttu-id="dac4e-332">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-332">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="dac4e-333">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-333">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="dac4e-334">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-334">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="dac4e-335">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-335">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-336">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-336">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="dac4e-337">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-337">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="dac4e-338">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="dac4e-338">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-339">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-339">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="dac4e-340">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-340">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="dac4e-341">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-341">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="dac4e-342">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-342">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="dac4e-343">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="dac4e-343">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="dac4e-344">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-344">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="dac4e-345">ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-345">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="dac4e-346">このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="dac4e-346">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="dac4e-347">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-347">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 処理エラーの状態コード ページ](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="dac4e-349">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="dac4e-349">Log creation and redirection</span></span>

<span data-ttu-id="dac4e-350">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-350">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="dac4e-351">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-351">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="dac4e-352">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-352">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="dac4e-353">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-353">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="dac4e-354">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-354">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="dac4e-355">stdout ログの使用は、アプリ起動時の問題をトラブルシューティングする場合にのみ推奨されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-355">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="dac4e-356">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-356">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="dac4e-357">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="dac4e-357">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="dac4e-358">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-358">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="dac4e-359">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-359">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="dac4e-360">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-360">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="dac4e-361">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-361">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-362">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-362">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="dac4e-363">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-363">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="dac4e-364">次の `aspNetCore` 要素の例では、Azure App Service でホストされているアプリの stdout ログを構成しています。</span><span class="sxs-lookup"><span data-stu-id="dac4e-364">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="dac4e-365">ローカル ログには、ローカル パスまたはネットワーク共有パスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-365">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="dac4e-366">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-366">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="dac4e-367">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="dac4e-367">Enhanced diagnostic logs</span></span>

<span data-ttu-id="dac4e-368">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-368">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="dac4e-369">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-369">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dac4e-370">パスに含まれるフォルダー (前の例では *logs*) は、ログ ファイルの作成時に、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-370">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="dac4e-371">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-371">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dac4e-372">`<handlerSetting>` 値に提供されるパスのフォルダー (前の例では *logs*) がこのモジュールによって自動的に作成されることはなく、デプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-372">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="dac4e-373">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-373">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-374">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-374">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="dac4e-375">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-375">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="dac4e-376">ERROR</span><span class="sxs-lookup"><span data-stu-id="dac4e-376">ERROR</span></span>
* <span data-ttu-id="dac4e-377">WARNING</span><span class="sxs-lookup"><span data-stu-id="dac4e-377">WARNING</span></span>
* <span data-ttu-id="dac4e-378">INFO</span><span class="sxs-lookup"><span data-stu-id="dac4e-378">INFO</span></span>
* <span data-ttu-id="dac4e-379">TRACE</span><span class="sxs-lookup"><span data-stu-id="dac4e-379">TRACE</span></span>

<span data-ttu-id="dac4e-380">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-380">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="dac4e-381">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="dac4e-381">CONSOLE</span></span>
* <span data-ttu-id="dac4e-382">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="dac4e-382">EVENTLOG</span></span>
* <span data-ttu-id="dac4e-383">ファイル</span><span class="sxs-lookup"><span data-stu-id="dac4e-383">FILE</span></span>

<span data-ttu-id="dac4e-384">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-384">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="dac4e-385">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルへのパス</span><span class="sxs-lookup"><span data-stu-id="dac4e-385">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="dac4e-386">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-386">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="dac4e-387">`ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="dac4e-387">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="dac4e-388">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="dac4e-388">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="dac4e-389">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-389">The size of the log isn't limited.</span></span> <span data-ttu-id="dac4e-390">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-390">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="dac4e-391">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dac4e-391">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="modify-the-stack-size"></a><span data-ttu-id="dac4e-392">スタック サイズを変更する</span><span class="sxs-lookup"><span data-stu-id="dac4e-392">Modify the stack size</span></span>

<span data-ttu-id="dac4e-393">`stackSize` 設定を使ってマネージド スタック サイズを構成します (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="dac4e-393">Configure the managed stack size using the `stackSize` setting in bytes.</span></span> <span data-ttu-id="dac4e-394">既定のサイズは `1048576` バイト (1 MB) です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-394">The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="dac4e-395">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="dac4e-395">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-396">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="dac4e-396">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="dac4e-397">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="dac4e-397">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="dac4e-398">HTTP を使うと、パフォーマンスが最適化されます。この場合、モジュールと Kestrel の間のトラフィックは、ネットワーク インターフェイスから切り離されたループバック アドレスで発生します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-398">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="dac4e-399">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-399">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="dac4e-400">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-400">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="dac4e-401">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-401">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="dac4e-402">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-402">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="dac4e-403">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-403">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="dac4e-404">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-404">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="dac4e-405">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-405">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="dac4e-406">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="dac4e-406">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="dac4e-407">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="dac4e-407">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="dac4e-408">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-408">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="dac4e-409">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-409">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="dac4e-410">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-410">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="dac4e-411">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-411">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="dac4e-412">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-412">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="dac4e-413">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-413">Run the installer.</span></span>
1. <span data-ttu-id="dac4e-414">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-414">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="dac4e-415">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-415">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="dac4e-416">IIS 共有構成を使うときは、次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="dac4e-416">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="dac4e-417">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-417">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="dac4e-418">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-418">Run the installer.</span></span>
1. <span data-ttu-id="dac4e-419">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-419">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="dac4e-420">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="dac4e-420">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="dac4e-421">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="dac4e-421">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="dac4e-422">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="dac4e-422">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="dac4e-423">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-423">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="dac4e-424">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-424">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="dac4e-425">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="dac4e-425">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="dac4e-426">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="dac4e-426">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="dac4e-427">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="dac4e-427">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="dac4e-428">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="dac4e-428">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="dac4e-429">Module</span><span class="sxs-lookup"><span data-stu-id="dac4e-429">Module</span></span>

<span data-ttu-id="dac4e-430">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="dac4e-430">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="dac4e-431">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-431">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="dac4e-432">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-432">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="dac4e-433">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-433">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="dac4e-434">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-434">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="dac4e-435">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="dac4e-435">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="dac4e-436">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-436">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="dac4e-437">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-437">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="dac4e-438">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-438">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="dac4e-439">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="dac4e-439">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="dac4e-440">Schema</span><span class="sxs-lookup"><span data-stu-id="dac4e-440">Schema</span></span>

<span data-ttu-id="dac4e-441">**IIS**</span><span class="sxs-lookup"><span data-stu-id="dac4e-441">**IIS**</span></span>

* <span data-ttu-id="dac4e-442">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="dac4e-442">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="dac4e-443">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="dac4e-443">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

<span data-ttu-id="dac4e-444">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="dac4e-444">**IIS Express**</span></span>

* <span data-ttu-id="dac4e-445">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="dac4e-445">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="dac4e-446">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="dac4e-446">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="dac4e-447">構成</span><span class="sxs-lookup"><span data-stu-id="dac4e-447">Configuration</span></span>

<span data-ttu-id="dac4e-448">**IIS**</span><span class="sxs-lookup"><span data-stu-id="dac4e-448">**IIS**</span></span>

* <span data-ttu-id="dac4e-449">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="dac4e-449">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="dac4e-450">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="dac4e-450">**IIS Express**</span></span>

* <span data-ttu-id="dac4e-451">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="dac4e-451">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="dac4e-452">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="dac4e-452">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="dac4e-453">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="dac4e-453">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dac4e-454">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dac4e-454">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="dac4e-455">ASP.NET Core モジュールの GitHub リポジトリ (参照元)</span><span class="sxs-lookup"><span data-stu-id="dac4e-455">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
