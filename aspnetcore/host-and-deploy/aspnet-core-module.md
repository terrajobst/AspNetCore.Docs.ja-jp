---
title: ASP.NET Core モジュール
author: guardrex
description: ASP.NET Core アプリをホストするための ASP.NET Core モジュールを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 917ee462a8f9592120685b53d059a661cb4a7452
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333891"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="f4991-103">ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="f4991-103">ASP.NET Core Module</span></span>

<span data-ttu-id="f4991-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f4991-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4991-105">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f4991-106">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="f4991-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f4991-107">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="f4991-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f4991-108">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="f4991-108">Supported Windows versions:</span></span>

* <span data-ttu-id="f4991-109">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="f4991-109">Windows 7 or later</span></span>
* <span data-ttu-id="f4991-110">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="f4991-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f4991-111">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f4991-112">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="f4991-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f4991-113">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="f4991-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f4991-114">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="f4991-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f4991-115">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="f4991-115">In-process hosting model</span></span>

<span data-ttu-id="f4991-116">ASP.NET Core アプリの既定はインプロセス ホスティング モデルです。</span><span class="sxs-lookup"><span data-stu-id="f4991-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="f4991-117">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f4991-118">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f4991-119">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f4991-120">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="f4991-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f4991-121">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f4991-122">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f4991-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f4991-124">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f4991-125">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f4991-125">Use one app pool per app.</span></span>

* <span data-ttu-id="f4991-126">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f4991-127">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f4991-128">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f4991-129">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-129">Client disconnects are detected.</span></span> <span data-ttu-id="f4991-130">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f4991-131">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="f4991-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f4991-132">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f4991-133">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f4991-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f4991-134"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f4991-135">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="f4991-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f4991-136">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f4991-137"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f4991-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="f4991-138">[Web パッケージ (単一ファイル) の配置](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f4991-139">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="f4991-139">Out-of-process hosting model</span></span>

