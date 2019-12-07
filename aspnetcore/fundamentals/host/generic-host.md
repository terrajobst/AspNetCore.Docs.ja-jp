---
title: .NET での汎用ホスト
author: rick-anderson
description: アプリの起動と有効期間の管理を行う .NET Core 汎用ホストについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
uid: fundamentals/host/generic-host
ms.openlocfilehash: 2ed4af109b5ccd303a03a0d9167649dda7793126
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717023"
---
# <a name="net-generic-host"></a><span data-ttu-id="f5e61-103">.NET での汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="f5e61-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5e61-104">この記事では .NET Core 汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について紹介し、その使用方法に関するガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="f5e61-105">ホストとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="f5e61-105">What's a host?</span></span>

<span data-ttu-id="f5e61-106">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="f5e61-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="f5e61-107">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="f5e61-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="f5e61-108">ログの記録</span><span class="sxs-lookup"><span data-stu-id="f5e61-108">Logging</span></span>
* <span data-ttu-id="f5e61-109">構成</span><span class="sxs-lookup"><span data-stu-id="f5e61-109">Configuration</span></span>
* <span data-ttu-id="f5e61-110">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="f5e61-110">`IHostedService` implementations</span></span>

<span data-ttu-id="f5e61-111">ホストを開始すると、DI コンテナー内で検出された <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装に対して `IHostedService.StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="f5e61-112">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="f5e61-113">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="f5e61-114">ASP.NET Core の 3.0 より前のバージョンでは、[Web ホスト](xref:fundamentals/host/web-host)が HTTP ワークロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="f5e61-115">Web ホストは Web アプリの推奨ホストではなくなり、下位互換性用のみに引き続き利用できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="f5e61-116">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="f5e61-116">Set up a host</span></span>

<span data-ttu-id="f5e61-117">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="f5e61-118">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="f5e61-118">The `Main` method:</span></span>

* <span data-ttu-id="f5e61-119">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="f5e61-120">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="f5e61-121">HTTP 以外のワークロード用の *Program.cs* コードを次に示します。単一の `IHostedService` 実装が DI コンテナーに追加されています。</span><span class="sxs-lookup"><span data-stu-id="f5e61-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="f5e61-122">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="f5e61-123">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="f5e61-124">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="f5e61-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="f5e61-125">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="f5e61-126">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="f5e61-126">Default builder settings</span></span>

<span data-ttu-id="f5e61-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="f5e61-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="f5e61-128">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="f5e61-129">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="f5e61-130">プレフィックス "DOTNET_" が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="f5e61-130">Environment variables prefixed with "DOTNET_".</span></span>
  * <span data-ttu-id="f5e61-131">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="f5e61-131">Command-line arguments.</span></span>
* <span data-ttu-id="f5e61-132">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="f5e61-133">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="f5e61-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="f5e61-134">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="f5e61-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="f5e61-135">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="f5e61-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="f5e61-136">環境変数。</span><span class="sxs-lookup"><span data-stu-id="f5e61-136">Environment variables.</span></span>
  * <span data-ttu-id="f5e61-137">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="f5e61-137">Command-line arguments.</span></span>
* <span data-ttu-id="f5e61-138">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="f5e61-139">コンソール</span><span class="sxs-lookup"><span data-stu-id="f5e61-139">Console</span></span>
  * <span data-ttu-id="f5e61-140">デバッグ</span><span class="sxs-lookup"><span data-stu-id="f5e61-140">Debug</span></span>
  * <span data-ttu-id="f5e61-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="f5e61-141">EventSource</span></span>
  * <span data-ttu-id="f5e61-142">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="f5e61-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="f5e61-143">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="f5e61-144">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="f5e61-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="f5e61-145">プレフィックス "ASPNETCORE_" が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-145">Loads host configuration from environment variables prefixed with "ASPNETCORE_".</span></span>
* <span data-ttu-id="f5e61-146">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="f5e61-147">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="f5e61-148">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="f5e61-149">ASPNETCORE_FORWARDEDHEADERS_ENABLED=true の場合は、[Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if ASPNETCORE_FORWARDEDHEADERS_ENABLED=true.</span></span>
* <span data-ttu-id="f5e61-150">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-150">Enables IIS integration.</span></span> <span data-ttu-id="f5e61-151">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="f5e61-152">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f5e61-153">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="f5e61-153">Framework-provided services</span></span>

<span data-ttu-id="f5e61-154">自動的に登録されるサービスには、次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-154">Services that are registered automatically include the following:</span></span>

* [<span data-ttu-id="f5e61-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="f5e61-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="f5e61-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="f5e61-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="f5e61-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="f5e61-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="f5e61-158">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="f5e61-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="f5e61-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="f5e61-160">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="f5e61-161">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="f5e61-162">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="f5e61-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="f5e61-163">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="f5e61-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="f5e61-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="f5e61-164">IHostLifetime</span></span>

<span data-ttu-id="f5e61-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="f5e61-166">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-166">The last implementation registered is used.</span></span>

<span data-ttu-id="f5e61-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="f5e61-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="f5e61-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="f5e61-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="f5e61-169">Ctrl + C/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-169">Listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="f5e61-170">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="f5e61-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="f5e61-171">IHostEnvironment</span></span>

<span data-ttu-id="f5e61-172">次に関する情報を取得するために、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following:</span></span>

* [<span data-ttu-id="f5e61-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="f5e61-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="f5e61-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="f5e61-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="f5e61-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="f5e61-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="f5e61-176">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="f5e61-177">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="f5e61-177">Host configuration</span></span>

<span data-ttu-id="f5e61-178">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="f5e61-179">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="f5e61-180">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="f5e61-181">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="f5e61-182">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="f5e61-183">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="f5e61-184">プレフィックス `DOTNET_` を持つ環境変数プロバイダーと、コマンド ライン引数が CreateDefaultBuilder によって取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-184">The environment variable provider with prefix `DOTNET_` and command line args are included by CreateDefaultBuilder.</span></span> <span data-ttu-id="f5e61-185">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="f5e61-186">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="f5e61-187">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="f5e61-188">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="f5e61-189">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="f5e61-189">App configuration</span></span>

<span data-ttu-id="f5e61-190">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="f5e61-191">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="f5e61-192">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="f5e61-193">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="f5e61-194">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="f5e61-195">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="f5e61-196">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="f5e61-196">Settings for all app types</span></span>

<span data-ttu-id="f5e61-197">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="f5e61-198">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="f5e61-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="f5e61-199">ApplicationName</span></span>

<span data-ttu-id="f5e61-200">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="f5e61-201">**キー**: applicationName</span><span class="sxs-lookup"><span data-stu-id="f5e61-201">**Key**: applicationName</span></span>  
<span data-ttu-id="f5e61-202">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-202">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-203">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="f5e61-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>
<span data-ttu-id="f5e61-204">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="f5e61-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="f5e61-205">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="f5e61-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="f5e61-206">ContentRootPath</span></span>

<span data-ttu-id="f5e61-207">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="f5e61-208">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="f5e61-209">**キー**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="f5e61-209">**Key**: contentRoot</span></span>  
<span data-ttu-id="f5e61-210">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-210">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-211">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="f5e61-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="f5e61-212">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="f5e61-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="f5e61-213">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="f5e61-214">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="f5e61-214">For more information, see:</span></span>

* [<span data-ttu-id="f5e61-215">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="f5e61-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="f5e61-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="f5e61-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="f5e61-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="f5e61-217">EnvironmentName</span></span>

<span data-ttu-id="f5e61-218">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="f5e61-219">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="f5e61-220">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-220">Values aren't case sensitive.</span></span>

<span data-ttu-id="f5e61-221">**キー**: 環境</span><span class="sxs-lookup"><span data-stu-id="f5e61-221">**Key**: environment</span></span>  
<span data-ttu-id="f5e61-222">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-222">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-223">**既定値**:実稼働</span><span class="sxs-lookup"><span data-stu-id="f5e61-223">**Default**: Production</span></span>  
<span data-ttu-id="f5e61-224">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="f5e61-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="f5e61-225">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="f5e61-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="f5e61-226">ShutdownTimeout</span></span>

<span data-ttu-id="f5e61-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="f5e61-228">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="f5e61-228">The default value is five seconds.</span></span>  <span data-ttu-id="f5e61-229">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="f5e61-230">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="f5e61-231">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="f5e61-232">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="f5e61-233">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="f5e61-234">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="f5e61-235">**キー**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="f5e61-235">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="f5e61-236">**型**: *int*</span><span class="sxs-lookup"><span data-stu-id="f5e61-236">**Type**: *int*</span></span>  
<span data-ttu-id="f5e61-237">**既定**: 5 秒 **環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="f5e61-237">**Default**: 5 seconds **Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="f5e61-238">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-238">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="f5e61-239">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-239">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="f5e61-240">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="f5e61-240">Settings for web apps</span></span>

<span data-ttu-id="f5e61-241">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-241">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="f5e61-242">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-242">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="f5e61-243">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-243">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="f5e61-244">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="f5e61-244">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="f5e61-245">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="f5e61-245">CaptureStartupErrors</span></span>

<span data-ttu-id="f5e61-246">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-246">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="f5e61-247">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-247">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="f5e61-248">**キー**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="f5e61-248">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="f5e61-249">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="f5e61-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f5e61-250">**既定値**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-250">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="f5e61-251">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="f5e61-251">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="f5e61-252">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-252">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="f5e61-253">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="f5e61-253">DetailedErrors</span></span>

<span data-ttu-id="f5e61-254">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-254">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="f5e61-255">**キー**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="f5e61-255">**Key**: detailedErrors</span></span>  
<span data-ttu-id="f5e61-256">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="f5e61-256">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f5e61-257">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="f5e61-257">**Default**: false</span></span>  
<span data-ttu-id="f5e61-258">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="f5e61-258">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="f5e61-259">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-259">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="f5e61-260">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="f5e61-260">HostingStartupAssemblies</span></span>

<span data-ttu-id="f5e61-261">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="f5e61-261">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="f5e61-262">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-262">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="f5e61-263">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-263">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="f5e61-264">**キー**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="f5e61-264">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="f5e61-265">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-265">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-266">**既定値**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="f5e61-266">**Default**: Empty string</span></span>  
<span data-ttu-id="f5e61-267">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="f5e61-267">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="f5e61-268">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-268">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="f5e61-269">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="f5e61-269">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="f5e61-270">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="f5e61-270">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="f5e61-271">**キー**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="f5e61-271">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="f5e61-272">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-272">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-273">**既定値**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="f5e61-273">**Default**: Empty string</span></span>  
<span data-ttu-id="f5e61-274">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="f5e61-274">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="f5e61-275">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-275">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="f5e61-276">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="f5e61-276">HTTPS_Port</span></span>

<span data-ttu-id="f5e61-277">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="f5e61-277">The HTTPS redirect port.</span></span> <span data-ttu-id="f5e61-278">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-278">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f5e61-279">**キー**: https_port</span><span class="sxs-lookup"><span data-stu-id="f5e61-279">**Key**: https_port</span></span>  
<span data-ttu-id="f5e61-280">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-280">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-281">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-281">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="f5e61-282">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="f5e61-282">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="f5e61-283">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-283">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="f5e61-284">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="f5e61-284">PreferHostingUrls</span></span>

<span data-ttu-id="f5e61-285">`IServer` 実装で構成されているものではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-285">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="f5e61-286">**キー**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="f5e61-286">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="f5e61-287">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="f5e61-287">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f5e61-288">**既定値**: true</span><span class="sxs-lookup"><span data-stu-id="f5e61-288">**Default**: true</span></span>  
<span data-ttu-id="f5e61-289">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="f5e61-289">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="f5e61-290">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-290">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="f5e61-291">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="f5e61-291">PreventHostingStartup</span></span>

<span data-ttu-id="f5e61-292">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-292">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="f5e61-293">詳細については、<xref:fundamentals/configuration/platform-specific-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-293">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="f5e61-294">**キー**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="f5e61-294">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="f5e61-295">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="f5e61-295">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="f5e61-296">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="f5e61-296">**Default**: false</span></span>  
<span data-ttu-id="f5e61-297">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="f5e61-297">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="f5e61-298">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-298">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="f5e61-299">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="f5e61-299">StartupAssembly</span></span>

<span data-ttu-id="f5e61-300">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="f5e61-300">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="f5e61-301">**キー**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="f5e61-301">**Key**: startupAssembly</span></span>  
<span data-ttu-id="f5e61-302">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-302">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-303">**既定値**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="f5e61-303">**Default**: The app's assembly</span></span>  
<span data-ttu-id="f5e61-304">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="f5e61-304">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="f5e61-305">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-305">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="f5e61-306">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-306">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="f5e61-307">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-307">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="f5e61-308">URL</span><span class="sxs-lookup"><span data-stu-id="f5e61-308">URLs</span></span>

<span data-ttu-id="f5e61-309">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="f5e61-309">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="f5e61-310">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-310">For example, `http://localhost:123`.</span></span> <span data-ttu-id="f5e61-311">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-311">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="f5e61-312">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-312">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="f5e61-313">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-313">Supported formats vary among servers.</span></span>

