---
title: ASP.NET Core モジュール
author: rick-anderson
description: ASP.NET Core アプリをホストするための ASP.NET Core モジュールを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650876"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="1bce3-103">ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="1bce3-103">ASP.NET Core Module</span></span>

<span data-ttu-id="1bce3-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="1bce3-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1bce3-105">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="1bce3-106">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="1bce3-107">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="1bce3-108">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="1bce3-108">Supported Windows versions:</span></span>

* <span data-ttu-id="1bce3-109">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="1bce3-109">Windows 7 or later</span></span>
* <span data-ttu-id="1bce3-110">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="1bce3-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1bce3-111">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1bce3-112">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="1bce3-113">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="1bce3-114">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="1bce3-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="1bce3-115">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="1bce3-115">In-process hosting model</span></span>

<span data-ttu-id="1bce3-116">ASP.NET Core アプリの既定はインプロセス ホスティング モデルです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="1bce3-117">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="1bce3-118">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="1bce3-119">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="1bce3-120">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="1bce3-121">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="1bce3-122">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="1bce3-123">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="1bce3-124">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="1bce3-125">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-125">Use one app pool per app.</span></span>

* <span data-ttu-id="1bce3-126">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1bce3-127">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="1bce3-128">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="1bce3-129">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-129">Client disconnects are detected.</span></span> <span data-ttu-id="1bce3-130">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="1bce3-131">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="1bce3-132">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="1bce3-133">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="1bce3-134"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="1bce3-135">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="1bce3-136">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="1bce3-137"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="1bce3-138">[Web パッケージ (単一ファイル) の配置](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="1bce3-139">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="1bce3-139">Out-of-process hosting model</span></span>

<span data-ttu-id="1bce3-140">アウトプロセス ホスティング用にアプリを構成するには、プロジェクト ファイル ( *.csproj*) で、`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="1bce3-141">インプロセス ホスティングは `InProcess` で設定され、これが既定値です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="1bce3-142">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="1bce3-143">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1bce3-144">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="1bce3-145">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="1bce3-146">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="1bce3-147">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="1bce3-147">Hosting model changes</span></span>

<span data-ttu-id="1bce3-148">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="1bce3-149">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="1bce3-150">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="1bce3-151">プロセス名</span><span class="sxs-lookup"><span data-stu-id="1bce3-151">Process name</span></span>

<span data-ttu-id="1bce3-152">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="1bce3-153">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="1bce3-154">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="1bce3-155">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="1bce3-156">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="1bce3-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="1bce3-157">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="1bce3-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="1bce3-158">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="1bce3-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="1bce3-159">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="1bce3-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="1bce3-160">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="1bce3-161">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="1bce3-161">Configuration with web.config</span></span>

<span data-ttu-id="1bce3-162">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="1bce3-163">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1bce3-164">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1bce3-165"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="1bce3-166">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1bce3-167">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="1bce3-168">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="1bce3-169">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="1bce3-170">属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-170">Attribute</span></span> | <span data-ttu-id="1bce3-171">説明</span><span class="sxs-lookup"><span data-stu-id="1bce3-171">Description</span></span> | <span data-ttu-id="1bce3-172">Default</span><span class="sxs-lookup"><span data-stu-id="1bce3-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="1bce3-173">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-173">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-174">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="1bce3-175">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-176">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="1bce3-177">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-178">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="1bce3-179">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="1bce3-180">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-180">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-181">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="1bce3-182">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-183">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="1bce3-184">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="1bce3-185">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="1bce3-186">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="1bce3-187">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="1bce3-187">Default: `1`</span></span><br><span data-ttu-id="1bce3-188">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="1bce3-188">Min: `1`</span></span><br><span data-ttu-id="1bce3-189">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="1bce3-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="1bce3-190">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-190">Required string attribute.</span></span></p><p><span data-ttu-id="1bce3-191">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="1bce3-192">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-192">Relative paths are supported.</span></span> <span data-ttu-id="1bce3-193">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="1bce3-194">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-195">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="1bce3-196">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="1bce3-197">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="1bce3-198">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="1bce3-198">Default: `10`</span></span><br><span data-ttu-id="1bce3-199">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-199">Min: `0`</span></span><br><span data-ttu-id="1bce3-200">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="1bce3-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="1bce3-201">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="1bce3-202">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="1bce3-203">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="1bce3-204">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="1bce3-205">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="1bce3-206">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="1bce3-207">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="1bce3-208">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-208">Default: `00:02:00`</span></span><br><span data-ttu-id="1bce3-209">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-209">Min: `00:00:00`</span></span><br><span data-ttu-id="1bce3-210">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="1bce3-211">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-212">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="1bce3-213">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="1bce3-213">Default: `10`</span></span><br><span data-ttu-id="1bce3-214">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-214">Min: `0`</span></span><br><span data-ttu-id="1bce3-215">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="1bce3-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="1bce3-216">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-217">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="1bce3-218">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="1bce3-219">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="1bce3-220">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="1bce3-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="1bce3-221">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="1bce3-221">Default: `120`</span></span><br><span data-ttu-id="1bce3-222">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-222">Min: `0`</span></span><br><span data-ttu-id="1bce3-223">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="1bce3-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="1bce3-224">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-225">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="1bce3-226">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-226">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-227">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="1bce3-228">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="1bce3-229">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="1bce3-230">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="1bce3-231">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="1bce3-232">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="1bce3-233">環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="1bce3-233">Set environment variables</span></span>

<span data-ttu-id="1bce3-234">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="1bce3-235">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="1bce3-236">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="1bce3-237">次の例では、*web.config* で 2 つの環境変数を設定します。`ASPNETCORE_ENVIRONMENT` では、アプリの環境が `Development` に構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="1bce3-238">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="1bce3-239">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="1bce3-240">*web.config* 内で環境を直接設定する代わりに、[発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="1bce3-241">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="1bce3-242">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="1bce3-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="1bce3-243">app_offline.htm</span></span>

<span data-ttu-id="1bce3-244">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="1bce3-245">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="1bce3-246">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="1bce3-247">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="1bce3-248">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1bce3-249">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="1bce3-250">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="1bce3-250">Start-up error page</span></span>

<span data-ttu-id="1bce3-251">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="1bce3-252">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="1bce3-253">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="1bce3-254">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="1bce3-255">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="1bce3-256">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="1bce3-257">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="1bce3-257">Log creation and redirection</span></span>

<span data-ttu-id="1bce3-258">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="1bce3-259">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="1bce3-260">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1bce3-261">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="1bce3-262">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="1bce3-263">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="1bce3-264">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="1bce3-265">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1bce3-266">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="1bce3-267">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="1bce3-268">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="1bce3-269">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="1bce3-270">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="1bce3-271">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="1bce3-272">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="1bce3-273">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="1bce3-274">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1bce3-275">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="1bce3-276">ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="1bce3-277">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="1bce3-278">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="1bce3-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="1bce3-279">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="1bce3-280">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="1bce3-281">パスに含まれるフォルダー (前の例では *logs*) は、ログ ファイルの作成時に、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="1bce3-282">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1bce3-283">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="1bce3-284">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="1bce3-285">ERROR</span><span class="sxs-lookup"><span data-stu-id="1bce3-285">ERROR</span></span>
* <span data-ttu-id="1bce3-286">WARNING</span><span class="sxs-lookup"><span data-stu-id="1bce3-286">WARNING</span></span>
* <span data-ttu-id="1bce3-287">INFO</span><span class="sxs-lookup"><span data-stu-id="1bce3-287">INFO</span></span>
* <span data-ttu-id="1bce3-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="1bce3-288">TRACE</span></span>

<span data-ttu-id="1bce3-289">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="1bce3-290">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="1bce3-290">CONSOLE</span></span>
* <span data-ttu-id="1bce3-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="1bce3-291">EVENTLOG</span></span>
* <span data-ttu-id="1bce3-292">ファイル</span><span class="sxs-lookup"><span data-stu-id="1bce3-292">FILE</span></span>

<span data-ttu-id="1bce3-293">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="1bce3-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルのパス</span><span class="sxs-lookup"><span data-stu-id="1bce3-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="1bce3-295">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="1bce3-296">`ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="1bce3-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="1bce3-297">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1bce3-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="1bce3-298">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-298">The size of the log isn't limited.</span></span> <span data-ttu-id="1bce3-299">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="1bce3-300">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="1bce3-301">スタック サイズを変更する</span><span class="sxs-lookup"><span data-stu-id="1bce3-301">Modify the stack size</span></span>

<span data-ttu-id="1bce3-302">"*インプロセス ホスティング モデルを使用している場合にのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="1bce3-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="1bce3-303">*web.config* で `stackSize` の設定を使用してマネージド スタックのサイズを構成します (バイト単位)。既定のサイズは `1048576` バイト (1 MB) です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1bce3-304">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="1bce3-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1bce3-305">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="1bce3-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="1bce3-306">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1bce3-307">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1bce3-308">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1bce3-309">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1bce3-310">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1bce3-311">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1bce3-312">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1bce3-313">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1bce3-314">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1bce3-315">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="1bce3-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1bce3-316">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="1bce3-317">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="1bce3-318">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="1bce3-319">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="1bce3-320">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1bce3-321">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-321">Run the installer.</span></span>
1. <span data-ttu-id="1bce3-322">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="1bce3-323">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1bce3-324">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="1bce3-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1bce3-325">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="1bce3-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1bce3-326">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="1bce3-327">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="1bce3-328">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1bce3-329">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1bce3-330">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1bce3-331">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="1bce3-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1bce3-332">Module</span><span class="sxs-lookup"><span data-stu-id="1bce3-332">Module</span></span>

<span data-ttu-id="1bce3-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1bce3-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="1bce3-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1bce3-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="1bce3-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1bce3-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="1bce3-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1bce3-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="1bce3-343">Schema</span><span class="sxs-lookup"><span data-stu-id="1bce3-343">Schema</span></span>

<span data-ttu-id="1bce3-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1bce3-344">**IIS**</span></span>

* <span data-ttu-id="1bce3-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1bce3-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="1bce3-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1bce3-347">**IIS Express**</span></span>

* <span data-ttu-id="1bce3-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1bce3-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="1bce3-350">構成</span><span class="sxs-lookup"><span data-stu-id="1bce3-350">Configuration</span></span>

<span data-ttu-id="1bce3-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1bce3-351">**IIS**</span></span>

* <span data-ttu-id="1bce3-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="1bce3-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1bce3-353">**IIS Express**</span></span>

* <span data-ttu-id="1bce3-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="1bce3-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="1bce3-356">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1bce3-357">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="1bce3-358">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="1bce3-359">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="1bce3-360">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="1bce3-360">Supported Windows versions:</span></span>

* <span data-ttu-id="1bce3-361">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="1bce3-361">Windows 7 or later</span></span>
* <span data-ttu-id="1bce3-362">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="1bce3-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1bce3-363">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1bce3-364">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="1bce3-365">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="1bce3-366">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="1bce3-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="1bce3-367">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="1bce3-367">In-process hosting model</span></span>

<span data-ttu-id="1bce3-368">アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="1bce3-369">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="1bce3-370">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="1bce3-371">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="1bce3-372">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="1bce3-373">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="1bce3-374">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="1bce3-375">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="1bce3-376">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="1bce3-377">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="1bce3-378">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="1bce3-379">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="1bce3-380">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-380">Use one app pool per app.</span></span>

* <span data-ttu-id="1bce3-381">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1bce3-382">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="1bce3-383">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="1bce3-384">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-384">Client disconnects are detected.</span></span> <span data-ttu-id="1bce3-385">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="1bce3-386">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="1bce3-387">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="1bce3-388">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="1bce3-389"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="1bce3-390">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="1bce3-391">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="1bce3-392"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="1bce3-393">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="1bce3-393">Out-of-process hosting model</span></span>

<span data-ttu-id="1bce3-394">アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="1bce3-395">`<AspNetCoreHostingModel>` プロパティは指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="1bce3-396">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="1bce3-397">`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="1bce3-398">この値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="1bce3-399">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="1bce3-400">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="1bce3-401">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="1bce3-402">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="1bce3-403">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="1bce3-403">Hosting model changes</span></span>

<span data-ttu-id="1bce3-404">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="1bce3-405">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="1bce3-406">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="1bce3-407">プロセス名</span><span class="sxs-lookup"><span data-stu-id="1bce3-407">Process name</span></span>

<span data-ttu-id="1bce3-408">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="1bce3-409">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="1bce3-410">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="1bce3-411">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="1bce3-412">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="1bce3-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="1bce3-413">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="1bce3-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="1bce3-414">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="1bce3-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="1bce3-415">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="1bce3-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="1bce3-416">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="1bce3-417">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="1bce3-417">Configuration with web.config</span></span>

<span data-ttu-id="1bce3-418">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="1bce3-419">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1bce3-420">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1bce3-421"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="1bce3-422">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1bce3-423">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="1bce3-424">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="1bce3-425">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="1bce3-426">属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-426">Attribute</span></span> | <span data-ttu-id="1bce3-427">説明</span><span class="sxs-lookup"><span data-stu-id="1bce3-427">Description</span></span> | <span data-ttu-id="1bce3-428">Default</span><span class="sxs-lookup"><span data-stu-id="1bce3-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="1bce3-429">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-429">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-430">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="1bce3-431">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-432">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="1bce3-433">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-434">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="1bce3-435">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="1bce3-436">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-436">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-437">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="1bce3-438">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-439">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="1bce3-440">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="1bce3-441">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="1bce3-442">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="1bce3-443">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="1bce3-443">Default: `1`</span></span><br><span data-ttu-id="1bce3-444">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="1bce3-444">Min: `1`</span></span><br><span data-ttu-id="1bce3-445">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="1bce3-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="1bce3-446">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-446">Required string attribute.</span></span></p><p><span data-ttu-id="1bce3-447">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="1bce3-448">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-448">Relative paths are supported.</span></span> <span data-ttu-id="1bce3-449">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="1bce3-450">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-451">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="1bce3-452">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="1bce3-453">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="1bce3-454">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="1bce3-454">Default: `10`</span></span><br><span data-ttu-id="1bce3-455">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-455">Min: `0`</span></span><br><span data-ttu-id="1bce3-456">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="1bce3-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="1bce3-457">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="1bce3-458">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="1bce3-459">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="1bce3-460">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="1bce3-461">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="1bce3-462">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="1bce3-463">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="1bce3-464">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-464">Default: `00:02:00`</span></span><br><span data-ttu-id="1bce3-465">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-465">Min: `00:00:00`</span></span><br><span data-ttu-id="1bce3-466">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="1bce3-467">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-468">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="1bce3-469">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="1bce3-469">Default: `10`</span></span><br><span data-ttu-id="1bce3-470">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-470">Min: `0`</span></span><br><span data-ttu-id="1bce3-471">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="1bce3-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="1bce3-472">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-473">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="1bce3-474">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="1bce3-475">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="1bce3-476">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="1bce3-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="1bce3-477">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="1bce3-477">Default: `120`</span></span><br><span data-ttu-id="1bce3-478">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-478">Min: `0`</span></span><br><span data-ttu-id="1bce3-479">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="1bce3-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="1bce3-480">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-481">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="1bce3-482">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-482">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-483">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="1bce3-484">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="1bce3-485">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="1bce3-486">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="1bce3-487">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="1bce3-488">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="1bce3-489">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="1bce3-489">Setting environment variables</span></span>

<span data-ttu-id="1bce3-490">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="1bce3-491">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="1bce3-492">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="1bce3-493">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-493">The following example sets two environment variables.</span></span> <span data-ttu-id="1bce3-494">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="1bce3-495">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="1bce3-496">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="1bce3-497">*web.config* 内で環境を直接設定する代わりに、[発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="1bce3-498">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="1bce3-499">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="1bce3-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="1bce3-500">app_offline.htm</span></span>

<span data-ttu-id="1bce3-501">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="1bce3-502">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="1bce3-503">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="1bce3-504">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="1bce3-505">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1bce3-506">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="1bce3-507">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="1bce3-507">Start-up error page</span></span>

<span data-ttu-id="1bce3-508">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="1bce3-509">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="1bce3-510">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="1bce3-511">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="1bce3-512">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="1bce3-513">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="1bce3-514">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="1bce3-514">Log creation and redirection</span></span>

<span data-ttu-id="1bce3-515">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="1bce3-516">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="1bce3-517">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1bce3-518">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="1bce3-519">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="1bce3-520">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="1bce3-521">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="1bce3-522">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1bce3-523">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="1bce3-524">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="1bce3-525">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="1bce3-526">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="1bce3-527">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="1bce3-528">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="1bce3-529">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="1bce3-530">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="1bce3-531">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1bce3-532">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="1bce3-533">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="1bce3-534">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="1bce3-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="1bce3-535">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="1bce3-536">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="1bce3-537">`<handlerSetting>` 値に提供されるパスのフォルダー (前の例では *logs*) がこのモジュールによって自動的に作成されることはなく、デプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="1bce3-538">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1bce3-539">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="1bce3-540">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="1bce3-541">ERROR</span><span class="sxs-lookup"><span data-stu-id="1bce3-541">ERROR</span></span>
* <span data-ttu-id="1bce3-542">WARNING</span><span class="sxs-lookup"><span data-stu-id="1bce3-542">WARNING</span></span>
* <span data-ttu-id="1bce3-543">INFO</span><span class="sxs-lookup"><span data-stu-id="1bce3-543">INFO</span></span>
* <span data-ttu-id="1bce3-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="1bce3-544">TRACE</span></span>

<span data-ttu-id="1bce3-545">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="1bce3-546">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="1bce3-546">CONSOLE</span></span>
* <span data-ttu-id="1bce3-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="1bce3-547">EVENTLOG</span></span>
* <span data-ttu-id="1bce3-548">ファイル</span><span class="sxs-lookup"><span data-stu-id="1bce3-548">FILE</span></span>

<span data-ttu-id="1bce3-549">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="1bce3-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; デバッグ ログ ファイルのパス</span><span class="sxs-lookup"><span data-stu-id="1bce3-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="1bce3-551">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="1bce3-552">`ASPNETCORE_MODULE_DEBUG` &ndash; デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="1bce3-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="1bce3-553">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="1bce3-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="1bce3-554">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-554">The size of the log isn't limited.</span></span> <span data-ttu-id="1bce3-555">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="1bce3-556">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1bce3-557">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="1bce3-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1bce3-558">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="1bce3-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="1bce3-559">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1bce3-560">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1bce3-561">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1bce3-562">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1bce3-563">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1bce3-564">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1bce3-565">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1bce3-566">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1bce3-567">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1bce3-568">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="1bce3-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1bce3-569">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="1bce3-570">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="1bce3-571">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="1bce3-572">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="1bce3-573">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1bce3-574">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-574">Run the installer.</span></span>
1. <span data-ttu-id="1bce3-575">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="1bce3-576">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1bce3-577">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="1bce3-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1bce3-578">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="1bce3-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1bce3-579">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="1bce3-580">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="1bce3-581">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1bce3-582">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1bce3-583">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1bce3-584">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="1bce3-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1bce3-585">Module</span><span class="sxs-lookup"><span data-stu-id="1bce3-585">Module</span></span>

<span data-ttu-id="1bce3-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1bce3-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="1bce3-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1bce3-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="1bce3-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1bce3-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="1bce3-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="1bce3-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="1bce3-596">Schema</span><span class="sxs-lookup"><span data-stu-id="1bce3-596">Schema</span></span>

<span data-ttu-id="1bce3-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1bce3-597">**IIS**</span></span>

* <span data-ttu-id="1bce3-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1bce3-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="1bce3-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1bce3-600">**IIS Express**</span></span>

* <span data-ttu-id="1bce3-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="1bce3-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="1bce3-603">構成</span><span class="sxs-lookup"><span data-stu-id="1bce3-603">Configuration</span></span>

<span data-ttu-id="1bce3-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1bce3-604">**IIS**</span></span>

* <span data-ttu-id="1bce3-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="1bce3-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1bce3-606">**IIS Express**</span></span>

* <span data-ttu-id="1bce3-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="1bce3-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="1bce3-609">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1bce3-610">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="1bce3-611">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="1bce3-611">Supported Windows versions:</span></span>

* <span data-ttu-id="1bce3-612">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="1bce3-612">Windows 7 or later</span></span>
* <span data-ttu-id="1bce3-613">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="1bce3-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="1bce3-614">モジュールは、Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-614">The module only works with Kestrel.</span></span> <span data-ttu-id="1bce3-615">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="1bce3-616">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="1bce3-617">モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="1bce3-618">この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="1bce3-619">次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core モジュール](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="1bce3-621">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="1bce3-622">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="1bce3-623">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="1bce3-624">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="1bce3-625">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="1bce3-626">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="1bce3-627">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="1bce3-628">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="1bce3-629">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="1bce3-630">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="1bce3-631">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="1bce3-632">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="1bce3-633">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="1bce3-634">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="1bce3-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="1bce3-635">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="1bce3-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="1bce3-636">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="1bce3-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="1bce3-637">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="1bce3-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="1bce3-638">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="1bce3-639">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="1bce3-639">Configuration with web.config</span></span>

<span data-ttu-id="1bce3-640">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="1bce3-641">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="1bce3-642">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="1bce3-643">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1bce3-644">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="1bce3-645">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="1bce3-646">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="1bce3-647">属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-647">Attribute</span></span> | <span data-ttu-id="1bce3-648">説明</span><span class="sxs-lookup"><span data-stu-id="1bce3-648">Description</span></span> | <span data-ttu-id="1bce3-649">Default</span><span class="sxs-lookup"><span data-stu-id="1bce3-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="1bce3-650">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-650">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-651">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="1bce3-652">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-653">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="1bce3-654">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-655">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="1bce3-656">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="1bce3-657">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-658">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="1bce3-659">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="1bce3-660">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="1bce3-661">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="1bce3-661">Default: `1`</span></span><br><span data-ttu-id="1bce3-662">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="1bce3-662">Min: `1`</span></span><br><span data-ttu-id="1bce3-663">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="1bce3-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="1bce3-664">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-664">Required string attribute.</span></span></p><p><span data-ttu-id="1bce3-665">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="1bce3-666">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-666">Relative paths are supported.</span></span> <span data-ttu-id="1bce3-667">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="1bce3-668">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-669">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="1bce3-670">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="1bce3-671">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="1bce3-671">Default: `10`</span></span><br><span data-ttu-id="1bce3-672">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-672">Min: `0`</span></span><br><span data-ttu-id="1bce3-673">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="1bce3-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="1bce3-674">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="1bce3-675">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="1bce3-676">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="1bce3-677">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-677">Default: `00:02:00`</span></span><br><span data-ttu-id="1bce3-678">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-678">Min: `00:00:00`</span></span><br><span data-ttu-id="1bce3-679">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="1bce3-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="1bce3-680">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-681">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="1bce3-682">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="1bce3-682">Default: `10`</span></span><br><span data-ttu-id="1bce3-683">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-683">Min: `0`</span></span><br><span data-ttu-id="1bce3-684">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="1bce3-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="1bce3-685">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="1bce3-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="1bce3-686">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="1bce3-687">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="1bce3-688">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="1bce3-689">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="1bce3-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="1bce3-690">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="1bce3-690">Default: `120`</span></span><br><span data-ttu-id="1bce3-691">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="1bce3-691">Min: `0`</span></span><br><span data-ttu-id="1bce3-692">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="1bce3-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="1bce3-693">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="1bce3-694">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="1bce3-695">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="1bce3-695">Optional string attribute.</span></span></p><p><span data-ttu-id="1bce3-696">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="1bce3-697">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="1bce3-698">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="1bce3-699">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="1bce3-700">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="1bce3-701">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="1bce3-702">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="1bce3-702">Setting environment variables</span></span>

<span data-ttu-id="1bce3-703">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="1bce3-704">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="1bce3-705">このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="1bce3-706">*web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="1bce3-707">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="1bce3-707">The following example sets two environment variables.</span></span> <span data-ttu-id="1bce3-708">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="1bce3-709">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="1bce3-710">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="1bce3-711">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="1bce3-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="1bce3-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="1bce3-712">app_offline.htm</span></span>

<span data-ttu-id="1bce3-713">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="1bce3-714">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="1bce3-715">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="1bce3-716">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="1bce3-717">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="1bce3-717">Start-up error page</span></span>

<span data-ttu-id="1bce3-718">ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="1bce3-719">このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="1bce3-720">カスタム エラー メッセージの構成方法について詳しくは、「[HTTP エラー \<httpErrors](/iis/configuration/system.webServer/httpErrors/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 処理エラーの状態コード ページ](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="1bce3-722">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="1bce3-722">Log creation and redirection</span></span>

<span data-ttu-id="1bce3-723">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="1bce3-724">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="1bce3-725">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="1bce3-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="1bce3-726">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="1bce3-727">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="1bce3-728">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="1bce3-729">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="1bce3-730">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1bce3-731">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="1bce3-732">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="1bce3-733">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="1bce3-734">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="1bce3-735">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="1bce3-736">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="1bce3-737">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="1bce3-738">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="1bce3-739">ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="1bce3-740">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1bce3-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1bce3-741">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="1bce3-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1bce3-742">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1bce3-743">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1bce3-744">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1bce3-745">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1bce3-746">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1bce3-747">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1bce3-748">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1bce3-749">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1bce3-750">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="1bce3-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1bce3-751">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="1bce3-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1bce3-752">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="1bce3-753">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="1bce3-754">IIS 共有構成を使うときは、次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="1bce3-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="1bce3-755">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1bce3-756">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-756">Run the installer.</span></span>
1. <span data-ttu-id="1bce3-757">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="1bce3-758">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="1bce3-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="1bce3-759">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="1bce3-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="1bce3-760">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="1bce3-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="1bce3-761">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="1bce3-762">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="1bce3-763">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="1bce3-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="1bce3-764">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="1bce3-765">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は、*dd_DotNetCoreWinSvrHosting__\<タイムスタンプ>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="1bce3-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1bce3-766">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="1bce3-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1bce3-767">Module</span><span class="sxs-lookup"><span data-stu-id="1bce3-767">Module</span></span>

<span data-ttu-id="1bce3-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1bce3-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="1bce3-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="1bce3-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="1bce3-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="1bce3-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="1bce3-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="1bce3-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="1bce3-774">Schema</span><span class="sxs-lookup"><span data-stu-id="1bce3-774">Schema</span></span>

<span data-ttu-id="1bce3-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1bce3-775">**IIS**</span></span>

* <span data-ttu-id="1bce3-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="1bce3-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1bce3-777">**IIS Express**</span></span>

* <span data-ttu-id="1bce3-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="1bce3-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="1bce3-779">構成</span><span class="sxs-lookup"><span data-stu-id="1bce3-779">Configuration</span></span>

<span data-ttu-id="1bce3-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1bce3-780">**IIS**</span></span>

* <span data-ttu-id="1bce3-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="1bce3-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1bce3-782">**IIS Express**</span></span>

* <span data-ttu-id="1bce3-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="1bce3-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="1bce3-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="1bce3-785">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="1bce3-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1bce3-786">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1bce3-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="1bce3-787">[ASP.NET Core Module 参照ソース (マスター ブランチ)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; **Branch** ドロップ ダウン リストを使用して、特定のリリース (たとえば、`release/3.1`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="1bce3-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