<span data-ttu-id="f4991-140">アウトプロセス ホスティング用にアプリを構成するには、プロジェクト ファイル ( *.csproj*) で、`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f4991-141">インプロセス ホスティングは `InProcess` で設定され、これが既定値です。</span><span class="sxs-lookup"><span data-stu-id="f4991-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="f4991-142">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-142">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f4991-143">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-143">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f4991-144">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-144">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f4991-145">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-145">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f4991-146">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="f4991-146">Hosting model changes</span></span>

<span data-ttu-id="f4991-147">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-147">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f4991-148">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-148">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f4991-149">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-149">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f4991-150">プロセス名</span><span class="sxs-lookup"><span data-stu-id="f4991-150">Process name</span></span>

<span data-ttu-id="f4991-151">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-151">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f4991-152">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="f4991-152">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f4991-153">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-153">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f4991-154">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="f4991-154">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f4991-155">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="f4991-155">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f4991-156">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="f4991-156">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f4991-157">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="f4991-157">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f4991-158">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="f4991-158">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f4991-159">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-159">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f4991-160">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="f4991-160">Configuration with web.config</span></span>

<span data-ttu-id="f4991-161">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-161">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f4991-162">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-162">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f4991-163">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="f4991-163">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f4991-164"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="f4991-164">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f4991-165">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-165">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f4991-166">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="f4991-166">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f4991-167">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-167">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f4991-168">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="f4991-168">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f4991-169">属性</span><span class="sxs-lookup"><span data-stu-id="f4991-169">Attribute</span></span> | <span data-ttu-id="f4991-170">説明</span><span class="sxs-lookup"><span data-stu-id="f4991-170">Description</span></span> | <span data-ttu-id="f4991-171">既定値</span><span class="sxs-lookup"><span data-stu-id="f4991-171">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f4991-172">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-172">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-173">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="f4991-173">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f4991-174">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-174">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-175">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-175">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f4991-176">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-176">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-177">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-177">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f4991-178">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="f4991-178">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f4991-179">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-179">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-180">ホスティング モデルをインプロセス (`InProcess`) またはアウト プロセス (`OutOfProcess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-180">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="f4991-181">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-181">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-182">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-182">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f4991-183">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-183">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f4991-184">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-184">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f4991-185">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-185">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f4991-186">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="f4991-186">Default: `1`</span></span><br><span data-ttu-id="f4991-187">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="f4991-187">Min: `1`</span></span><br><span data-ttu-id="f4991-188">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="f4991-188">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f4991-189">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-189">Required string attribute.</span></span></p><p><span data-ttu-id="f4991-190">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="f4991-190">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f4991-191">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f4991-191">Relative paths are supported.</span></span> <span data-ttu-id="f4991-192">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-192">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f4991-193">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-194">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-194">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f4991-195">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="f4991-195">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f4991-196">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-196">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f4991-197">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="f4991-197">Default: `10`</span></span><br><span data-ttu-id="f4991-198">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-198">Min: `0`</span></span><br><span data-ttu-id="f4991-199">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="f4991-199">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f4991-200">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-200">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f4991-201">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-201">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f4991-202">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-202">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f4991-203">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-203">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f4991-204">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="f4991-204">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f4991-205">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="f4991-205">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f4991-206">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="f4991-206">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="f4991-207">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-207">Default: `00:02:00`</span></span><br><span data-ttu-id="f4991-208">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-208">Min: `00:00:00`</span></span><br><span data-ttu-id="f4991-209">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-209">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f4991-210">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-210">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-211">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="f4991-211">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f4991-212">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="f4991-212">Default: `10`</span></span><br><span data-ttu-id="f4991-213">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-213">Min: `0`</span></span><br><span data-ttu-id="f4991-214">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="f4991-214">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f4991-215">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-215">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-216">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="f4991-216">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f4991-217">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="f4991-217">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f4991-218">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="f4991-218">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f4991-219">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="f4991-219">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f4991-220">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="f4991-220">Default: `120`</span></span><br><span data-ttu-id="f4991-221">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-221">Min: `0`</span></span><br><span data-ttu-id="f4991-222">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="f4991-222">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f4991-223">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-223">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-224">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-224">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f4991-225">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-225">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-226">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-226">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f4991-227">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="f4991-227">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f4991-228">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="f4991-228">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f4991-229">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-229">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f4991-230">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-230">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f4991-231">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-231">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="f4991-232">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="f4991-232">Set environment variables</span></span>

