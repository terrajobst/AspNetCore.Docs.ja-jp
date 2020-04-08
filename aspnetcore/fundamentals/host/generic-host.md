---
title: .NET での汎用ホスト
author: rick-anderson
description: アプリの起動と有効期間の管理を行う .NET Core 汎用ホストについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: 454216cec72048217ede412f8ff6d4261f7353b1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417630"
---
# <a name="net-generic-host"></a><span data-ttu-id="9fd89-103">.NET での汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="9fd89-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9fd89-104">この記事では .NET Core 汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について紹介し、その使用方法に関するガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-104">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="9fd89-105">ホストとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="9fd89-105">What's a host?</span></span>

<span data-ttu-id="9fd89-106">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="9fd89-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9fd89-107">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="9fd89-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="9fd89-108">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9fd89-108">Logging</span></span>
* <span data-ttu-id="9fd89-109">構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-109">Configuration</span></span>
* <span data-ttu-id="9fd89-110">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="9fd89-110">`IHostedService` implementations</span></span>

<span data-ttu-id="9fd89-111">ホストを開始すると、DI コンテナー内で検出された <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装に対して `IHostedService.StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="9fd89-112">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9fd89-113">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9fd89-114">ASP.NET Core の 3.0 より前のバージョンでは、[Web ホスト](xref:fundamentals/host/web-host)が HTTP ワークロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-114">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="9fd89-115">Web ホストは Web アプリの推奨ホストではなくなり、下位互換性用のみに引き続き利用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-115">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9fd89-116">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="9fd89-116">Set up a host</span></span>

<span data-ttu-id="9fd89-117">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-117">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9fd89-118">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="9fd89-118">The `Main` method:</span></span>

* <span data-ttu-id="9fd89-119">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-119">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9fd89-120">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-120">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9fd89-121">HTTP 以外のワークロード用の *Program.cs* コードを次に示します。単一の `IHostedService` 実装が DI コンテナーに追加されています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-121">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="9fd89-122">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9fd89-123">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-123">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9fd89-124">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-124">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9fd89-125">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-125">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9fd89-126">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="9fd89-126">Default builder settings</span></span>

<span data-ttu-id="9fd89-127"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="9fd89-127">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9fd89-128">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-128">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9fd89-129">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-129">Loads host configuration from:</span></span>
  * <span data-ttu-id="9fd89-130">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-130">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9fd89-131">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-131">Command-line arguments.</span></span>
* <span data-ttu-id="9fd89-132">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-132">Loads app configuration from:</span></span>
  * <span data-ttu-id="9fd89-133">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="9fd89-133">*appsettings.json*.</span></span>
  * <span data-ttu-id="9fd89-134">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="9fd89-134">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9fd89-135">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9fd89-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9fd89-136">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-136">Environment variables.</span></span>
  * <span data-ttu-id="9fd89-137">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-137">Command-line arguments.</span></span>
* <span data-ttu-id="9fd89-138">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-138">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9fd89-139">コンソール</span><span class="sxs-lookup"><span data-stu-id="9fd89-139">Console</span></span>
  * <span data-ttu-id="9fd89-140">デバッグ</span><span class="sxs-lookup"><span data-stu-id="9fd89-140">Debug</span></span>
  * <span data-ttu-id="9fd89-141">EventSource</span><span class="sxs-lookup"><span data-stu-id="9fd89-141">EventSource</span></span>
  * <span data-ttu-id="9fd89-142">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="9fd89-142">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9fd89-143">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-143">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9fd89-144">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="9fd89-144">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9fd89-145">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-145">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9fd89-146">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-146">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9fd89-147">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-147">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9fd89-148">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-148">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9fd89-149">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-149">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9fd89-150">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-150">Enables IIS integration.</span></span> <span data-ttu-id="9fd89-151">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-151">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9fd89-152">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-152">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9fd89-153">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="9fd89-153">Framework-provided services</span></span>

<span data-ttu-id="9fd89-154">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-154">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9fd89-155">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-155">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9fd89-156">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-156">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9fd89-157">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9fd89-157">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9fd89-158">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-158">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9fd89-159">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-159">IHostApplicationLifetime</span></span>

<span data-ttu-id="9fd89-160">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-160">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9fd89-161">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-161">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9fd89-162">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-162">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9fd89-163">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-163">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9fd89-164">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-164">IHostLifetime</span></span>

<span data-ttu-id="9fd89-165"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-165">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9fd89-166">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-166">The last implementation registered is used.</span></span>

<span data-ttu-id="9fd89-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-167">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9fd89-168">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9fd89-168">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9fd89-169"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-169">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9fd89-170">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-170">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9fd89-171">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9fd89-171">IHostEnvironment</span></span>

<span data-ttu-id="9fd89-172">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-172">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9fd89-173">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9fd89-173">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9fd89-174">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9fd89-174">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9fd89-175">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9fd89-175">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="9fd89-176">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-176">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9fd89-177">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-177">Host configuration</span></span>

<span data-ttu-id="9fd89-178">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-178">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9fd89-179">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-179">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9fd89-180">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-180">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9fd89-181">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-181">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9fd89-182">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-182">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9fd89-183">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-183">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9fd89-184">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-184">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9fd89-185">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-185">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9fd89-186">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-186">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9fd89-187">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-187">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9fd89-188">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-188">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9fd89-189">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-189">App configuration</span></span>

<span data-ttu-id="9fd89-190">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-190">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9fd89-191">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-191">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9fd89-192">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-192">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9fd89-193">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-193">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9fd89-194">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-194">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9fd89-195">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-195">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9fd89-196">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="9fd89-196">Settings for all app types</span></span>

<span data-ttu-id="9fd89-197">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-197">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9fd89-198">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-198">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9fd89-199">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9fd89-199">ApplicationName</span></span>

<span data-ttu-id="9fd89-200">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-200">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9fd89-201">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9fd89-201">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9fd89-202">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-202">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-203">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="9fd89-203">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9fd89-204">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9fd89-204">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9fd89-205">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-205">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="9fd89-206">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9fd89-206">ContentRootPath</span></span>

<span data-ttu-id="9fd89-207">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-207">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9fd89-208">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-208">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9fd89-209">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9fd89-209">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9fd89-210">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-210">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-211">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="9fd89-211">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9fd89-212">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-212">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9fd89-213">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-213">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9fd89-214">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="9fd89-214">For more information, see:</span></span>

* [<span data-ttu-id="9fd89-215">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="9fd89-215">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9fd89-216">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9fd89-216">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9fd89-217">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9fd89-217">EnvironmentName</span></span>

<span data-ttu-id="9fd89-218">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-218">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9fd89-219">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-219">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9fd89-220">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-220">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9fd89-221">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9fd89-221">**Key**: `environment`</span></span>  
<span data-ttu-id="9fd89-222">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-222">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-223">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9fd89-223">**Default**: `Production`</span></span>  
<span data-ttu-id="9fd89-224">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-224">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9fd89-225">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-225">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9fd89-226">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9fd89-226">ShutdownTimeout</span></span>

<span data-ttu-id="9fd89-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-227">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9fd89-228">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-228">The default value is five seconds.</span></span>  <span data-ttu-id="9fd89-229">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-229">During the timeout period, the host:</span></span>

* <span data-ttu-id="9fd89-230">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-230">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9fd89-231">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-231">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9fd89-232">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-232">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9fd89-233">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-233">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9fd89-234">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-234">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9fd89-235">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9fd89-235">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9fd89-236">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="9fd89-236">**Type**: `int`</span></span>  
<span data-ttu-id="9fd89-237">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="9fd89-237">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9fd89-238">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-238">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9fd89-239">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-239">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9fd89-240">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-240">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="9fd89-241">変更時にアプリ構成の再度読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="9fd89-241">Disable app configuration reload on change</span></span>

<span data-ttu-id="9fd89-242">[既定](xref:fundamentals/configuration/index#default)では、*appsettings.json* と *appsettings.{Environment}.json* は、ファイルの変更時に再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-242">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="9fd89-243">ASP.NET Core 5.0 Preview 3 以降でこの再度読み込み動作を無効にするには、`hostBuilder:reloadConfigOnChange` キーを `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-243">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="9fd89-244">**キー**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9fd89-244">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9fd89-245">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-245">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-246">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="9fd89-246">**Default**: `true`</span></span>  
<span data-ttu-id="9fd89-247">**コマンドライン引数**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9fd89-247">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="9fd89-248">**環境変数**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="9fd89-248">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="9fd89-249">コロン (`:`) の区切り記号は、すべてのプラットフォームの環境変数階層キーには対応していません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-249">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="9fd89-250">詳細については、「[環境変数](xref:fundamentals/configuration/index#environment-variables)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-250">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="9fd89-251">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="9fd89-251">Settings for web apps</span></span>

<span data-ttu-id="9fd89-252">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-252">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9fd89-253">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-253">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9fd89-254">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-254">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9fd89-255">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-255">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9fd89-256">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9fd89-256">CaptureStartupErrors</span></span>

<span data-ttu-id="9fd89-257">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-257">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9fd89-258">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-258">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9fd89-259">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9fd89-259">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9fd89-260">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-260">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-261">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-261">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9fd89-262">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-262">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9fd89-263">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-263">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9fd89-264">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9fd89-264">DetailedErrors</span></span>

<span data-ttu-id="9fd89-265">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-265">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9fd89-266">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9fd89-266">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9fd89-267">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-267">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-268">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="9fd89-268">**Default**: `false`</span></span>  
<span data-ttu-id="9fd89-269">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-269">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9fd89-270">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9fd89-271">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9fd89-271">HostingStartupAssemblies</span></span>

<span data-ttu-id="9fd89-272">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="9fd89-272">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9fd89-273">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-273">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9fd89-274">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-274">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9fd89-275">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9fd89-275">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9fd89-276">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-276">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-277">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="9fd89-277">**Default**: Empty string</span></span>  
<span data-ttu-id="9fd89-278">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9fd89-278">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9fd89-279">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-279">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9fd89-280">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9fd89-280">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9fd89-281">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="9fd89-281">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9fd89-282">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9fd89-282">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9fd89-283">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-283">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-284">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="9fd89-284">**Default**: Empty string</span></span>  
<span data-ttu-id="9fd89-285">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9fd89-285">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9fd89-286">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-286">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9fd89-287">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9fd89-287">HTTPS_Port</span></span>

<span data-ttu-id="9fd89-288">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="9fd89-288">The HTTPS redirect port.</span></span> <span data-ttu-id="9fd89-289">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-289">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9fd89-290">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9fd89-290">**Key**: `https_port`</span></span>  
<span data-ttu-id="9fd89-291">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-291">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-292">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-292">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9fd89-293">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-293">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9fd89-294">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-294">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9fd89-295">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="9fd89-295">PreferHostingUrls</span></span>

<span data-ttu-id="9fd89-296">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-296">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9fd89-297">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9fd89-297">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9fd89-298">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-298">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-299">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="9fd89-299">**Default**: `true`</span></span>  
<span data-ttu-id="9fd89-300">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-300">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9fd89-301">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-301">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9fd89-302">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="9fd89-302">PreventHostingStartup</span></span>

<span data-ttu-id="9fd89-303">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-303">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9fd89-304">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-304">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9fd89-305">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9fd89-305">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9fd89-306">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-306">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-307">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="9fd89-307">**Default**: `false`</span></span>  
<span data-ttu-id="9fd89-308">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9fd89-308">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9fd89-309">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-309">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9fd89-310">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9fd89-310">StartupAssembly</span></span>

<span data-ttu-id="9fd89-311">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="9fd89-311">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9fd89-312">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9fd89-312">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9fd89-313">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-313">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-314">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="9fd89-314">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9fd89-315">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9fd89-315">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9fd89-316">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-316">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9fd89-317">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-317">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9fd89-318">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-318">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9fd89-319">URL</span><span class="sxs-lookup"><span data-stu-id="9fd89-319">URLs</span></span>

<span data-ttu-id="9fd89-320">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="9fd89-320">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9fd89-321">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-321">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9fd89-322">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-322">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9fd89-323">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-323">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9fd89-324">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-324">Supported formats vary among servers.</span></span>

<span data-ttu-id="9fd89-325">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9fd89-325">**Key**: `urls`</span></span>  
<span data-ttu-id="9fd89-326">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-326">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-327">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9fd89-327">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9fd89-328">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-328">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9fd89-329">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-329">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9fd89-330">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-330">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9fd89-331">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-331">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9fd89-332">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9fd89-332">WebRoot</span></span>

<span data-ttu-id="9fd89-333">アプリの静的資産への相対パス。</span><span class="sxs-lookup"><span data-stu-id="9fd89-333">The relative path to the app's static assets.</span></span>

<span data-ttu-id="9fd89-334">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9fd89-334">**Key**: `webroot`</span></span>  
<span data-ttu-id="9fd89-335">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-335">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-336">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-336">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9fd89-337">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-337">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="9fd89-338">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-338">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="9fd89-339">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-339">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9fd89-340">この値を設定するには、環境変数を使用するか、または `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-340">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9fd89-341">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="9fd89-341">For more information, see:</span></span>

* [<span data-ttu-id="9fd89-342">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="9fd89-342">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9fd89-343">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9fd89-343">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9fd89-344">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="9fd89-344">Manage the host lifetime</span></span>

<span data-ttu-id="9fd89-345">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="9fd89-345">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9fd89-346">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-346">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9fd89-347">実行</span><span class="sxs-lookup"><span data-stu-id="9fd89-347">Run</span></span>

<span data-ttu-id="9fd89-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-348"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9fd89-349">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-349">RunAsync</span></span>

<span data-ttu-id="9fd89-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9fd89-351">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-351">RunConsoleAsync</span></span>

<span data-ttu-id="9fd89-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-352"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9fd89-353">[開始]</span><span class="sxs-lookup"><span data-stu-id="9fd89-353">Start</span></span>

<span data-ttu-id="9fd89-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-354"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9fd89-355">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-355">StartAsync</span></span>

<span data-ttu-id="9fd89-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-356"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9fd89-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-357"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9fd89-358">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-358">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9fd89-359">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-359">StopAsync</span></span>

<span data-ttu-id="9fd89-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-360"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9fd89-361">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9fd89-361">WaitForShutdown</span></span>

<span data-ttu-id="9fd89-362"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9fd89-363">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-363">WaitForShutdownAsync</span></span>

<span data-ttu-id="9fd89-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9fd89-365">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="9fd89-365">External control</span></span>

<span data-ttu-id="9fd89-366">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-366">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="9fd89-367">この記事では .NET Core 汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について紹介し、その使用方法に関するガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-367">This article introduces the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) and provides guidance on how to use it.</span></span>

## <a name="whats-a-host"></a><span data-ttu-id="9fd89-368">ホストとは何ですか?</span><span class="sxs-lookup"><span data-stu-id="9fd89-368">What's a host?</span></span>

<span data-ttu-id="9fd89-369">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="9fd89-369">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="9fd89-370">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="9fd89-370">Dependency injection (DI)</span></span>
* <span data-ttu-id="9fd89-371">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9fd89-371">Logging</span></span>
* <span data-ttu-id="9fd89-372">構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-372">Configuration</span></span>
* <span data-ttu-id="9fd89-373">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="9fd89-373">`IHostedService` implementations</span></span>

<span data-ttu-id="9fd89-374">ホストを開始すると、DI コンテナー内で検出された <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装に対して `IHostedService.StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-374">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="9fd89-375">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-375">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="9fd89-376">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-376">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9fd89-377">ASP.NET Core の 3.0 より前のバージョンでは、[Web ホスト](xref:fundamentals/host/web-host)が HTTP ワークロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-377">In versions of ASP.NET Core earlier than 3.0, the [Web Host](xref:fundamentals/host/web-host) is used for HTTP workloads.</span></span> <span data-ttu-id="9fd89-378">Web ホストは Web アプリの推奨ホストではなくなり、下位互換性用のみに引き続き利用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-378">The Web Host is no longer recommended for web apps and remains available only for backward compatibility.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9fd89-379">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="9fd89-379">Set up a host</span></span>

<span data-ttu-id="9fd89-380">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-380">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="9fd89-381">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="9fd89-381">The `Main` method:</span></span>

* <span data-ttu-id="9fd89-382">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-382">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="9fd89-383">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-383">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="9fd89-384">HTTP 以外のワークロード用の *Program.cs* コードを次に示します。単一の `IHostedService` 実装が DI コンテナーに追加されています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-384">Here's *Program.cs* code for a non-HTTP workload, with a single `IHostedService` implementation added to the DI container.</span></span> 

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

<span data-ttu-id="9fd89-385">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-385">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="9fd89-386">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-386">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="9fd89-387">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-387">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="9fd89-388">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-388">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="9fd89-389">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="9fd89-389">Default builder settings</span></span>

<span data-ttu-id="9fd89-390"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="9fd89-390">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="9fd89-391">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-391">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="9fd89-392">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-392">Loads host configuration from:</span></span>
  * <span data-ttu-id="9fd89-393">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-393">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="9fd89-394">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-394">Command-line arguments.</span></span>
* <span data-ttu-id="9fd89-395">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-395">Loads app configuration from:</span></span>
  * <span data-ttu-id="9fd89-396">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="9fd89-396">*appsettings.json*.</span></span>
  * <span data-ttu-id="9fd89-397">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="9fd89-397">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="9fd89-398">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="9fd89-398">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="9fd89-399">環境変数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-399">Environment variables.</span></span>
  * <span data-ttu-id="9fd89-400">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="9fd89-400">Command-line arguments.</span></span>
* <span data-ttu-id="9fd89-401">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-401">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="9fd89-402">コンソール</span><span class="sxs-lookup"><span data-stu-id="9fd89-402">Console</span></span>
  * <span data-ttu-id="9fd89-403">デバッグ</span><span class="sxs-lookup"><span data-stu-id="9fd89-403">Debug</span></span>
  * <span data-ttu-id="9fd89-404">EventSource</span><span class="sxs-lookup"><span data-stu-id="9fd89-404">EventSource</span></span>
  * <span data-ttu-id="9fd89-405">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="9fd89-405">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="9fd89-406">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-406">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="9fd89-407">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="9fd89-407">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="9fd89-408">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-408">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="9fd89-409">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-409">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="9fd89-410">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-410">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="9fd89-411">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-411">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="9fd89-412">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-412">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="9fd89-413">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-413">Enables IIS integration.</span></span> <span data-ttu-id="9fd89-414">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-414">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="9fd89-415">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-415">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="9fd89-416">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="9fd89-416">Framework-provided services</span></span>

<span data-ttu-id="9fd89-417">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-417">The following services are registered automatically:</span></span>

* [<span data-ttu-id="9fd89-418">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-418">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="9fd89-419">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-419">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="9fd89-420">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9fd89-420">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="9fd89-421">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-421">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="9fd89-422">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-422">IHostApplicationLifetime</span></span>

<span data-ttu-id="9fd89-423">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-423">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="9fd89-424">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-424">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="9fd89-425">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-425">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="9fd89-426">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-426">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="9fd89-427">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-427">IHostLifetime</span></span>

<span data-ttu-id="9fd89-428"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-428">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="9fd89-429">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-429">The last implementation registered is used.</span></span>

<span data-ttu-id="9fd89-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-430">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="9fd89-431">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="9fd89-431">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="9fd89-432"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-432">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="9fd89-433">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-433">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="9fd89-434">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="9fd89-434">IHostEnvironment</span></span>

<span data-ttu-id="9fd89-435">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-435">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="9fd89-436">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9fd89-436">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="9fd89-437">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9fd89-437">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="9fd89-438">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9fd89-438">ContentRootPath</span></span>](#contentrootpath)

<span data-ttu-id="9fd89-439">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-439">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9fd89-440">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-440">Host configuration</span></span>

<span data-ttu-id="9fd89-441">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-441">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="9fd89-442">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-442">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="9fd89-443">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-443">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="9fd89-444">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-444">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9fd89-445">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-445">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9fd89-446">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-446">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9fd89-447">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-447">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="9fd89-448">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-448">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="9fd89-449">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-449">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9fd89-450">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-450">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9fd89-451">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-451">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="9fd89-452">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-452">App configuration</span></span>

<span data-ttu-id="9fd89-453">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-453">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="9fd89-454">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-454">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="9fd89-455">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-455">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="9fd89-456">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-456">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="9fd89-457">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-457">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="9fd89-458">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-458">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="9fd89-459">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="9fd89-459">Settings for all app types</span></span>

<span data-ttu-id="9fd89-460">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-460">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="9fd89-461">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-461">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="9fd89-462">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="9fd89-462">ApplicationName</span></span>

<span data-ttu-id="9fd89-463">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-463">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="9fd89-464">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9fd89-464">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9fd89-465">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-465">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-466">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="9fd89-466">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="9fd89-467">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="9fd89-467">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="9fd89-468">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-468">To set this value, use the environment variable.</span></span> 

### <a name="contentrootpath"></a><span data-ttu-id="9fd89-469">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9fd89-469">ContentRootPath</span></span>

<span data-ttu-id="9fd89-470">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-470">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="9fd89-471">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-471">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="9fd89-472">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9fd89-472">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9fd89-473">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-473">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-474">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="9fd89-474">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="9fd89-475">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-475">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="9fd89-476">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-476">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="9fd89-477">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="9fd89-477">For more information, see:</span></span>

* [<span data-ttu-id="9fd89-478">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="9fd89-478">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="9fd89-479">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9fd89-479">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="9fd89-480">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9fd89-480">EnvironmentName</span></span>

<span data-ttu-id="9fd89-481">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-481">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="9fd89-482">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-482">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9fd89-483">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-483">Values aren't case-sensitive.</span></span>

<span data-ttu-id="9fd89-484">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9fd89-484">**Key**: `environment`</span></span>  
<span data-ttu-id="9fd89-485">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-485">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-486">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9fd89-486">**Default**: `Production`</span></span>  
<span data-ttu-id="9fd89-487">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-487">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="9fd89-488">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-488">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="9fd89-489">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="9fd89-489">ShutdownTimeout</span></span>

<span data-ttu-id="9fd89-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-490">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9fd89-491">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-491">The default value is five seconds.</span></span>  <span data-ttu-id="9fd89-492">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-492">During the timeout period, the host:</span></span>

* <span data-ttu-id="9fd89-493">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-493">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="9fd89-494">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-494">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="9fd89-495">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-495">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="9fd89-496">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-496">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="9fd89-497">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-497">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="9fd89-498">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="9fd89-498">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="9fd89-499">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="9fd89-499">**Type**: `int`</span></span>  
<span data-ttu-id="9fd89-500">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="9fd89-500">**Default**: 5 seconds</span></span>  
<span data-ttu-id="9fd89-501">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-501">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="9fd89-502">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-502">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="9fd89-503">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-503">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="9fd89-504">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="9fd89-504">Settings for web apps</span></span>

<span data-ttu-id="9fd89-505">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-505">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="9fd89-506">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-506">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="9fd89-507">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-507">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="9fd89-508">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-508">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="9fd89-509">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="9fd89-509">CaptureStartupErrors</span></span>

<span data-ttu-id="9fd89-510">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-510">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="9fd89-511">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-511">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="9fd89-512">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="9fd89-512">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="9fd89-513">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-513">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-514">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-514">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="9fd89-515">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-515">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="9fd89-516">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-516">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="9fd89-517">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="9fd89-517">DetailedErrors</span></span>

<span data-ttu-id="9fd89-518">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-518">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="9fd89-519">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="9fd89-519">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="9fd89-520">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-520">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-521">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="9fd89-521">**Default**: `false`</span></span>  
<span data-ttu-id="9fd89-522">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-522">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="9fd89-523">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-523">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="9fd89-524">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="9fd89-524">HostingStartupAssemblies</span></span>

<span data-ttu-id="9fd89-525">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="9fd89-525">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="9fd89-526">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-526">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="9fd89-527">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-527">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="9fd89-528">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9fd89-528">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="9fd89-529">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-529">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-530">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="9fd89-530">**Default**: Empty string</span></span>  
<span data-ttu-id="9fd89-531">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9fd89-531">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="9fd89-532">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-532">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="9fd89-533">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="9fd89-533">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="9fd89-534">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="9fd89-534">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="9fd89-535">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="9fd89-535">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="9fd89-536">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-536">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-537">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="9fd89-537">**Default**: Empty string</span></span>  
<span data-ttu-id="9fd89-538">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="9fd89-538">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="9fd89-539">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-539">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="9fd89-540">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="9fd89-540">HTTPS_Port</span></span>

<span data-ttu-id="9fd89-541">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="9fd89-541">The HTTPS redirect port.</span></span> <span data-ttu-id="9fd89-542">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-542">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="9fd89-543">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="9fd89-543">**Key**: `https_port`</span></span>  
<span data-ttu-id="9fd89-544">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-544">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-545">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-545">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="9fd89-546">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-546">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="9fd89-547">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-547">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="9fd89-548">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="9fd89-548">PreferHostingUrls</span></span>

<span data-ttu-id="9fd89-549">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-549">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="9fd89-550">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="9fd89-550">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="9fd89-551">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-551">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-552">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="9fd89-552">**Default**: `true`</span></span>  
<span data-ttu-id="9fd89-553">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-553">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="9fd89-554">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-554">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="9fd89-555">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="9fd89-555">PreventHostingStartup</span></span>

<span data-ttu-id="9fd89-556">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-556">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="9fd89-557">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-557">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="9fd89-558">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="9fd89-558">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="9fd89-559">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-559">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="9fd89-560">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="9fd89-560">**Default**: `false`</span></span>  
<span data-ttu-id="9fd89-561">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="9fd89-561">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="9fd89-562">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-562">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="9fd89-563">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="9fd89-563">StartupAssembly</span></span>

<span data-ttu-id="9fd89-564">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="9fd89-564">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="9fd89-565">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="9fd89-565">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="9fd89-566">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-566">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-567">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="9fd89-567">**Default**: The app's assembly</span></span>  
<span data-ttu-id="9fd89-568">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="9fd89-568">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="9fd89-569">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-569">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="9fd89-570">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-570">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="9fd89-571">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-571">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="9fd89-572">URL</span><span class="sxs-lookup"><span data-stu-id="9fd89-572">URLs</span></span>

<span data-ttu-id="9fd89-573">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="9fd89-573">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="9fd89-574">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-574">For example, `http://localhost:123`.</span></span> <span data-ttu-id="9fd89-575">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-575">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="9fd89-576">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-576">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="9fd89-577">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-577">Supported formats vary among servers.</span></span>

<span data-ttu-id="9fd89-578">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="9fd89-578">**Key**: `urls`</span></span>  
<span data-ttu-id="9fd89-579">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-579">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-580">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="9fd89-580">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="9fd89-581">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="9fd89-581">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="9fd89-582">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-582">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="9fd89-583">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-583">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="9fd89-584">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-584">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="9fd89-585">WebRoot</span><span class="sxs-lookup"><span data-stu-id="9fd89-585">WebRoot</span></span>

<span data-ttu-id="9fd89-586">アプリの静的資産への相対パス。</span><span class="sxs-lookup"><span data-stu-id="9fd89-586">The relative path to the app's static assets.</span></span>

<span data-ttu-id="9fd89-587">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="9fd89-587">**Key**: `webroot`</span></span>  
<span data-ttu-id="9fd89-588">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-588">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-589">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-589">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="9fd89-590">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-590">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="9fd89-591">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-591">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="9fd89-592">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="9fd89-592">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="9fd89-593">この値を設定するには、環境変数を使用するか、または `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-593">To set this value, use the environment variable or call `UseWebRoot`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="9fd89-594">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="9fd89-594">For more information, see:</span></span>

* [<span data-ttu-id="9fd89-595">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="9fd89-595">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="9fd89-596">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9fd89-596">ContentRootPath</span></span>](#contentrootpath)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="9fd89-597">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="9fd89-597">Manage the host lifetime</span></span>

<span data-ttu-id="9fd89-598">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="9fd89-598">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="9fd89-599">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-599">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9fd89-600">実行</span><span class="sxs-lookup"><span data-stu-id="9fd89-600">Run</span></span>

<span data-ttu-id="9fd89-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-601"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="9fd89-602">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-602">RunAsync</span></span>

<span data-ttu-id="9fd89-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-603"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="9fd89-604">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-604">RunConsoleAsync</span></span>

<span data-ttu-id="9fd89-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-605"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="9fd89-606">[開始]</span><span class="sxs-lookup"><span data-stu-id="9fd89-606">Start</span></span>

<span data-ttu-id="9fd89-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="9fd89-608">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-608">StartAsync</span></span>

<span data-ttu-id="9fd89-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-609"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="9fd89-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-610"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9fd89-611">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-611">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="9fd89-612">StopAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-612">StopAsync</span></span>

<span data-ttu-id="9fd89-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="9fd89-614">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9fd89-614">WaitForShutdown</span></span>

<span data-ttu-id="9fd89-615"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-615"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="9fd89-616">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-616">WaitForShutdownAsync</span></span>

<span data-ttu-id="9fd89-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-617"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="9fd89-618">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="9fd89-618">External control</span></span>

<span data-ttu-id="9fd89-619">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-619">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9fd89-620">ASP.NET Core アプリはホストを構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-620">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="9fd89-621">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-621">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="9fd89-622">この記事では、HTTP 要求を処理しないアプリに使用される、ASP.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について説明します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-622">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="9fd89-623">汎用ホストの目的は、Web ホスト API から HTTP パイプラインを切り離して、多様なホスト シナリオを有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-623">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="9fd89-624">メッセージング、バックグラウンド タスク、汎用ホストに基づくその他の HTTP ワークロードに対して、構成、依存関係の挿入 (DI)、ログなどの横断的機能によるメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-624">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="9fd89-625">汎用ホストは ASP.NET Core 2.1 の新機能であり、Web ホスティングのシナリオには適していません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-625">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="9fd89-626">Web ホスティングのシナリオの場合は、[Web ホスト](xref:fundamentals/host/web-host)を使ってください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-626">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="9fd89-627">汎用ホストは将来のリリースで Web ホストに代わるものであり、HTTP と非 HTTP 両方のシナリオのプライマリ ホスト API として機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-627">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="9fd89-628">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9fd89-628">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9fd89-629">[Visual Studio Code](https://code.visualstudio.com/) でサンプル アプリを実行するときは、"*外部ターミナルまたは統合ターミナル*" を使います。</span><span class="sxs-lookup"><span data-stu-id="9fd89-629">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="9fd89-630">`internalConsole` ではサンプルを実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-630">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="9fd89-631">Visual Studio Code でコンソールを設定するには:</span><span class="sxs-lookup"><span data-stu-id="9fd89-631">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="9fd89-632">*.vscode/launch.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-632">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="9fd89-633">**.NET Core Launch (console)** の構成で、**console** エントリを探します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-633">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="9fd89-634">値を `externalTerminal` または `integratedTerminal` に設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-634">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="9fd89-635">はじめに</span><span class="sxs-lookup"><span data-stu-id="9fd89-635">Introduction</span></span>

<span data-ttu-id="9fd89-636">汎用ホスト ライブラリは、<xref:Microsoft.Extensions.Hosting> 名前空間で使用でき、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) パッケージで提供されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-636">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="9fd89-637">`Microsoft.Extensions.Hosting` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます (ASP.NET Core 2.1 以降)。</span><span class="sxs-lookup"><span data-stu-id="9fd89-637">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="9fd89-638"><xref:Microsoft.Extensions.Hosting.IHostedService> がコード実行のエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-638"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="9fd89-639"><xref:Microsoft.Extensions.Hosting.IHostedService> の各実装は、[ConfigureServices でのサービス登録](#configureservices)の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-639">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="9fd89-640">ホストが開始するときは <xref:Microsoft.Extensions.Hosting.IHostedService> ごとに <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> が呼び出され、ホストが正常に終了するときは登録と逆の順序で <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-640"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="9fd89-641">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="9fd89-641">Set up a host</span></span>

<span data-ttu-id="9fd89-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> は、ライブラリとアプリがホストの初期化、ビルド、実行に使用するメイン コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-642"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="9fd89-643">オプション</span><span class="sxs-lookup"><span data-stu-id="9fd89-643">Options</span></span>

<span data-ttu-id="9fd89-644"><xref:Microsoft.Extensions.Hosting.IHost> の <xref:Microsoft.Extensions.Hosting.HostOptions> 構成オプションです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-644"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="9fd89-645">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="9fd89-645">Shutdown timeout</span></span>

<span data-ttu-id="9fd89-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> によって <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-646"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="9fd89-647">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="9fd89-647">The default value is five seconds.</span></span>

<span data-ttu-id="9fd89-648">次に示す `Program.Main` のオプション構成では、既定の 5 秒のシャットダウン タイムアウトが 20 秒に延長されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-648">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="9fd89-649">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="9fd89-649">Default services</span></span>

<span data-ttu-id="9fd89-650">次のサービスは、ホストの初期化中に登録されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-650">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="9fd89-651">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="9fd89-651">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="9fd89-652">[構成](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="9fd89-652">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="9fd89-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-653"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="9fd89-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="9fd89-654"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="9fd89-655">[オプション](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="9fd89-655">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="9fd89-656">[ログ](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="9fd89-656">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="9fd89-657">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-657">Host configuration</span></span>

<span data-ttu-id="9fd89-658">ホストの構成は、次のようにして作成されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-658">Host configuration is created by:</span></span>

* <span data-ttu-id="9fd89-659"><xref:Microsoft.Extensions.Hosting.IHostBuilder> で拡張メソッドを呼び出して、[コンテンツ ルート](#content-root)と[環境](#environment)を設定する。</span><span class="sxs-lookup"><span data-stu-id="9fd89-659">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="9fd89-660"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> の構成プロバイダーから構成を読み取る。</span><span class="sxs-lookup"><span data-stu-id="9fd89-660">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="9fd89-661">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="9fd89-661">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="9fd89-662">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="9fd89-662">Application key (name)</span></span>

<span data-ttu-id="9fd89-663">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-663">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="9fd89-664">値を明示的に設定するには、[HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-664">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="9fd89-665">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="9fd89-665">**Key**: `applicationName`</span></span>  
<span data-ttu-id="9fd89-666">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-666">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-667">**既定**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="9fd89-667">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="9fd89-668">**次を使用して設定**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="9fd89-668">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="9fd89-669">**環境変数**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9fd89-669">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="9fd89-670">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="9fd89-670">Content root</span></span>

<span data-ttu-id="9fd89-671">この設定では、ホストがコンテンツ ファイルの検索を開始する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-671">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="9fd89-672">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="9fd89-672">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="9fd89-673">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-673">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-674">**既定**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-674">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="9fd89-675">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="9fd89-675">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="9fd89-676">**環境変数**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9fd89-676">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9fd89-677">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-677">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="9fd89-678">詳細については、[基礎: コンテンツ ルート](xref:fundamentals/index#content-root)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-678">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="9fd89-679">環境</span><span class="sxs-lookup"><span data-stu-id="9fd89-679">Environment</span></span>

<span data-ttu-id="9fd89-680">アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-680">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9fd89-681">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="9fd89-681">**Key**: `environment`</span></span>  
<span data-ttu-id="9fd89-682">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="9fd89-682">**Type**: `string`</span></span>  
<span data-ttu-id="9fd89-683">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="9fd89-683">**Default**: `Production`</span></span>  
<span data-ttu-id="9fd89-684">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="9fd89-684">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="9fd89-685">**環境変数**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="9fd89-685">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="9fd89-686">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-686">The environment can be set to any value.</span></span> <span data-ttu-id="9fd89-687">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-687">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="9fd89-688">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-688">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="9fd89-689">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="9fd89-689">ConfigureHostConfiguration</span></span>

<span data-ttu-id="9fd89-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、ホストの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-690"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="9fd89-691">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> をアプリのビルド プロセスで使用するための初期化に使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-691">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="9fd89-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-692"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9fd89-693">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-693">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="9fd89-694">既定ではプロバイダーが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="9fd89-694">No providers are included by default.</span></span> <span data-ttu-id="9fd89-695">次のような、アプリが <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> で必要とする構成プロバイダーを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-695">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="9fd89-696">ファイルの構成 (*hostsettings.json* ファイルからなど)。</span><span class="sxs-lookup"><span data-stu-id="9fd89-696">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="9fd89-697">環境変数の構成。</span><span class="sxs-lookup"><span data-stu-id="9fd89-697">Environment variable configuration.</span></span>
* <span data-ttu-id="9fd89-698">コマンドライン引数の構成。</span><span class="sxs-lookup"><span data-stu-id="9fd89-698">Command-line argument configuration.</span></span>
* <span data-ttu-id="9fd89-699">その他に必要なすべての構成プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="9fd89-699">Any other required configuration providers.</span></span>

<span data-ttu-id="9fd89-700">ホストのファイル構成は、いずれかの[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)に対する呼び出しの前に `SetBasePath` のアプリのベース パスを指定することで有効になります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-700">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="9fd89-701">サンプル アプリは、JSON ファイル (*hostsettings.json*) を使用し、<xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> を呼び出して、ファイルのホスト構成設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-701">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="9fd89-702">ホストの[環境変数の構成](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を追加するには、ホスト ビルダーで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-702">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="9fd89-703">`AddEnvironmentVariables` は、オプションのユーザー定義プレフィックスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-703">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="9fd89-704">サンプル アプリは、`PREFIX_` のプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-704">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="9fd89-705">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-705">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="9fd89-706">サンプル アプリのホストが構成されると、`PREFIX_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-706">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="9fd89-707">開発中に [Visual Studio](https://visualstudio.microsoft.com) を使用している、または `dotnet run` を使用してアプリを実行している場合は、環境変数を *Properties/launchSettings.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-707">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="9fd89-708">[Visual Studio Code](https://code.visualstudio.com/) では、開発中に環境変数を *.vscode/launch.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-708">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="9fd89-709">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-709">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9fd89-710">[コマンドラインの構成](xref:fundamentals/configuration/index#command-line-configuration-provider)は、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>を呼び出すことで追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-710">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="9fd89-711">コマンドラインの構成は、コマンドライン引数を許可して以前の構成プロバイダーから提供された構成をオーバーライドするために最後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-711">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="9fd89-712">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9fd89-712">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="9fd89-713">追加の構成は、[applicationName](#application-key-name) と [contentRoot](#content-root) キーで指定することができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-713">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="9fd89-714"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を使う `HostBuilder` の構成の例:</span><span class="sxs-lookup"><span data-stu-id="9fd89-714">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="9fd89-715">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="9fd89-715">ConfigureAppConfiguration</span></span>

<span data-ttu-id="9fd89-716">アプリの構成は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-716">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="9fd89-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、アプリの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-717"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="9fd89-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-718"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="9fd89-719">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-719">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="9fd89-720"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> によって作成された構成は、以降の操作に対する [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) において、および <xref:Microsoft.Extensions.Hosting.IHost.Services*>サービス内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-720">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="9fd89-721">アプリの構成は、[ConfigureHostConfiguration](#configurehostconfiguration) によって提供されたホストの構成を自動的に受信します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-721">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="9fd89-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を使うアプリ構成の例:</span><span class="sxs-lookup"><span data-stu-id="9fd89-722">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="9fd89-723">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="9fd89-723">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="9fd89-724">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="9fd89-724">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="9fd89-725">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="9fd89-725">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="9fd89-726">設定ファイルを出力ディレクトリに移動するには、プロジェクト ファイルの設定ファイルを [MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) として指定します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-726">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="9fd89-727">サンプル アプリはその JSON アプリ設定ファイルと、次の `<Content>` 項目を含む *hostsettings.json* を移動します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-727">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="9fd89-728"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> や <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> などの構成拡張メソッドでは、[Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) や [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) など、追加の NuGet パッケージを必要とします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-728">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="9fd89-729">アプリが [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) を使用しない限り、中心となる [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) パッケージに加えて、これらのパッケージがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-729">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="9fd89-730">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-730">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="9fd89-731">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="9fd89-731">ConfigureServices</span></span>

<span data-ttu-id="9fd89-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> は、アプリの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーにサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-732"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="9fd89-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-733"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9fd89-734">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-734">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="9fd89-735">詳細については、「<xref:fundamentals/host/hosted-services>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-735">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="9fd89-736">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)では、`AddHostedService` 拡張メソッドを使って、有効期間イベント `LifetimeEventsHostedService` と時刻指定付きバックグラウンド タスク `TimedHostedService` のサービスをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-736">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="9fd89-737">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="9fd89-737">ConfigureLogging</span></span>

<span data-ttu-id="9fd89-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> は、指定された <xref:Microsoft.Extensions.Logging.ILoggingBuilder> を構成するためのデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="9fd89-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="9fd89-740">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="9fd89-740">UseConsoleLifetime</span></span>

<span data-ttu-id="9fd89-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> では、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-741"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="9fd89-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> は、[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-742"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="9fd89-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の有効期間の実装として事前に登録されています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-743">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="9fd89-744">最後に登録された有効期間が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-744">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="9fd89-745">コンテナーの構成</span><span class="sxs-lookup"><span data-stu-id="9fd89-745">Container configuration</span></span>

<span data-ttu-id="9fd89-746">他のコンテナーの接続をサポートするため、ホストは <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-746">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="9fd89-747">ファクトリの提供は、DI コンテナーの登録の一部ではなく、具象 DI コンテナーの作成に使用されるホストの組み込みです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-747">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="9fd89-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) は、アプリのサービス プロバイダーの作成に使われる既定のファクトリをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-748">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="9fd89-749">カスタム コンテナーの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> メソッドによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-749">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="9fd89-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> は、基盤のホスト API を基にしてコンテナーを構成するための、厳密に型指定されたエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-750"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="9fd89-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-751"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="9fd89-752">アプリのサービス コンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-752">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="9fd89-753">サービス コンテナーのファクトリを提供します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-753">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="9fd89-754">ファクトリを使って、アプリのカスタム サービス コンテナーを構成します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-754">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="9fd89-755">機能拡張</span><span class="sxs-lookup"><span data-stu-id="9fd89-755">Extensibility</span></span>

<span data-ttu-id="9fd89-756">ホスト拡張機能は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> 上の拡張メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-756">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="9fd89-757">次の例では、拡張メソッドが <xref:fundamentals/host/hosted-services> で示される [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) の例を使って <xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装を拡張する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9fd89-757">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="9fd89-758">アプリは `UseHostedService` 拡張メソッドを確率して `T` に渡されたホステッド サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-758">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="9fd89-759">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="9fd89-759">Manage the host</span></span>

<span data-ttu-id="9fd89-760"><xref:Microsoft.Extensions.Hosting.IHost> の実装は、サービス コンテナーに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の実装の開始と停止を行います。</span><span class="sxs-lookup"><span data-stu-id="9fd89-760">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="9fd89-761">実行</span><span class="sxs-lookup"><span data-stu-id="9fd89-761">Run</span></span>

<span data-ttu-id="9fd89-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-762"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="9fd89-763">RunAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-763">RunAsync</span></span>

<span data-ttu-id="9fd89-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-764"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="9fd89-765">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-765">RunConsoleAsync</span></span>

<span data-ttu-id="9fd89-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-766"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="9fd89-767">Start と StopAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-767">Start and StopAsync</span></span>

<span data-ttu-id="9fd89-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="9fd89-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="9fd89-770">StartAsync と StopAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-770">StartAsync and StopAsync</span></span>

<span data-ttu-id="9fd89-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> がアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-771"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="9fd89-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> がアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-772"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="9fd89-773">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="9fd89-773">WaitForShutdown</span></span>

<span data-ttu-id="9fd89-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は、`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンする) などの <xref:Microsoft.Extensions.Hosting.IHostLifetime> を介してトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="9fd89-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="9fd89-776">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="9fd89-776">WaitForShutdownAsync</span></span>

<span data-ttu-id="9fd89-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-777"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="9fd89-778">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="9fd89-778">External control</span></span>

<span data-ttu-id="9fd89-779">ホストの外部コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-779">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="9fd89-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-780"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="9fd89-781">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-781">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="9fd89-782">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="9fd89-782">IHostingEnvironment interface</span></span>

<span data-ttu-id="9fd89-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> は、アプリのホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-783"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="9fd89-784">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> を取得します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-784">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="9fd89-785">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9fd89-785">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="9fd89-786">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="9fd89-786">IApplicationLifetime interface</span></span>

<span data-ttu-id="9fd89-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> は、正常なシャットダウンの要求など、起動後とシャットダウンのアクティビティに対応します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-787"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="9fd89-788">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する <xref:System.Action> メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="9fd89-788">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="9fd89-789">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="9fd89-789">Cancellation Token</span></span> | <span data-ttu-id="9fd89-790">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="9fd89-790">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="9fd89-791">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="9fd89-791">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="9fd89-792">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="9fd89-792">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="9fd89-793">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fd89-793">All requests should be processed.</span></span> <span data-ttu-id="9fd89-794">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-794">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="9fd89-795">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="9fd89-795">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="9fd89-796">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="9fd89-796">Requests may still be processing.</span></span> <span data-ttu-id="9fd89-797">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="9fd89-797">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="9fd89-798">コンストラクターは任意のクラスに <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-798">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="9fd89-799">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)は、`LifetimeEventsHostedService` クラス (<xref:Microsoft.Extensions.Hosting.IHostedService> の実装) へのコンストラクターの挿入を使って、イベントを登録します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-799">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="9fd89-800">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="9fd89-800">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="9fd89-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="9fd89-801"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="9fd89-802">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="9fd89-802">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9fd89-803">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9fd89-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
