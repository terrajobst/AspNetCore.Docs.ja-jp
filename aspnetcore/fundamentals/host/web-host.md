---
title: ASP.NET Core の Web ホスト
author: rick-anderson
description: ASP.NET Core アプリの Web ホスト (アプリの起動と有効期間の管理を担当する) について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650582"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="ffb7a-103">ASP.NET Core の Web ホスト</span><span class="sxs-lookup"><span data-stu-id="ffb7a-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="ffb7a-104">ASP.NET Core アプリは*ホスト*を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="ffb7a-105">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="ffb7a-106">少なくとも、ホストはサーバーおよび要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="ffb7a-107">ホストでは、ログ記録、依存関係挿入、構成を設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ffb7a-108">この記事では、Web ホストについて説明します。これは、下位互換性のためだけに引き続き使用可能となっています。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="ffb7a-109">すべての種類のアプリに対して、[汎用ホスト](xref:fundamentals/host/generic-host)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffb7a-110">この記事では Web ホストについて説明します。これは Web アプリをホストするためのものです。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="ffb7a-111">その他の種類のアプリでは、[汎用ホスト](xref:fundamentals/host/generic-host)をご使用ください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="ffb7a-112">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="ffb7a-112">Set up a host</span></span>

<span data-ttu-id="ffb7a-113">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) のインスタンスを使用して、ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="ffb7a-114">通常、これはアプリのエントリ ポイントの `Main` メソッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="ffb7a-115">プロジェクト テンプレートでは、`Main` は *Program.cs* にあります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="ffb7a-116">一般的なアプリでは、[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) を呼び出してホストの設定を開始します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="ffb7a-117">`CreateDefaultBuilder` を呼び出すコードは、`CreateWebHostBuilder` という名前のメソッドに含まれ、ビルダー オブジェクトで `Run` を呼び出す `Main` のコードから分離されています。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="ffb7a-118">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/)を使用する場合は、このような分離が必要です。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="ffb7a-119">ツールでは、アプリを実行することなくホストを構成するために、デザイン時に呼び出すことができる `CreateWebHostBuilder` メソッドの検出が想定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="ffb7a-120">別の方法は、`IDesignTimeDbContextFactory` を実装することです。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="ffb7a-121">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="ffb7a-122">`CreateDefaultBuilder` では次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="ffb7a-123">アプリのホスティング構成プロバイダーを使用して [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="ffb7a-124">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="ffb7a-125">[コンテンツ ルート](xref:fundamentals/index#content-root)を、[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="ffb7a-126">次から[ ホスト構成](#host-configuration-values)を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="ffb7a-127">`ASPNETCORE_` のプレフィックスが付いた環境変数 (たとえば、`ASPNETCORE_ENVIRONMENT`)。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="ffb7a-128">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-128">Command-line arguments.</span></span>
* <span data-ttu-id="ffb7a-129">次の順序でアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="ffb7a-130">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="ffb7a-131">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="ffb7a-132">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="ffb7a-133">環境変数。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-133">Environment variables.</span></span>
  * <span data-ttu-id="ffb7a-134">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-134">Command-line arguments.</span></span>
* <span data-ttu-id="ffb7a-135">コンソールとデバッグ出力の[ログ](xref:fundamentals/logging/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="ffb7a-136">ログには、*appsettings.json* または *appsettings.{Environment}.json* ファイルのログ構成セクションで指定される[ログ フィルター](xref:fundamentals/logging/index#log-filtering)規則が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="ffb7a-137">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して IIS の背後で実行されている場合は、`CreateDefaultBuilder` によってアプリのベース アドレスとポートが構成される [IIS の統合](xref:host-and-deploy/iis/index)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="ffb7a-138">IIS の統合により、[スタートアップ エラーをキャプチャする](#capture-startup-errors)アプリも構成されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="ffb7a-139">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="ffb7a-140">アプリの環境が開発の場合、[ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="ffb7a-141">詳しくは、「[スコープの検証](#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="ffb7a-142">`CreateDefaultBuilder` によって定義された構成は、[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)、[ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)、そして [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) のその他のメソッドと拡張メソッドによってオーバーライドされ、拡張されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="ffb7a-143">以下に、いくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-143">A few examples follow:</span></span>

* <span data-ttu-id="ffb7a-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) はアプリの追加の `IConfiguration` を指定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="ffb7a-145">次の `ConfigureAppConfiguration` の呼び出しによりデリゲートが追加され、*appsettings.xml* ファイルにアプリの構成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="ffb7a-146">`ConfigureAppConfiguration` は複数回呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="ffb7a-147">この構成はホスト (たとえば、サーバーの URL や環境など) には適用されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="ffb7a-148">「[ホストの構成値](#host-configuration-values)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="ffb7a-149">次の `ConfigureLogging` の呼び出しによりデリゲートが追加され、最小ログ記録レベル ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) が [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel) に構成されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="ffb7a-150">この設定により、`CreateDefaultBuilder` で構成された *appsettings.Development.json* (`LogLevel.Debug`) および *appsettings.Production.json* (`LogLevel.Error`) の設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="ffb7a-151">`ConfigureLogging` は複数回呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="ffb7a-152">次の `ConfigureKestrel` の呼び出しにより、Kestrel が `CreateDefaultBuilder` によって構成されたときに確立された既定の [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) サイズである 30,000,000 バイトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="ffb7a-153">次の [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) の呼び出しにより、Kestrel が `CreateDefaultBuilder` によって構成されたときに確立された既定の [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) サイズである 30,000,000 バイトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="ffb7a-154">[コンテンツ ルート](xref:fundamentals/index#content-root)で、ホストが MVC ビュー ファイルなどのコンテンツ ファイルを検索する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="ffb7a-155">プロジェクトのルート フォルダーからアプリが開始された場合は、そのプロジェクトのルート フォルダーがコンテンツ ルートとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="ffb7a-156">これは、[Visual Studio](https://visualstudio.microsoft.com) と [dotnet の新しいテンプレート](/dotnet/core/tools/dotnet-new)で使用される既定です。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="ffb7a-157">アプリの構成の詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="ffb7a-158">静的な `CreateDefaultBuilder` メソッドを使用する代わりに、[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) からホストを作成する方法が ASP.NET Core 2.x でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="ffb7a-159">ホストの構成時に、[Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) および [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) メソッドを指摘することができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="ffb7a-160">`Startup` クラスが指定されている場合は、`Configure` メソッドを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="ffb7a-161">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="ffb7a-162">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="ffb7a-163">`WebHostBuilder` での `Configure` または `UseStartup` の複数回の呼び出しでは、以前の設定が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="ffb7a-164">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="ffb7a-164">Host configuration values</span></span>

<span data-ttu-id="ffb7a-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) では、ホストの構成値を設定する場合に以下の方法に依存します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="ffb7a-166">`ASPNETCORE_{configurationKey}` 形式の環境変数を含む、ホスト ビルダー構成。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="ffb7a-167">たとえば、`ASPNETCORE_ENVIRONMENT` のようにします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="ffb7a-168">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) や [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) などの拡張機能 (「[構成をオーバーライドする](#override-configuration)」のセクションを参照)。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="ffb7a-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) と関連するキー。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="ffb7a-170">`UseSetting` で値を設定すると、値はその型に関係なく、文字列として設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="ffb7a-171">ホストは、最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="ffb7a-172">詳しくは、次のセクション「[構成をオーバーライドする](#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="ffb7a-173">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="ffb7a-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ffb7a-174">ホストの構築時に [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) または [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) を呼び出すと、`IWebHostEnvironment.ApplicationName` プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="ffb7a-175">この値はアプリのエントリ ポイントを含むアセンブリの名前に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="ffb7a-176">値を明示的に設定するには、[WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffb7a-177">ホストの構築時に [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) または [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) が呼び出されると、[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="ffb7a-178">この値はアプリのエントリ ポイントを含むアセンブリの名前に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="ffb7a-179">値を明示的に設定するには、[WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="ffb7a-180">**キー**: applicationName</span><span class="sxs-lookup"><span data-stu-id="ffb7a-180">**Key**: applicationName</span></span>  
<span data-ttu-id="ffb7a-181">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-181">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-182">**既定値**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="ffb7a-183">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ffb7a-184">**環境変数**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="ffb7a-185">スタートアップ エラーをキャプチャする</span><span class="sxs-lookup"><span data-stu-id="ffb7a-185">Capture Startup Errors</span></span>

<span data-ttu-id="ffb7a-186">この設定では、スタートアップ エラーのキャプチャを制御します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="ffb7a-187">**キー**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="ffb7a-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="ffb7a-188">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="ffb7a-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ffb7a-189">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="ffb7a-190">**次を使用して設定**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="ffb7a-191">**環境変数**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="ffb7a-192">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="ffb7a-193">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="ffb7a-194">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="ffb7a-194">Content root</span></span>

<span data-ttu-id="ffb7a-195">この設定により、ASP.NET Core でコンテンツ ファイルの検索が開始される場所が決まります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="ffb7a-196">**キー**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="ffb7a-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="ffb7a-197">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-197">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-198">**既定**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="ffb7a-199">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="ffb7a-200">**環境変数**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="ffb7a-201">コンテンツ ルートは、[Web ルート](xref:fundamentals/index#web-root)の基本パスとしても使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="ffb7a-202">コンテンツ ルート パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="ffb7a-203">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="ffb7a-203">For more information, see:</span></span>

* [<span data-ttu-id="ffb7a-204">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="ffb7a-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="ffb7a-205">Web ルート</span><span class="sxs-lookup"><span data-stu-id="ffb7a-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="ffb7a-206">詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="ffb7a-206">Detailed Errors</span></span>

<span data-ttu-id="ffb7a-207">詳細なエラーをキャプチャするかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="ffb7a-208">**キー**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="ffb7a-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="ffb7a-209">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="ffb7a-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ffb7a-210">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="ffb7a-210">**Default**: false</span></span>  
<span data-ttu-id="ffb7a-211">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ffb7a-212">**環境変数**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="ffb7a-213">有効な場合 (または<a href="#environment">環境</a>が `Development` に設定されている場合)、アプリは詳細な例外をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="ffb7a-214">環境</span><span class="sxs-lookup"><span data-stu-id="ffb7a-214">Environment</span></span>

<span data-ttu-id="ffb7a-215">アプリの環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-215">Sets the app's environment.</span></span>

<span data-ttu-id="ffb7a-216">**キー**: 環境</span><span class="sxs-lookup"><span data-stu-id="ffb7a-216">**Key**: environment</span></span>  
<span data-ttu-id="ffb7a-217">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-217">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-218">**既定**:実稼働</span><span class="sxs-lookup"><span data-stu-id="ffb7a-218">**Default**: Production</span></span>  
<span data-ttu-id="ffb7a-219">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="ffb7a-220">**環境変数**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="ffb7a-221">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-221">The environment can be set to any value.</span></span> <span data-ttu-id="ffb7a-222">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="ffb7a-223">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-223">Values aren't case sensitive.</span></span> <span data-ttu-id="ffb7a-224">既定では、*環境*は `ASPNETCORE_ENVIRONMENT` 環境変数から読み取られます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="ffb7a-225">[Visual Studio](https://visualstudio.microsoft.com) を使用する場合、環境変数は *launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="ffb7a-226">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="ffb7a-227">ホスティング スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="ffb7a-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="ffb7a-228">アプリのホスティング スタートアップ アセンブリを設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="ffb7a-229">**キー**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="ffb7a-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="ffb7a-230">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-230">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-231">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="ffb7a-231">**Default**: Empty string</span></span>  
<span data-ttu-id="ffb7a-232">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ffb7a-233">**環境変数**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="ffb7a-234">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="ffb7a-235">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="ffb7a-236">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="ffb7a-237">HTTPS Port</span><span class="sxs-lookup"><span data-stu-id="ffb7a-237">HTTPS Port</span></span>

<span data-ttu-id="ffb7a-238">HTTPS リダイレクト ポートを設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="ffb7a-239">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ffb7a-240">**キー**: https_port **型**: *文字列*
**既定値**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="ffb7a-241">**次を使用して設定**:`UseSetting`
**環境変数**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="ffb7a-242">除外するホスティング スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="ffb7a-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="ffb7a-243">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="ffb7a-244">**キー**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="ffb7a-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="ffb7a-245">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-245">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-246">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="ffb7a-246">**Default**: Empty string</span></span>  
<span data-ttu-id="ffb7a-247">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ffb7a-248">**環境変数**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="ffb7a-249">ホスティング URL を優先する</span><span class="sxs-lookup"><span data-stu-id="ffb7a-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="ffb7a-250">`IServer` 実装で構成されているものではなく、`WebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="ffb7a-251">**キー**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="ffb7a-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="ffb7a-252">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="ffb7a-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ffb7a-253">**既定値**: true</span><span class="sxs-lookup"><span data-stu-id="ffb7a-253">**Default**: true</span></span>  
<span data-ttu-id="ffb7a-254">**次を使用して設定**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="ffb7a-255">**環境変数**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="ffb7a-256">ホスティング スタートアップを回避する</span><span class="sxs-lookup"><span data-stu-id="ffb7a-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="ffb7a-257">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="ffb7a-258">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="ffb7a-259">**キー**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="ffb7a-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="ffb7a-260">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="ffb7a-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="ffb7a-261">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="ffb7a-261">**Default**: false</span></span>  
<span data-ttu-id="ffb7a-262">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="ffb7a-263">**環境変数**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="ffb7a-264">サーバーの URL</span><span class="sxs-lookup"><span data-stu-id="ffb7a-264">Server URLs</span></span>

<span data-ttu-id="ffb7a-265">サーバーが要求をリッスンする必要があるポートとプロトコルを使用して、IP アドレスまたはホスト アドレスを示します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="ffb7a-266">**キー**: urls</span><span class="sxs-lookup"><span data-stu-id="ffb7a-266">**Key**: urls</span></span>  
<span data-ttu-id="ffb7a-267">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-267">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-268">**既定値**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="ffb7a-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="ffb7a-269">**次を使用して設定**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="ffb7a-270">**環境変数**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="ffb7a-271">サーバーが応答する必要がある URL プレフィックスのセミコロン (;) で区切られたリストに設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="ffb7a-272">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="ffb7a-273">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="ffb7a-274">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="ffb7a-275">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="ffb7a-276">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="ffb7a-277">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="ffb7a-278">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="ffb7a-278">Shutdown Timeout</span></span>

<span data-ttu-id="ffb7a-279">Web ホストがシャットダウンするまで待機する時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="ffb7a-280">**キー**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="ffb7a-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="ffb7a-281">**型**: *int*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-281">**Type**: *int*</span></span>  
<span data-ttu-id="ffb7a-282">**既定**:5</span><span class="sxs-lookup"><span data-stu-id="ffb7a-282">**Default**: 5</span></span>  
<span data-ttu-id="ffb7a-283">**次を使用して設定**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="ffb7a-284">**環境変数**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="ffb7a-285">キーは `UseSetting` で *int* を受け取りますが (`.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` など)、[UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 拡張メソッドは [TimeSpan](/dotnet/api/system.timespan) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="ffb7a-286">タイムアウト期間中、ホスティングは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="ffb7a-287">[IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="ffb7a-288">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="ffb7a-289">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="ffb7a-290">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="ffb7a-291">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="ffb7a-292">スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="ffb7a-292">Startup Assembly</span></span>

<span data-ttu-id="ffb7a-293">`Startup` クラスを検索するアセンブリを決定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="ffb7a-294">**キー**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="ffb7a-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="ffb7a-295">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-295">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-296">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="ffb7a-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="ffb7a-297">**次を使用して設定**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="ffb7a-298">**環境変数**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="ffb7a-299">名前 (`string`) または型 (`TStartup`) 別のアセンブリを参照できます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="ffb7a-300">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="ffb7a-301">Web ルート</span><span class="sxs-lookup"><span data-stu-id="ffb7a-301">Web root</span></span>

<span data-ttu-id="ffb7a-302">アプリの静的資産への相対パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="ffb7a-303">**キー**: webroot</span><span class="sxs-lookup"><span data-stu-id="ffb7a-303">**Key**: webroot</span></span>  
<span data-ttu-id="ffb7a-304">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="ffb7a-304">**Type**: *string*</span></span>  
<span data-ttu-id="ffb7a-305">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="ffb7a-306">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="ffb7a-307">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="ffb7a-308">**次を使用して設定**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="ffb7a-309">**環境変数**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="ffb7a-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="ffb7a-310">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="ffb7a-310">For more information, see:</span></span>

* [<span data-ttu-id="ffb7a-311">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="ffb7a-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="ffb7a-312">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="ffb7a-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="ffb7a-313">構成をオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="ffb7a-313">Override configuration</span></span>

<span data-ttu-id="ffb7a-314">[Configuration](xref:fundamentals/configuration/index) を使用して、Web ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="ffb7a-315">次の例では、ホスト構成はオプションで *hostsettings.json* ファイルに指定されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="ffb7a-316">*hostsettings.json* ファイルから読み込まれた構成は、コマンド ライン引数でオーバーライドされる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="ffb7a-317">(`config` の) ビルド構成は、[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) でホストを構成する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="ffb7a-318">`IWebHostBuilder` 構成はアプリの構成に追加されますが、その逆は当てはまりません&mdash;`ConfigureAppConfiguration` は `IWebHostBuilder` の構成に影響しません。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="ffb7a-319">次のように、`UseUrls` で指定された構成をオーバーライドします (最初の構成は *hostsettings.json*、2 番目の構成はコマンドライン引数です)。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="ffb7a-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ffb7a-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="ffb7a-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) は、指定された `IConfiguration` からホスト ビルダーの構成へのキーのみをコピーします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="ffb7a-322">そのため、JSON、INI、XML 設定のファイルに `reloadOnChange: true` を設定しても影響はありません。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="ffb7a-323">特定の URL でホストを実行するように指定する場合、[dotnet run](/dotnet/core/tools/dotnet-run) の実行時にコマンド プロンプトから必要な値を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="ffb7a-324">コマンドライン引数は *hostsettings.json* ファイルからの `urls` 値をオーバーライドし、サーバーはポート 8080 でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="ffb7a-325">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="ffb7a-325">Manage the host</span></span>

<span data-ttu-id="ffb7a-326">**実行**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-326">**Run**</span></span>

<span data-ttu-id="ffb7a-327">`Run` メソッドは Web アプリを起動し、ホストがシャットダウンするまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="ffb7a-328">**[開始]**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-328">**Start**</span></span>

<span data-ttu-id="ffb7a-329">`Start` メソッドを呼び出して、ブロックせずにホストを実行します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="ffb7a-330">URL のリストが `Start` メソッドに渡された場合、指定された URL でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="ffb7a-331">アプリは、便利な静的メソッドを使用し、`CreateDefaultBuilder` の構成済みの既定値を使用して、新しいホストを初期化して起動できます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="ffb7a-332">これらのメソッドは、コンソール出力なしでサーバーを起動します。その場合、[WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) を指定し、中断される (Ctrl-C/SIGINT または SIGTERM) まで待機します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="ffb7a-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="ffb7a-334">次のように `RequestDelegate` で始まります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ffb7a-335">"Hello World!" という応答を受け取るには、ブラウザで `http://localhost:5000` に対する要求を行います。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="ffb7a-336">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="ffb7a-337">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="ffb7a-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="ffb7a-339">次のように、URL と `RequestDelegate` で始まります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ffb7a-340">アプリが `http://localhost:8080` で応答する場合を除き、**Start(RequestDelegate app)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="ffb7a-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="ffb7a-342">次のように、`IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) のインスタンスを使用してルーティング ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ffb7a-343">この例では、以下のブラウザー要求を使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="ffb7a-344">要求</span><span class="sxs-lookup"><span data-stu-id="ffb7a-344">Request</span></span>                                    | <span data-ttu-id="ffb7a-345">応答</span><span class="sxs-lookup"><span data-stu-id="ffb7a-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="ffb7a-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="ffb7a-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="ffb7a-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="ffb7a-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="ffb7a-348">"ooops!" という文字列がある場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="ffb7a-349">"Uh oh!" という文字列がある場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="ffb7a-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="ffb7a-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="ffb7a-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="ffb7a-351">Hello World!</span></span>                             |

<span data-ttu-id="ffb7a-352">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="ffb7a-353">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="ffb7a-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="ffb7a-355">次のように、URL と `IRouteBuilder` のインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ffb7a-356">アプリが `http://localhost:8080` で応答する場合を除き、**Start(Action\<IRouteBuilder> routeBuilder)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="ffb7a-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="ffb7a-358">次のように、デリゲートを指定して `IApplicationBuilder` を構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ffb7a-359">"Hello World!" という応答を受け取るには、ブラウザで `http://localhost:5000` に対する要求を行います。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="ffb7a-360">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="ffb7a-361">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="ffb7a-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="ffb7a-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="ffb7a-363">次のように、URL とデリゲートを指定して `IApplicationBuilder` を構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="ffb7a-364">アプリが `http://localhost:8080` で応答する場合を除き、**StartWith(Action\<IApplicationBuilder> app)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="ffb7a-365">IWebHostEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="ffb7a-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="ffb7a-366">`IWebHostEnvironment` インターフェイスでは、アプリの Web ホスティング環境に関する情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="ffb7a-367">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して `IWebHostEnvironment` を取得します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="ffb7a-368">[規則ベースのアプローチ](xref:fundamentals/environments#environment-based-startup-class-and-methods)を使用して、環境に基づいて起動時にアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="ffb7a-369">あるいは、次のように `ConfigureServices` で使用するために `IWebHostEnvironment` を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="ffb7a-370">`IsDevelopment` 拡張メソッドに加え、`IWebHostEnvironment` は `IsStaging`、`IsProduction`、`IsEnvironment(string environmentName)` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="ffb7a-371">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ffb7a-372">処理パイプラインを設定するために、次のように `IWebHostEnvironment` サービスを `Configure` メソッドに直接挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="ffb7a-373">カスタムの[ミドルウェア](xref:fundamentals/middleware/write)を作成する際に、次のように `IWebHostEnvironment` を `Invoke` メソッドに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="ffb7a-374">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="ffb7a-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="ffb7a-375">[IHostingEnvironment インターフェイス](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)では、アプリの Web ホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="ffb7a-376">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して `IHostingEnvironment` を取得します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="ffb7a-377">[規則ベースのアプローチ](xref:fundamentals/environments#environment-based-startup-class-and-methods)を使用して、環境に基づいて起動時にアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="ffb7a-378">あるいは、次のように `ConfigureServices` で使用するために `IHostingEnvironment` を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="ffb7a-379">`IsDevelopment` 拡張メソッドに加え、`IHostingEnvironment` は `IsStaging`、`IsProduction`、`IsEnvironment(string environmentName)` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="ffb7a-380">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ffb7a-381">処理パイプラインを設定するために、次のように `IHostingEnvironment` サービスを `Configure` メソッドに直接挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="ffb7a-382">カスタムの[ミドルウェア](xref:fundamentals/middleware/write)を作成する際に、次のように `IHostingEnvironment` を `Invoke` メソッドに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="ffb7a-383">IHostApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="ffb7a-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="ffb7a-384">`IHostApplicationLifetime` では、起動後とシャットダウンのアクティビティを実行できます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="ffb7a-385">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する `Action` メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ffb7a-386">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="ffb7a-386">Cancellation Token</span></span>    | <span data-ttu-id="ffb7a-387">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="ffb7a-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="ffb7a-388">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="ffb7a-389">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ffb7a-390">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-390">All requests should be processed.</span></span> <span data-ttu-id="ffb7a-391">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="ffb7a-392">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ffb7a-393">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-393">Requests may still be processing.</span></span> <span data-ttu-id="ffb7a-394">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="ffb7a-395">`StopApplication` は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="ffb7a-396">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、`StopApplication` を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="ffb7a-397">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="ffb7a-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="ffb7a-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) は、起動後とシャットダウンのアクティビティに適用できます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="ffb7a-399">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する `Action` メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="ffb7a-400">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="ffb7a-400">Cancellation Token</span></span>    | <span data-ttu-id="ffb7a-401">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="ffb7a-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="ffb7a-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="ffb7a-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="ffb7a-403">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-403">The host has fully started.</span></span> |
| [<span data-ttu-id="ffb7a-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="ffb7a-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="ffb7a-405">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="ffb7a-406">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-406">All requests should be processed.</span></span> <span data-ttu-id="ffb7a-407">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="ffb7a-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="ffb7a-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="ffb7a-409">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="ffb7a-410">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-410">Requests may still be processing.</span></span> <span data-ttu-id="ffb7a-411">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="ffb7a-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="ffb7a-413">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、`StopApplication` を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="ffb7a-414">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="ffb7a-414">Scope validation</span></span>

<span data-ttu-id="ffb7a-415">アプリの環境が開発の場合、[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) は [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="ffb7a-416">`ValidateScopes` が `true` に設定されていると、既定のサービス プロバイダーはチェックを実行して次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="ffb7a-417">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="ffb7a-418">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="ffb7a-419">[BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="ffb7a-420">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="ffb7a-421">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="ffb7a-422">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="ffb7a-423">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="ffb7a-424">運用環境も含めて、常にスコープを検証するには、ホスト ビルダー上の [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) で [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) を構成します。</span><span class="sxs-lookup"><span data-stu-id="ffb7a-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="ffb7a-425">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ffb7a-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