<span data-ttu-id="f4991-233">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-233">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f4991-234">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-234">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f4991-235">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-235">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f4991-236">次の例では、*web.config* で 2 つの環境変数を設定します。`ASPNETCORE_ENVIRONMENT` では、アプリの環境が `Development` に構成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-236">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f4991-237">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-237">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f4991-238">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="f4991-238">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!NOTE]
> <span data-ttu-id="f4991-239">*web.config* 内で環境を直接設定する代わりに、発行プロファイル ( *.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f4991-239">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="f4991-240">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-240">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f4991-241">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="f4991-241">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f4991-242">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f4991-242">app_offline.htm</span></span>

<span data-ttu-id="f4991-243">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="f4991-243">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f4991-244">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="f4991-244">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f4991-245">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="f4991-245">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f4991-246">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-246">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f4991-247">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-247">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f4991-248">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-248">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f4991-249">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="f4991-249">Start-up error page</span></span>

<span data-ttu-id="f4991-250">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-250">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f4991-251">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-251">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f4991-252">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-252">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f4991-253">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-253">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f4991-254">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-254">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f4991-255">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-255">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f4991-256">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="f4991-256">Log creation and redirection</span></span>

<span data-ttu-id="f4991-257">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-257">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f4991-258">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-258">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f4991-259">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="f4991-259">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f4991-260">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-260">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f4991-261">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="f4991-261">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f4991-262">stdout ログの使用は、アプリ起動時の問題をトラブルシューティングする場合にのみ推奨されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-262">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f4991-263">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="f4991-263">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f4991-264">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-264">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f4991-265">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-265">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f4991-266">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-266">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f4991-267">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-267">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f4991-268">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="f4991-268">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f4991-269">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-269">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f4991-270">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-270">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f4991-271">次の例の *web.config* ファイルの `aspNetCore` 要素では、Azure App Service でホストされているアプリの stdout ログが構成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-271">The following sample `aspNetCore` element in a *web.config* file configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f4991-272">ローカル ログには、ローカル パスまたはネットワーク共有パスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-272">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f4991-273">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f4991-274">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="f4991-274">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f4991-275">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-275">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f4991-276">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="f4991-276">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="f4991-277">パスに含まれるフォルダー (前の例では *logs*) は、ログ ファイルの作成時に、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-277">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="f4991-278">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="f4991-278">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f4991-279">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f4991-279">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f4991-280">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="f4991-280">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f4991-281">ERROR</span><span class="sxs-lookup"><span data-stu-id="f4991-281">ERROR</span></span>
* <span data-ttu-id="f4991-282">WARNING</span><span class="sxs-lookup"><span data-stu-id="f4991-282">WARNING</span></span>
* <span data-ttu-id="f4991-283">INFO</span><span class="sxs-lookup"><span data-stu-id="f4991-283">INFO</span></span>
* <span data-ttu-id="f4991-284">TRACE</span><span class="sxs-lookup"><span data-stu-id="f4991-284">TRACE</span></span>

<span data-ttu-id="f4991-285">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="f4991-285">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f4991-286">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f4991-286">CONSOLE</span></span>
* <span data-ttu-id="f4991-287">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f4991-287">EVENTLOG</span></span>
* <span data-ttu-id="f4991-288">ファイル</span><span class="sxs-lookup"><span data-stu-id="f4991-288">FILE</span></span>

<span data-ttu-id="f4991-289">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="f4991-289">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f4991-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルへのパス</span><span class="sxs-lookup"><span data-stu-id="f4991-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="f4991-291">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="f4991-291">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f4991-292">`ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="f4991-292">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f4991-293">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="f4991-293">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f4991-294">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-294">The size of the log isn't limited.</span></span> <span data-ttu-id="f4991-295">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-295">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f4991-296">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-296">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="f4991-297">スタック サイズを変更する</span><span class="sxs-lookup"><span data-stu-id="f4991-297">Modify the stack size</span></span>

<span data-ttu-id="f4991-298">"*インプロセス ホスティング モデルを使用している場合にのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="f4991-298">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="f4991-299">*web.config* で `stackSize` の設定を使用してマネージド スタックのサイズを構成します (バイト単位)。既定のサイズは `1048576` バイト (1 MB) です。</span><span class="sxs-lookup"><span data-stu-id="f4991-299">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f4991-300">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="f4991-300">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f4991-301">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="f4991-301">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f4991-302">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-302">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f4991-303">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="f4991-303">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f4991-304">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-304">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f4991-305">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-305">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f4991-306">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-306">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f4991-307">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="f4991-307">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f4991-308">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-308">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f4991-309">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f4991-309">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f4991-310">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="f4991-310">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f4991-311">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="f4991-311">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f4991-312">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-312">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f4991-313">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-313">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f4991-314">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-314">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f4991-315">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-315">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f4991-316">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="f4991-316">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f4991-317">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-317">Run the installer.</span></span>
1. <span data-ttu-id="f4991-318">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="f4991-318">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f4991-319">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="f4991-319">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f4991-320">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="f4991-320">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f4991-321">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="f4991-321">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f4991-322">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="f4991-322">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f4991-323">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="f4991-323">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f4991-324">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="f4991-324">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f4991-325">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="f4991-325">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f4991-326">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="f4991-326">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f4991-327">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="f4991-327">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f4991-328">Module</span><span class="sxs-lookup"><span data-stu-id="f4991-328">Module</span></span>

<span data-ttu-id="f4991-329">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f4991-329">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f4991-330">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-330">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f4991-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="f4991-334">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f4991-334">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f4991-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f4991-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f4991-339">Schema</span><span class="sxs-lookup"><span data-stu-id="f4991-339">Schema</span></span>

<span data-ttu-id="f4991-340">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f4991-340">**IIS**</span></span>

* <span data-ttu-id="f4991-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f4991-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="f4991-343">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f4991-343">**IIS Express**</span></span>

* <span data-ttu-id="f4991-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f4991-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f4991-346">構成</span><span class="sxs-lookup"><span data-stu-id="f4991-346">Configuration</span></span>

<span data-ttu-id="f4991-347">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f4991-347">**IIS**</span></span>

* <span data-ttu-id="f4991-348">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-348">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f4991-349">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f4991-349">**IIS Express**</span></span>

* <span data-ttu-id="f4991-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f4991-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f4991-352">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="f4991-352">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f4991-353">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-353">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f4991-354">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="f4991-354">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f4991-355">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="f4991-355">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f4991-356">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="f4991-356">Supported Windows versions:</span></span>

* <span data-ttu-id="f4991-357">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="f4991-357">Windows 7 or later</span></span>
* <span data-ttu-id="f4991-358">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="f4991-358">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f4991-359">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-359">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f4991-360">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="f4991-360">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f4991-361">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="f4991-361">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f4991-362">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="f4991-362">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f4991-363">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="f4991-363">In-process hosting model</span></span>

<span data-ttu-id="f4991-364">アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。</span><span class="sxs-lookup"><span data-stu-id="f4991-364">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f4991-365">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-365">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="f4991-366">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="f4991-366">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="f4991-367">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-367">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f4991-368">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-368">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f4991-369">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-369">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f4991-370">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="f4991-370">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f4991-371">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-371">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f4991-372">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-372">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f4991-373">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-373">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f4991-374">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-374">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f4991-375">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f4991-375">Use one app pool per app.</span></span>

* <span data-ttu-id="f4991-376">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-376">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f4991-377">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-377">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f4991-378">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-378">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f4991-379">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-379">Client disconnects are detected.</span></span> <span data-ttu-id="f4991-380">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-380">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f4991-381">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="f4991-381">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f4991-382">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-382">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f4991-383">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f4991-383">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f4991-384"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-384">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f4991-385">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="f4991-385">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f4991-386">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-386">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f4991-387"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f4991-387">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f4991-388">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="f4991-388">Out-of-process hosting model</span></span>

<span data-ttu-id="f4991-389">アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="f4991-389">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="f4991-390">`<AspNetCoreHostingModel>` プロパティは指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="f4991-390">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="f4991-391">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="f4991-391">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="f4991-392">`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。</span><span class="sxs-lookup"><span data-stu-id="f4991-392">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f4991-393">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-393">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f4991-394">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-394">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f4991-395">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-395">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f4991-396">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-396">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f4991-397">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="f4991-397">Hosting model changes</span></span>

<span data-ttu-id="f4991-398">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-398">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f4991-399">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-399">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f4991-400">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-400">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f4991-401">プロセス名</span><span class="sxs-lookup"><span data-stu-id="f4991-401">Process name</span></span>

<span data-ttu-id="f4991-402">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-402">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f4991-403">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="f4991-403">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f4991-404">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-404">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f4991-405">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="f4991-405">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f4991-406">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="f4991-406">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f4991-407">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="f4991-407">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f4991-408">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="f4991-408">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f4991-409">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="f4991-409">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f4991-410">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-410">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f4991-411">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="f4991-411">Configuration with web.config</span></span>

<span data-ttu-id="f4991-412">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-412">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f4991-413">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-413">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f4991-414">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="f4991-414">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f4991-415"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="f4991-415">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f4991-416">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-416">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f4991-417">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="f4991-417">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f4991-418">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-418">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f4991-419">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="f4991-419">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f4991-420">属性</span><span class="sxs-lookup"><span data-stu-id="f4991-420">Attribute</span></span> | <span data-ttu-id="f4991-421">説明</span><span class="sxs-lookup"><span data-stu-id="f4991-421">Description</span></span> | <span data-ttu-id="f4991-422">既定値</span><span class="sxs-lookup"><span data-stu-id="f4991-422">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f4991-423">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-423">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-424">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="f4991-424">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f4991-425">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-425">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-426">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-426">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f4991-427">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-427">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-428">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-428">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f4991-429">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="f4991-429">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f4991-430">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-430">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-431">ホスティング モデルをインプロセス (`InProcess`) またはアウト プロセス (`OutOfProcess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-431">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="f4991-432">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-432">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-433">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-433">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f4991-434">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-434">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f4991-435">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-435">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f4991-436">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-436">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f4991-437">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="f4991-437">Default: `1`</span></span><br><span data-ttu-id="f4991-438">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="f4991-438">Min: `1`</span></span><br><span data-ttu-id="f4991-439">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="f4991-439">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f4991-440">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-440">Required string attribute.</span></span></p><p><span data-ttu-id="f4991-441">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="f4991-441">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f4991-442">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f4991-442">Relative paths are supported.</span></span> <span data-ttu-id="f4991-443">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-443">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f4991-444">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-445">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-445">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f4991-446">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="f4991-446">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f4991-447">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-447">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f4991-448">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="f4991-448">Default: `10`</span></span><br><span data-ttu-id="f4991-449">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-449">Min: `0`</span></span><br><span data-ttu-id="f4991-450">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="f4991-450">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f4991-451">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-451">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f4991-452">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-452">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f4991-453">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-453">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f4991-454">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-454">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f4991-455">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="f4991-455">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f4991-456">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="f4991-456">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f4991-457">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="f4991-457">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="f4991-458">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-458">Default: `00:02:00`</span></span><br><span data-ttu-id="f4991-459">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-459">Min: `00:00:00`</span></span><br><span data-ttu-id="f4991-460">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-460">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f4991-461">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-461">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-462">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="f4991-462">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f4991-463">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="f4991-463">Default: `10`</span></span><br><span data-ttu-id="f4991-464">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-464">Min: `0`</span></span><br><span data-ttu-id="f4991-465">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="f4991-465">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f4991-466">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-466">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-467">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="f4991-467">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f4991-468">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="f4991-468">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f4991-469">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="f4991-469">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f4991-470">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="f4991-470">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f4991-471">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="f4991-471">Default: `120`</span></span><br><span data-ttu-id="f4991-472">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-472">Min: `0`</span></span><br><span data-ttu-id="f4991-473">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="f4991-473">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f4991-474">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-474">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-475">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-475">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f4991-476">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-476">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-477">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-477">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f4991-478">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="f4991-478">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f4991-479">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="f4991-479">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f4991-480">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-480">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f4991-481">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-481">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f4991-482">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-482">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f4991-483">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="f4991-483">Setting environment variables</span></span>

<span data-ttu-id="f4991-484">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-484">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f4991-485">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-485">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f4991-486">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-486">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f4991-487">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="f4991-487">The following example sets two environment variables.</span></span> <span data-ttu-id="f4991-488">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-488">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f4991-489">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-489">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f4991-490">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="f4991-490">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!NOTE]
> <span data-ttu-id="f4991-491">*web.config* 内で環境を直接設定する代わりに、発行プロファイル ( *.pubxml*) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f4991-491">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="f4991-492">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-492">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f4991-493">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="f4991-493">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f4991-494">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f4991-494">app_offline.htm</span></span>