<span data-ttu-id="f5e61-314">**キー**: urls</span><span class="sxs-lookup"><span data-stu-id="f5e61-314">**Key**: urls</span></span>  
<span data-ttu-id="f5e61-315">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-315">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-316">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="f5e61-316">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="f5e61-317">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="f5e61-317">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="f5e61-318">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-318">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="f5e61-319">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-319">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="f5e61-320">詳細については、<xref:fundamentals/servers/kestrel#endpoint-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-320">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="f5e61-321">WebRoot</span><span class="sxs-lookup"><span data-stu-id="f5e61-321">WebRoot</span></span>

<span data-ttu-id="f5e61-322">アプリの静的資産への相対パス。</span><span class="sxs-lookup"><span data-stu-id="f5e61-322">The relative path to the app's static assets.</span></span>

<span data-ttu-id="f5e61-323">**キー**: webroot</span><span class="sxs-lookup"><span data-stu-id="f5e61-323">**Key**: webroot</span></span>  
<span data-ttu-id="f5e61-324">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-324">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-325">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="f5e61-325">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="f5e61-326">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-326">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="f5e61-327">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-327">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="f5e61-328">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="f5e61-328">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="f5e61-329">この値を設定するには、環境変数を使用するか、または `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-329">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="f5e61-330">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="f5e61-330">For more information, see:</span></span>

* [<span data-ttu-id="f5e61-331">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="f5e61-331">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="f5e61-332">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="f5e61-332">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="f5e61-333">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="f5e61-333">Manage the host lifetime</span></span>

<span data-ttu-id="f5e61-334">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="f5e61-334">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="f5e61-335">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-335">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="f5e61-336">実行</span><span class="sxs-lookup"><span data-stu-id="f5e61-336">Run</span></span>

<span data-ttu-id="f5e61-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-337"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="f5e61-338">RunAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-338">RunAsync</span></span>

<span data-ttu-id="f5e61-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="f5e61-340">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-340">RunConsoleAsync</span></span>

<span data-ttu-id="f5e61-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、Ctrl + C/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-341"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="f5e61-342">[開始]</span><span class="sxs-lookup"><span data-stu-id="f5e61-342">Start</span></span>

<span data-ttu-id="f5e61-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-343"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="f5e61-344">StartAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-344">StartAsync</span></span>

<span data-ttu-id="f5e61-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-345"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="f5e61-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-346"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="f5e61-347">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-347">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="f5e61-348">StopAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-348">StopAsync</span></span>

<span data-ttu-id="f5e61-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-349"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="f5e61-350">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="f5e61-350">WaitForShutdown</span></span>

<span data-ttu-id="f5e61-351">Ctrl + C/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via Ctrl+C/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="f5e61-352">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-352">WaitForShutdownAsync</span></span>

<span data-ttu-id="f5e61-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="f5e61-354">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="f5e61-354">External control</span></span>

<span data-ttu-id="f5e61-355">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-355">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5e61-356">ASP.NET Core アプリはホストを構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-356">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="f5e61-357">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-357">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="f5e61-358">この記事では、HTTP 要求を処理しないアプリに使用される、ASP.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について説明します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-358">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="f5e61-359">汎用ホストの目的は、Web ホスト API から HTTP パイプラインを切り離して、多様なホスト シナリオを有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-359">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="f5e61-360">メッセージング、バックグラウンド タスク、汎用ホストに基づくその他の HTTP ワークロードに対して、構成、依存関係の挿入 (DI)、ログなどの横断的機能によるメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-360">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="f5e61-361">汎用ホストは ASP.NET Core 2.1 の新機能であり、Web ホスティングのシナリオには適していません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-361">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="f5e61-362">Web ホスティングのシナリオの場合は、[Web ホスト](xref:fundamentals/host/web-host)を使ってください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-362">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="f5e61-363">汎用ホストは将来のリリースで Web ホストに代わるものであり、HTTP と非 HTTP 両方のシナリオのプライマリ ホスト API として機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-363">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="f5e61-364">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f5e61-364">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f5e61-365">[Visual Studio Code](https://code.visualstudio.com/) でサンプル アプリを実行するときは、"*外部ターミナルまたは統合ターミナル*" を使います。</span><span class="sxs-lookup"><span data-stu-id="f5e61-365">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="f5e61-366">`internalConsole` ではサンプルを実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-366">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="f5e61-367">Visual Studio Code でコンソールを設定するには:</span><span class="sxs-lookup"><span data-stu-id="f5e61-367">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="f5e61-368">*.vscode/launch.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-368">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="f5e61-369">**.NET Core Launch (console)** の構成で、**console** エントリを探します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-369">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="f5e61-370">値を `externalTerminal` または `integratedTerminal` に設定します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-370">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="f5e61-371">はじめに</span><span class="sxs-lookup"><span data-stu-id="f5e61-371">Introduction</span></span>

<span data-ttu-id="f5e61-372">汎用ホスト ライブラリは、<xref:Microsoft.Extensions.Hosting> 名前空間で使用でき、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) パッケージで提供されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-372">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="f5e61-373">`Microsoft.Extensions.Hosting` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます (ASP.NET Core 2.1 以降)。</span><span class="sxs-lookup"><span data-stu-id="f5e61-373">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="f5e61-374"><xref:Microsoft.Extensions.Hosting.IHostedService> がコード実行のエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-374"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="f5e61-375"><xref:Microsoft.Extensions.Hosting.IHostedService> の各実装は、[ConfigureServices でのサービス登録](#configureservices)の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-375">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="f5e61-376">ホストが開始するときは <xref:Microsoft.Extensions.Hosting.IHostedService> ごとに <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> が呼び出され、ホストが正常に終了するときは登録と逆の順序で <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-376"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="f5e61-377">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="f5e61-377">Set up a host</span></span>

<span data-ttu-id="f5e61-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> は、ライブラリとアプリがホストの初期化、ビルド、実行に使用するメイン コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-378"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="f5e61-379">オプション</span><span class="sxs-lookup"><span data-stu-id="f5e61-379">Options</span></span>

<span data-ttu-id="f5e61-380"><xref:Microsoft.Extensions.Hosting.IHost> の <xref:Microsoft.Extensions.Hosting.HostOptions> 構成オプションです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-380"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="f5e61-381">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="f5e61-381">Shutdown timeout</span></span>

<span data-ttu-id="f5e61-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> によって <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-382"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="f5e61-383">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="f5e61-383">The default value is five seconds.</span></span>

<span data-ttu-id="f5e61-384">次に示す `Program.Main` のオプション構成では、既定の 5 秒のシャットダウン タイムアウトが 20 秒に延長されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-384">The following option configuration in `Program.Main` increases the default five second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="f5e61-385">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="f5e61-385">Default services</span></span>

<span data-ttu-id="f5e61-386">次のサービスは、ホストの初期化中に登録されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-386">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="f5e61-387">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="f5e61-387">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="f5e61-388">[構成](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="f5e61-388">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="f5e61-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="f5e61-389"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="f5e61-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="f5e61-390"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="f5e61-391">[オプション](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="f5e61-391">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="f5e61-392">[ログ](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="f5e61-392">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="f5e61-393">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="f5e61-393">Host configuration</span></span>

<span data-ttu-id="f5e61-394">ホストの構成は、次のようにして作成されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-394">Host configuration is created by:</span></span>

* <span data-ttu-id="f5e61-395"><xref:Microsoft.Extensions.Hosting.IHostBuilder> で拡張メソッドを呼び出して、[コンテンツ ルート](#content-root)と[環境](#environment)を設定する。</span><span class="sxs-lookup"><span data-stu-id="f5e61-395">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="f5e61-396"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> の構成プロバイダーから構成を読み取る。</span><span class="sxs-lookup"><span data-stu-id="f5e61-396">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="f5e61-397">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="f5e61-397">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="f5e61-398">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="f5e61-398">Application key (name)</span></span>

<span data-ttu-id="f5e61-399">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-399">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="f5e61-400">値を明示的に設定するには、[HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-400">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="f5e61-401">**キー**: applicationName</span><span class="sxs-lookup"><span data-stu-id="f5e61-401">**Key**: applicationName</span></span>  
<span data-ttu-id="f5e61-402">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-402">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-403">**既定値**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="f5e61-403">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="f5e61-404">**次を使用して設定**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="f5e61-404">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="f5e61-405">**環境変数**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="f5e61-405">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="f5e61-406">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="f5e61-406">Content root</span></span>

<span data-ttu-id="f5e61-407">この設定では、ホストがコンテンツ ファイルの検索を開始する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-407">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="f5e61-408">**キー**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="f5e61-408">**Key**: contentRoot</span></span>  
<span data-ttu-id="f5e61-409">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-409">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-410">**既定値**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-410">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="f5e61-411">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="f5e61-411">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="f5e61-412">**環境変数**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="f5e61-412">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="f5e61-413">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-413">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="f5e61-414">詳細については、[基礎: コンテンツ ルート](xref:fundamentals/index#content-root)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-414">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="f5e61-415">環境</span><span class="sxs-lookup"><span data-stu-id="f5e61-415">Environment</span></span>

<span data-ttu-id="f5e61-416">アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-416">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f5e61-417">**キー**: 環境</span><span class="sxs-lookup"><span data-stu-id="f5e61-417">**Key**: environment</span></span>  
<span data-ttu-id="f5e61-418">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="f5e61-418">**Type**: *string*</span></span>  
<span data-ttu-id="f5e61-419">**既定値**:実稼働</span><span class="sxs-lookup"><span data-stu-id="f5e61-419">**Default**: Production</span></span>  
<span data-ttu-id="f5e61-420">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="f5e61-420">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="f5e61-421">**環境変数**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="f5e61-421">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="f5e61-422">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-422">The environment can be set to any value.</span></span> <span data-ttu-id="f5e61-423">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-423">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="f5e61-424">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-424">Values aren't case sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="f5e61-425">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="f5e61-425">ConfigureHostConfiguration</span></span>

<span data-ttu-id="f5e61-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、ホストの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-426"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="f5e61-427">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> をアプリのビルド プロセスで使用するための初期化に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-427">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="f5e61-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="f5e61-429">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-429">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="f5e61-430">既定ではプロバイダーが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="f5e61-430">No providers are included by default.</span></span> <span data-ttu-id="f5e61-431">次のような、アプリが <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> で必要とする構成プロバイダーを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-431">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="f5e61-432">ファイルの構成 (*hostsettings.json* ファイルからなど)。</span><span class="sxs-lookup"><span data-stu-id="f5e61-432">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="f5e61-433">環境変数の構成。</span><span class="sxs-lookup"><span data-stu-id="f5e61-433">Environment variable configuration.</span></span>
* <span data-ttu-id="f5e61-434">コマンドライン引数の構成。</span><span class="sxs-lookup"><span data-stu-id="f5e61-434">Command-line argument configuration.</span></span>
* <span data-ttu-id="f5e61-435">その他に必要なすべての構成プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="f5e61-435">Any other required configuration providers.</span></span>

<span data-ttu-id="f5e61-436">ホストのファイル構成は、いずれかの[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)に対する呼び出しの前に `SetBasePath` のアプリのベース パスを指定することで有効になります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-436">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="f5e61-437">サンプル アプリは、JSON ファイル (*hostsettings.json*) を使用し、<xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> を呼び出して、ファイルのホスト構成設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-437">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="f5e61-438">ホストの[環境変数の構成](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を追加するには、ホスト ビルダーで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-438">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="f5e61-439">`AddEnvironmentVariables` は、オプションのユーザー定義プレフィックスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-439">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="f5e61-440">サンプル アプリは、`PREFIX_` のプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-440">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="f5e61-441">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-441">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="f5e61-442">サンプル アプリのホストが構成されると、`PREFIX_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-442">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="f5e61-443">開発中に [Visual Studio](https://visualstudio.microsoft.com) を使用している、または `dotnet run` を使用してアプリを実行している場合は、環境変数を *Properties/launchSettings.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-443">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="f5e61-444">[Visual Studio Code](https://code.visualstudio.com/) では、開発中に環境変数を *.vscode/launch.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-444">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="f5e61-445">詳細については、<xref:fundamentals/environments> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-445">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f5e61-446">[コマンドラインの構成](xref:fundamentals/configuration/index#command-line-configuration-provider)は、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>を呼び出すことで追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-446">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="f5e61-447">コマンドラインの構成は、コマンドライン引数を許可して以前の構成プロバイダーから提供された構成をオーバーライドするために最後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-447">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="f5e61-448">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e61-448">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="f5e61-449">追加の構成は、[applicationName](#application-key-name) と [contentRoot](#content-root) キーで指定することができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-449">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="f5e61-450"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を使う `HostBuilder` の構成の例:</span><span class="sxs-lookup"><span data-stu-id="f5e61-450">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="f5e61-451">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="f5e61-451">ConfigureAppConfiguration</span></span>

<span data-ttu-id="f5e61-452">アプリの構成は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-452">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="f5e61-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、アプリの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-453"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="f5e61-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-454"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="f5e61-455">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-455">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="f5e61-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> によって作成された構成は、以降の操作に対する [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) において、および <xref:Microsoft.Extensions.Hosting.IHost.Services*>サービス内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-456">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="f5e61-457">アプリの構成は、[ConfigureHostConfiguration](#configurehostconfiguration) によって提供されたホストの構成を自動的に受信します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-457">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="f5e61-458"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を使うアプリ構成の例:</span><span class="sxs-lookup"><span data-stu-id="f5e61-458">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="f5e61-459">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e61-459">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="f5e61-460">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e61-460">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="f5e61-461">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e61-461">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="f5e61-462">設定ファイルを出力ディレクトリに移動するには、プロジェクト ファイルの設定ファイルを [MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) として指定します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-462">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="f5e61-463">サンプル アプリはその JSON アプリ設定ファイルと、次の `<Content>` 項目を含む *hostsettings.json* を移動します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-463">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="f5e61-464"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> や <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> などの構成拡張メソッドでは、[Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) や [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) など、追加の NuGet パッケージを必要とします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-464">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="f5e61-465">アプリが [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) を使用しない限り、中心となる [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) パッケージに加えて、これらのパッケージがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-465">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="f5e61-466">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-466">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="f5e61-467">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="f5e61-467">ConfigureServices</span></span>

<span data-ttu-id="f5e61-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> は、アプリの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーにサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-468"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f5e61-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-469"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="f5e61-470">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-470">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="f5e61-471">詳細については、<xref:fundamentals/host/hosted-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-471">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="f5e61-472">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)では、`AddHostedService` 拡張メソッドを使って、有効期間イベント `LifetimeEventsHostedService` と時刻指定付きバックグラウンド タスク `TimedHostedService` のサービスをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-472">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="f5e61-473">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="f5e61-473">ConfigureLogging</span></span>

<span data-ttu-id="f5e61-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> は、指定された <xref:Microsoft.Extensions.Logging.ILoggingBuilder> を構成するためのデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-474"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="f5e61-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-475"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="f5e61-476">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="f5e61-476">UseConsoleLifetime</span></span>

<span data-ttu-id="f5e61-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> では、Ctrl + C/SIGINT または SIGTERM がリッスンされ、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> が呼び出されてシャットダウン プロセスが開始されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for Ctrl+C/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="f5e61-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> は、[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-478"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="f5e61-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の有効期間の実装として事前に登録されています。</span><span class="sxs-lookup"><span data-stu-id="f5e61-479">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="f5e61-480">最後に登録された有効期間が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-480">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="f5e61-481">コンテナーの構成</span><span class="sxs-lookup"><span data-stu-id="f5e61-481">Container configuration</span></span>

<span data-ttu-id="f5e61-482">他のコンテナーの接続をサポートするため、ホストは <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-482">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="f5e61-483">ファクトリの提供は、DI コンテナーの登録の一部ではなく、具象 DI コンテナーの作成に使用されるホストの組み込みです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-483">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="f5e61-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) は、アプリのサービス プロバイダーの作成に使われる既定のファクトリをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-484">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="f5e61-485">カスタム コンテナーの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> メソッドによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-485">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="f5e61-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> は、基盤のホスト API を基にしてコンテナーを構成するための、厳密に型指定されたエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-486"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="f5e61-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-487"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="f5e61-488">アプリのサービス コンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-488">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="f5e61-489">サービス コンテナーのファクトリを提供します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-489">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="f5e61-490">ファクトリを使って、アプリのカスタム サービス コンテナーを構成します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-490">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="f5e61-491">機能拡張</span><span class="sxs-lookup"><span data-stu-id="f5e61-491">Extensibility</span></span>

<span data-ttu-id="f5e61-492">ホスト拡張機能は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> 上の拡張メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-492">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="f5e61-493">次の例では、拡張メソッドが <xref:fundamentals/host/hosted-services> で示される [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) の例を使って <xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装を拡張する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f5e61-493">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="f5e61-494">アプリは `UseHostedService` 拡張メソッドを確率して `T` に渡されたホステッド サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-494">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="f5e61-495">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="f5e61-495">Manage the host</span></span>

<span data-ttu-id="f5e61-496"><xref:Microsoft.Extensions.Hosting.IHost> の実装は、サービス コンテナーに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の実装の開始と停止を行います。</span><span class="sxs-lookup"><span data-stu-id="f5e61-496">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="f5e61-497">実行</span><span class="sxs-lookup"><span data-stu-id="f5e61-497">Run</span></span>

<span data-ttu-id="f5e61-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-498"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="f5e61-499">RunAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-499">RunAsync</span></span>

<span data-ttu-id="f5e61-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="f5e61-501">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-501">RunConsoleAsync</span></span>

<span data-ttu-id="f5e61-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、Ctrl + C/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-502"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for Ctrl+C/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="f5e61-503">Start と StopAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-503">Start and StopAsync</span></span>

<span data-ttu-id="f5e61-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-504"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="f5e61-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-505"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="f5e61-506">StartAsync と StopAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-506">StartAsync and StopAsync</span></span>

<span data-ttu-id="f5e61-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> がアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-507"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="f5e61-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> がアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-508"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="f5e61-509">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="f5e61-509">WaitForShutdown</span></span>

<span data-ttu-id="f5e61-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は、`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (Ctrl + C/SIGINT または SIGTERM をリッスンする) などの <xref:Microsoft.Extensions.Hosting.IHostLifetime> を介してトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-510"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for Ctrl+C/SIGINT or SIGTERM).</span></span> <span data-ttu-id="f5e61-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-511"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="f5e61-512">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="f5e61-512">WaitForShutdownAsync</span></span>

<span data-ttu-id="f5e61-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="f5e61-514">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="f5e61-514">External control</span></span>

<span data-ttu-id="f5e61-515">ホストの外部コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-515">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="f5e61-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-516"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="f5e61-517">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-517">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="f5e61-518">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="f5e61-518">IHostingEnvironment interface</span></span>

<span data-ttu-id="f5e61-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> は、アプリのホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-519"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="f5e61-520">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> を取得します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-520">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="f5e61-521">詳細については、<xref:fundamentals/environments> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f5e61-521">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="f5e61-522">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="f5e61-522">IApplicationLifetime interface</span></span>

<span data-ttu-id="f5e61-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> は、正常なシャットダウンの要求など、起動後とシャットダウンのアクティビティに対応します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-523"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="f5e61-524">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する <xref:System.Action> メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="f5e61-524">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="f5e61-525">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="f5e61-525">Cancellation Token</span></span> | <span data-ttu-id="f5e61-526">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="f5e61-526">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="f5e61-527">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="f5e61-527">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="f5e61-528">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="f5e61-528">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="f5e61-529">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f5e61-529">All requests should be processed.</span></span> <span data-ttu-id="f5e61-530">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-530">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="f5e61-531">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="f5e61-531">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="f5e61-532">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="f5e61-532">Requests may still be processing.</span></span> <span data-ttu-id="f5e61-533">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="f5e61-533">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="f5e61-534">コンストラクターは任意のクラスに <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-534">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="f5e61-535">[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)は、`LifetimeEventsHostedService` クラス (<xref:Microsoft.Extensions.Hosting.IHostedService> の実装) へのコンストラクターの挿入を使って、イベントを登録します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-535">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="f5e61-536">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e61-536">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="f5e61-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="f5e61-537"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="f5e61-538">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="f5e61-538">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f5e61-539">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f5e61-539">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