<span data-ttu-id="f4991-495">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="f4991-495">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f4991-496">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="f4991-496">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f4991-497">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="f4991-497">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f4991-498">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-498">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f4991-499">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-499">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f4991-500">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-500">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f4991-501">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="f4991-501">Start-up error page</span></span>

<span data-ttu-id="f4991-502">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-502">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f4991-503">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-503">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f4991-504">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-504">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f4991-505">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-505">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f4991-506">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-506">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f4991-507">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-507">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f4991-508">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="f4991-508">Log creation and redirection</span></span>

<span data-ttu-id="f4991-509">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-509">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f4991-510">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-510">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f4991-511">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="f4991-511">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f4991-512">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-512">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f4991-513">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="f4991-513">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f4991-514">stdout ログの使用は、アプリ起動時の問題をトラブルシューティングする場合にのみ推奨されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-514">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f4991-515">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="f4991-515">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f4991-516">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-516">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f4991-517">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-517">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f4991-518">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-518">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f4991-519">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-519">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f4991-520">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="f4991-520">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f4991-521">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-521">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f4991-522">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-522">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f4991-523">次の `aspNetCore` 要素の例では、Azure App Service でホストされているアプリの stdout ログを構成しています。</span><span class="sxs-lookup"><span data-stu-id="f4991-523">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f4991-524">ローカル ログには、ローカル パスまたはネットワーク共有パスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-524">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f4991-525">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-525">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f4991-526">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="f4991-526">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f4991-527">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-527">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f4991-528">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="f4991-528">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="f4991-529">`<handlerSetting>` 値に提供されるパスのフォルダー (前の例では *logs*) がこのモジュールによって自動的に作成されることはなく、デプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-529">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="f4991-530">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="f4991-530">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f4991-531">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f4991-531">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f4991-532">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="f4991-532">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f4991-533">ERROR</span><span class="sxs-lookup"><span data-stu-id="f4991-533">ERROR</span></span>
* <span data-ttu-id="f4991-534">WARNING</span><span class="sxs-lookup"><span data-stu-id="f4991-534">WARNING</span></span>
* <span data-ttu-id="f4991-535">INFO</span><span class="sxs-lookup"><span data-stu-id="f4991-535">INFO</span></span>
* <span data-ttu-id="f4991-536">TRACE</span><span class="sxs-lookup"><span data-stu-id="f4991-536">TRACE</span></span>

<span data-ttu-id="f4991-537">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="f4991-537">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f4991-538">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f4991-538">CONSOLE</span></span>
* <span data-ttu-id="f4991-539">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f4991-539">EVENTLOG</span></span>
* <span data-ttu-id="f4991-540">ファイル</span><span class="sxs-lookup"><span data-stu-id="f4991-540">FILE</span></span>

<span data-ttu-id="f4991-541">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="f4991-541">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f4991-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルへのパス</span><span class="sxs-lookup"><span data-stu-id="f4991-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="f4991-543">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="f4991-543">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f4991-544">`ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="f4991-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f4991-545">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="f4991-545">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f4991-546">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="f4991-546">The size of the log isn't limited.</span></span> <span data-ttu-id="f4991-547">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-547">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f4991-548">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-548">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f4991-549">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="f4991-549">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f4991-550">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="f4991-550">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f4991-551">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-551">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f4991-552">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="f4991-552">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f4991-553">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-553">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f4991-554">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-554">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f4991-555">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-555">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f4991-556">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="f4991-556">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f4991-557">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-557">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f4991-558">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f4991-558">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f4991-559">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="f4991-559">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f4991-560">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="f4991-560">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f4991-561">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-561">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f4991-562">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-562">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f4991-563">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-563">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f4991-564">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-564">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f4991-565">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="f4991-565">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f4991-566">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-566">Run the installer.</span></span>
1. <span data-ttu-id="f4991-567">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="f4991-567">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f4991-568">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="f4991-568">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f4991-569">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="f4991-569">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f4991-570">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="f4991-570">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f4991-571">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="f4991-571">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f4991-572">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="f4991-572">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f4991-573">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="f4991-573">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f4991-574">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="f4991-574">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f4991-575">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="f4991-575">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f4991-576">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="f4991-576">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f4991-577">Module</span><span class="sxs-lookup"><span data-stu-id="f4991-577">Module</span></span>

<span data-ttu-id="f4991-578">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f4991-578">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f4991-579">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-579">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f4991-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="f4991-583">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f4991-583">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f4991-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f4991-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f4991-588">Schema</span><span class="sxs-lookup"><span data-stu-id="f4991-588">Schema</span></span>

<span data-ttu-id="f4991-589">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f4991-589">**IIS**</span></span>

* <span data-ttu-id="f4991-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f4991-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="f4991-592">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f4991-592">**IIS Express**</span></span>

* <span data-ttu-id="f4991-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f4991-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f4991-595">構成</span><span class="sxs-lookup"><span data-stu-id="f4991-595">Configuration</span></span>

<span data-ttu-id="f4991-596">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f4991-596">**IIS**</span></span>

* <span data-ttu-id="f4991-597">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-597">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f4991-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f4991-598">**IIS Express**</span></span>

* <span data-ttu-id="f4991-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f4991-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f4991-601">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="f4991-601">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f4991-602">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="f4991-603">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="f4991-603">Supported Windows versions:</span></span>

* <span data-ttu-id="f4991-604">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="f4991-604">Windows 7 or later</span></span>
* <span data-ttu-id="f4991-605">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="f4991-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f4991-606">モジュールは、Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="f4991-606">The module only works with Kestrel.</span></span> <span data-ttu-id="f4991-607">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="f4991-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="f4991-608">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="f4991-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="f4991-609">モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="f4991-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="f4991-610">この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="f4991-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f4991-611">次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="f4991-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core モジュール](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="f4991-613">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="f4991-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f4991-614">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="f4991-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f4991-615">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f4991-616">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f4991-617">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f4991-618">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f4991-619">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f4991-620">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="f4991-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f4991-621">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f4991-622">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="f4991-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f4991-623">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="f4991-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f4991-624">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f4991-625">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="f4991-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f4991-626">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="f4991-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f4991-627">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="f4991-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f4991-628">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="f4991-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f4991-629">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="f4991-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f4991-630">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f4991-631">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="f4991-631">Configuration with web.config</span></span>

<span data-ttu-id="f4991-632">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f4991-633">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f4991-634">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="f4991-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f4991-635">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f4991-636">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="f4991-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f4991-637">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f4991-638">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="f4991-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f4991-639">属性</span><span class="sxs-lookup"><span data-stu-id="f4991-639">Attribute</span></span> | <span data-ttu-id="f4991-640">説明</span><span class="sxs-lookup"><span data-stu-id="f4991-640">Description</span></span> | <span data-ttu-id="f4991-641">既定値</span><span class="sxs-lookup"><span data-stu-id="f4991-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f4991-642">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-642">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-643">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="f4991-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f4991-644">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-645">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f4991-646">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-647">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f4991-648">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="f4991-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="f4991-649">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-650">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f4991-651">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f4991-652">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="f4991-653">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="f4991-653">Default: `1`</span></span><br><span data-ttu-id="f4991-654">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="f4991-654">Min: `1`</span></span><br><span data-ttu-id="f4991-655">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="f4991-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="f4991-656">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-656">Required string attribute.</span></span></p><p><span data-ttu-id="f4991-657">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="f4991-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f4991-658">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f4991-658">Relative paths are supported.</span></span> <span data-ttu-id="f4991-659">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f4991-660">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-661">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f4991-662">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="f4991-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="f4991-663">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="f4991-663">Default: `10`</span></span><br><span data-ttu-id="f4991-664">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-664">Min: `0`</span></span><br><span data-ttu-id="f4991-665">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="f4991-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f4991-666">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f4991-667">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f4991-668">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="f4991-669">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-669">Default: `00:02:00`</span></span><br><span data-ttu-id="f4991-670">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-670">Min: `00:00:00`</span></span><br><span data-ttu-id="f4991-671">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f4991-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f4991-672">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-673">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="f4991-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f4991-674">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="f4991-674">Default: `10`</span></span><br><span data-ttu-id="f4991-675">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-675">Min: `0`</span></span><br><span data-ttu-id="f4991-676">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="f4991-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f4991-677">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="f4991-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="f4991-678">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="f4991-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f4991-679">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="f4991-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f4991-680">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="f4991-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f4991-681">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="f4991-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f4991-682">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="f4991-682">Default: `120`</span></span><br><span data-ttu-id="f4991-683">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="f4991-683">Min: `0`</span></span><br><span data-ttu-id="f4991-684">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="f4991-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f4991-685">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="f4991-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f4991-686">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f4991-687">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="f4991-687">Optional string attribute.</span></span></p><p><span data-ttu-id="f4991-688">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f4991-689">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="f4991-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f4991-690">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="f4991-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f4991-691">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="f4991-692">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f4991-693">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f4991-694">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="f4991-694">Setting environment variables</span></span>

<span data-ttu-id="f4991-695">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f4991-696">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="f4991-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="f4991-697">このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。</span><span class="sxs-lookup"><span data-stu-id="f4991-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="f4991-698">*web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。</span><span class="sxs-lookup"><span data-stu-id="f4991-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="f4991-699">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="f4991-699">The following example sets two environment variables.</span></span> <span data-ttu-id="f4991-700">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="f4991-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f4991-701">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f4991-702">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="f4991-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!WARNING]
> <span data-ttu-id="f4991-703">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="f4991-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f4991-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f4991-704">app_offline.htm</span></span>

<span data-ttu-id="f4991-705">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="f4991-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f4991-706">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="f4991-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f4991-707">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="f4991-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f4991-708">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f4991-709">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="f4991-709">Start-up error page</span></span>

<span data-ttu-id="f4991-710">ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="f4991-711">このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f4991-712">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 処理エラーの状態コード ページ](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f4991-714">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="f4991-714">Log creation and redirection</span></span>

<span data-ttu-id="f4991-715">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-715">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f4991-716">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-716">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f4991-717">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="f4991-717">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f4991-718">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="f4991-718">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f4991-719">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="f4991-719">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f4991-720">stdout ログの使用は、アプリ起動時の問題をトラブルシューティングする場合にのみ推奨されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-720">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f4991-721">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="f4991-721">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f4991-722">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-722">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f4991-723">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-723">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f4991-724">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-724">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f4991-725">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-725">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f4991-726">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="f4991-726">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f4991-727">次の `aspNetCore` 要素の例では、Azure App Service でホストされているアプリの stdout ログを構成しています。</span><span class="sxs-lookup"><span data-stu-id="f4991-727">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f4991-728">ローカル ログには、ローカル パスまたはネットワーク共有パスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-728">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f4991-729">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="f4991-729">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="f4991-730">`<handlerSetting>` 値に提供されるパスのフォルダー (前の例では *logs*) がこのモジュールによって自動的に作成されることはなく、デプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f4991-730">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="f4991-731">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="f4991-731">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f4991-732">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f4991-732">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f4991-733">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="f4991-733">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f4991-734">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="f4991-734">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f4991-735">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="f4991-735">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f4991-736">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="f4991-736">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f4991-737">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-737">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f4991-738">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-738">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f4991-739">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="f4991-739">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f4991-740">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="f4991-740">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f4991-741">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f4991-741">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f4991-742">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="f4991-742">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f4991-743">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="f4991-743">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f4991-744">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-744">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f4991-745">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="f4991-745">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f4991-746">IIS 共有構成を使うときは、次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="f4991-746">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="f4991-747">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="f4991-747">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f4991-748">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="f4991-748">Run the installer.</span></span>
1. <span data-ttu-id="f4991-749">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="f4991-749">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f4991-750">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="f4991-750">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f4991-751">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="f4991-751">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f4991-752">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="f4991-752">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f4991-753">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="f4991-753">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f4991-754">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="f4991-754">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f4991-755">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="f4991-755">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f4991-756">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="f4991-756">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f4991-757">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="f4991-757">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f4991-758">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="f4991-758">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f4991-759">Module</span><span class="sxs-lookup"><span data-stu-id="f4991-759">Module</span></span>

<span data-ttu-id="f4991-760">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f4991-760">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f4991-761">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-761">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="f4991-763">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f4991-763">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f4991-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f4991-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f4991-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f4991-766">Schema</span><span class="sxs-lookup"><span data-stu-id="f4991-766">Schema</span></span>

<span data-ttu-id="f4991-767">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f4991-767">**IIS**</span></span>

* <span data-ttu-id="f4991-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="f4991-769">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f4991-769">**IIS Express**</span></span>

* <span data-ttu-id="f4991-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f4991-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f4991-771">構成</span><span class="sxs-lookup"><span data-stu-id="f4991-771">Configuration</span></span>

<span data-ttu-id="f4991-772">**IIS**</span><span class="sxs-lookup"><span data-stu-id="f4991-772">**IIS**</span></span>

* <span data-ttu-id="f4991-773">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-773">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f4991-774">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f4991-774">**IIS Express**</span></span>

* <span data-ttu-id="f4991-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f4991-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f4991-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f4991-777">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="f4991-777">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f4991-778">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f4991-778">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="f4991-779">ASP.NET Core モジュールの GitHub リポジトリ (参照元)</span><span class="sxs-lookup"><span data-stu-id="f4991-779">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
