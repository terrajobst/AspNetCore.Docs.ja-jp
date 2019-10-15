---
title: ASP.NET Core の Web ホスト
author: rick-anderson
description: ASP.NET Core アプリの Web ホスト (アプリの起動と有効期間の管理を担当する) について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: bc18b5490d232758b796d33a62cd8d1a7dd7289f
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007111"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="8bd5b-103">ASP.NET Core の Web ホスト</span><span class="sxs-lookup"><span data-stu-id="8bd5b-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="8bd5b-104">ASP.NET Core アプリは*ホスト*を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="8bd5b-105">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8bd5b-106">少なくとも、ホストはサーバーおよび要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="8bd5b-107">ホストでは、ログ記録、依存関係挿入、構成を設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bd5b-108">この記事では、Web ホストについて説明します。これは、下位互換性のためだけに引き続き使用可能となっています。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="8bd5b-109">すべての種類のアプリに対して、[汎用ホスト](xref:fundamentals/host/generic-host)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bd5b-110">この記事では Web ホストについて説明します。これは Web アプリをホストするためのものです。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="8bd5b-111">その他の種類のアプリでは、[汎用ホスト](xref:fundamentals/host/generic-host)をご使用ください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="8bd5b-112">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="8bd5b-112">Set up a host</span></span>

<span data-ttu-id="8bd5b-113">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) のインスタンスを使用して、ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="8bd5b-114">通常、これはアプリのエントリ ポイントの `Main` メソッドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="8bd5b-115">プロジェクト テンプレートでは、`Main` は *Program.cs* にあります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="8bd5b-116">一般的なアプリでは、[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) を呼び出してホストの設定を開始します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

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

<span data-ttu-id="8bd5b-117">`CreateDefaultBuilder` を呼び出すコードは、`CreateWebHostBuilder` という名前のメソッドに含まれ、ビルダー オブジェクトで `Run` を呼び出す `Main` のコードから分離されています。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="8bd5b-118">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/)を使用する場合は、このような分離が必要です。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="8bd5b-119">ツールでは、アプリを実行することなくホストを構成するために、デザイン時に呼び出すことができる `CreateWebHostBuilder` メソッドの検出が想定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="8bd5b-120">別の方法は、`IDesignTimeDbContextFactory` を実装することです。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="8bd5b-121">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="8bd5b-122">`CreateDefaultBuilder` では次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="8bd5b-123">アプリのホスティング構成プロバイダーを使用して [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="8bd5b-124">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="8bd5b-125">[コンテンツ ルート](xref:fundamentals/index#content-root)を、[Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="8bd5b-126">次から[ ホスト構成](#host-configuration-values)を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="8bd5b-127">`ASPNETCORE_` のプレフィックスが付いた環境変数 (たとえば、`ASPNETCORE_ENVIRONMENT`)。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="8bd5b-128">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-128">Command-line arguments.</span></span>
* <span data-ttu-id="8bd5b-129">次の順序でアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="8bd5b-130">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="8bd5b-131">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="8bd5b-132">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8bd5b-133">環境変数。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-133">Environment variables.</span></span>
  * <span data-ttu-id="8bd5b-134">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-134">Command-line arguments.</span></span>
* <span data-ttu-id="8bd5b-135">コンソールとデバッグ出力の[ログ](xref:fundamentals/logging/index)を構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="8bd5b-136">ログには、*appsettings.json* または *appsettings.{Environment}.json* ファイルのログ構成セクションで指定される[ログ フィルター](xref:fundamentals/logging/index#log-filtering)規則が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="8bd5b-137">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を使用して IIS の背後で実行されている場合は、`CreateDefaultBuilder` によってアプリのベース アドレスとポートが構成される [IIS の統合](xref:host-and-deploy/iis/index)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="8bd5b-138">IIS の統合により、[スタートアップ エラーをキャプチャする](#capture-startup-errors)アプリも構成されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="8bd5b-139">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="8bd5b-140">アプリの環境が開発の場合、[ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="8bd5b-141">詳しくは、「[スコープの検証](#scope-validation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="8bd5b-142">`CreateDefaultBuilder` によって定義された構成は、[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)、[ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)、そして [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) のその他のメソッドと拡張メソッドによってオーバーライドされ、拡張されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="8bd5b-143">以下に、いくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-143">A few examples follow:</span></span>

* <span data-ttu-id="8bd5b-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) はアプリの追加の `IConfiguration` を指定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="8bd5b-145">次の `ConfigureAppConfiguration` の呼び出しによりデリゲートが追加され、*appsettings.xml* ファイルにアプリの構成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="8bd5b-146">`ConfigureAppConfiguration` は複数回呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="8bd5b-147">この構成はホスト (たとえば、サーバーの URL や環境など) には適用されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="8bd5b-148">「[ホストの構成値](#host-configuration-values)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="8bd5b-149">次の `ConfigureLogging` の呼び出しによりデリゲートが追加され、最小ログ記録レベル ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) が [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel) に構成されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="8bd5b-150">この設定により、`CreateDefaultBuilder` で構成された *appsettings.Development.json* (`LogLevel.Debug`) および *appsettings.Production.json* (`LogLevel.Error`) の設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8bd5b-151">`ConfigureLogging` は複数回呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="8bd5b-152">次の `ConfigureKestrel` の呼び出しにより、Kestrel が `CreateDefaultBuilder` によって構成されたときに確立された既定の [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) サイズである 30,000,000 バイトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="8bd5b-153">次の [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) の呼び出しにより、Kestrel が `CreateDefaultBuilder` によって構成されたときに確立された既定の [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) サイズである 30,000,000 バイトがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="8bd5b-154">[コンテンツ ルート](xref:fundamentals/index#content-root)で、ホストが MVC ビュー ファイルなどのコンテンツ ファイルを検索する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="8bd5b-155">プロジェクトのルート フォルダーからアプリが開始された場合は、そのプロジェクトのルート フォルダーがコンテンツ ルートとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="8bd5b-156">これは、[Visual Studio](https://visualstudio.microsoft.com) と [dotnet の新しいテンプレート](/dotnet/core/tools/dotnet-new)で使用される既定です。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="8bd5b-157">アプリの構成の詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="8bd5b-158">静的な `CreateDefaultBuilder` メソッドを使用する代わりに、[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) からホストを作成する方法が ASP.NET Core 2.x でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="8bd5b-159">ホストの構成時に、[Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) および [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) メソッドを指摘することができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="8bd5b-160">`Startup` クラスが指定されている場合は、`Configure` メソッドを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="8bd5b-161">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="8bd5b-162">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="8bd5b-163">`WebHostBuilder` での `Configure` または `UseStartup` の複数回の呼び出しでは、以前の設定が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8bd5b-164">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="8bd5b-164">Host configuration values</span></span>

<span data-ttu-id="8bd5b-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) では、ホストの構成値を設定する場合に以下の方法に依存します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="8bd5b-166">`ASPNETCORE_{configurationKey}` 形式の環境変数を含む、ホスト ビルダー構成。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="8bd5b-167">たとえば、`ASPNETCORE_ENVIRONMENT` のようにします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="8bd5b-168">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) や [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) などの拡張機能 (「[構成をオーバーライドする](#override-configuration)」のセクションを参照)。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="8bd5b-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) と関連するキー。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="8bd5b-170">`UseSetting` で値を設定すると、値はその型に関係なく、文字列として設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="8bd5b-171">ホストは、最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="8bd5b-172">詳しくは、次のセクション「[構成をオーバーライドする](#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="8bd5b-173">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="8bd5b-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bd5b-174">ホストの構築時に [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) または [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) を呼び出すと、`IWebHostEnvironment.ApplicationName` プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="8bd5b-175">この値はアプリのエントリ ポイントを含むアセンブリの名前に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="8bd5b-176">値を明示的に設定するには、[WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bd5b-177">ホストの構築時に [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) または [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) が呼び出されると、[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="8bd5b-178">この値はアプリのエントリ ポイントを含むアセンブリの名前に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="8bd5b-179">値を明示的に設定するには、[WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="8bd5b-180">**キー**: applicationName</span><span class="sxs-lookup"><span data-stu-id="8bd5b-180">**Key**: applicationName</span></span>  
<span data-ttu-id="8bd5b-181">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-181">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-182">**既定値**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="8bd5b-183">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8bd5b-184">**環境変数**: `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="8bd5b-185">スタートアップ エラーをキャプチャする</span><span class="sxs-lookup"><span data-stu-id="8bd5b-185">Capture Startup Errors</span></span>

<span data-ttu-id="8bd5b-186">この設定では、スタートアップ エラーのキャプチャを制御します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="8bd5b-187">**キー**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="8bd5b-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="8bd5b-188">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="8bd5b-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8bd5b-189">**既定値**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="8bd5b-190">**次を使用して設定**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="8bd5b-191">**環境変数**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="8bd5b-192">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="8bd5b-193">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="8bd5b-194">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="8bd5b-194">Content root</span></span>

<span data-ttu-id="8bd5b-195">この設定により、ASP.NET Core でコンテンツ ファイルの検索が開始される場所が決まります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="8bd5b-196">**キー**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="8bd5b-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="8bd5b-197">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-197">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-198">**既定値**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="8bd5b-199">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="8bd5b-200">**環境変数**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="8bd5b-201">コンテンツ ルートは、[Web ルート](xref:fundamentals/index#web-root)の基本パスとしても使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="8bd5b-202">コンテンツ ルート パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="8bd5b-203">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="8bd5b-203">For more information, see:</span></span>

* [<span data-ttu-id="8bd5b-204">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="8bd5b-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="8bd5b-205">Web ルート</span><span class="sxs-lookup"><span data-stu-id="8bd5b-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="8bd5b-206">詳細なエラー</span><span class="sxs-lookup"><span data-stu-id="8bd5b-206">Detailed Errors</span></span>

<span data-ttu-id="8bd5b-207">詳細なエラーをキャプチャするかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="8bd5b-208">**キー**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="8bd5b-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="8bd5b-209">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="8bd5b-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8bd5b-210">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="8bd5b-210">**Default**: false</span></span>  
<span data-ttu-id="8bd5b-211">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8bd5b-212">**環境変数**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="8bd5b-213">有効な場合 (または<a href="#environment">環境</a>が `Development` に設定されている場合)、アプリは詳細な例外をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="8bd5b-214">環境</span><span class="sxs-lookup"><span data-stu-id="8bd5b-214">Environment</span></span>

<span data-ttu-id="8bd5b-215">アプリの環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-215">Sets the app's environment.</span></span>

<span data-ttu-id="8bd5b-216">**キー**: 環境</span><span class="sxs-lookup"><span data-stu-id="8bd5b-216">**Key**: environment</span></span>  
<span data-ttu-id="8bd5b-217">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-217">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-218">**既定値**:実稼働</span><span class="sxs-lookup"><span data-stu-id="8bd5b-218">**Default**: Production</span></span>  
<span data-ttu-id="8bd5b-219">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="8bd5b-220">**環境変数**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="8bd5b-221">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-221">The environment can be set to any value.</span></span> <span data-ttu-id="8bd5b-222">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="8bd5b-223">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-223">Values aren't case sensitive.</span></span> <span data-ttu-id="8bd5b-224">既定では、*環境*は `ASPNETCORE_ENVIRONMENT` 環境変数から読み取られます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="8bd5b-225">[Visual Studio](https://visualstudio.microsoft.com) を使用する場合、環境変数は *launchSettings.json* ファイルで設定できます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="8bd5b-226">詳細については、<xref:fundamentals/environments> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="8bd5b-227">ホスティング スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="8bd5b-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="8bd5b-228">アプリのホスティング スタートアップ アセンブリを設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="8bd5b-229">**キー**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="8bd5b-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="8bd5b-230">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-230">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-231">**既定値**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="8bd5b-231">**Default**: Empty string</span></span>  
<span data-ttu-id="8bd5b-232">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8bd5b-233">**環境変数**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="8bd5b-234">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="8bd5b-235">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="8bd5b-236">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="8bd5b-237">HTTPS Port</span><span class="sxs-lookup"><span data-stu-id="8bd5b-237">HTTPS Port</span></span>

<span data-ttu-id="8bd5b-238">HTTPS リダイレクト ポートを設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="8bd5b-239">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="8bd5b-240">**キー**: https_port **型**: *文字列*
**既定値**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="8bd5b-241">**次を使用して設定**:`UseSetting`
**環境変数**: `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="8bd5b-242">除外するホスティング スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="8bd5b-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="8bd5b-243">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="8bd5b-244">**キー**: hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="8bd5b-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="8bd5b-245">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-245">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-246">**既定値**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="8bd5b-246">**Default**: Empty string</span></span>  
<span data-ttu-id="8bd5b-247">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8bd5b-248">**環境変数**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="8bd5b-249">ホスティング URL を優先する</span><span class="sxs-lookup"><span data-stu-id="8bd5b-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="8bd5b-250">`IServer` 実装で構成されているものではなく、`WebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="8bd5b-251">**キー**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="8bd5b-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="8bd5b-252">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="8bd5b-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8bd5b-253">**既定値**: true</span><span class="sxs-lookup"><span data-stu-id="8bd5b-253">**Default**: true</span></span>  
<span data-ttu-id="8bd5b-254">**次を使用して設定**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="8bd5b-255">**環境変数**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="8bd5b-256">ホスティング スタートアップを回避する</span><span class="sxs-lookup"><span data-stu-id="8bd5b-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="8bd5b-257">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="8bd5b-258">詳細については、<xref:fundamentals/configuration/platform-specific-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="8bd5b-259">**キー**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="8bd5b-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="8bd5b-260">**型**: *ブール* (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="8bd5b-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="8bd5b-261">**既定値**: false</span><span class="sxs-lookup"><span data-stu-id="8bd5b-261">**Default**: false</span></span>  
<span data-ttu-id="8bd5b-262">**次を使用して設定**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="8bd5b-263">**環境変数**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="8bd5b-264">サーバーの URL</span><span class="sxs-lookup"><span data-stu-id="8bd5b-264">Server URLs</span></span>

<span data-ttu-id="8bd5b-265">サーバーが要求をリッスンする必要があるポートとプロトコルを使用して、IP アドレスまたはホスト アドレスを示します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="8bd5b-266">**キー**: urls</span><span class="sxs-lookup"><span data-stu-id="8bd5b-266">**Key**: urls</span></span>  
<span data-ttu-id="8bd5b-267">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-267">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-268">**既定値**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="8bd5b-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="8bd5b-269">**次を使用して設定**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="8bd5b-270">**環境変数**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="8bd5b-271">サーバーが応答する必要がある URL プレフィックスのセミコロン (;) で区切られたリストに設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="8bd5b-272">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="8bd5b-273">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="8bd5b-274">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="8bd5b-275">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="8bd5b-276">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="8bd5b-277">詳細については、<xref:fundamentals/servers/kestrel#endpoint-configuration> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="8bd5b-278">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="8bd5b-278">Shutdown Timeout</span></span>

<span data-ttu-id="8bd5b-279">Web ホストがシャットダウンするまで待機する時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="8bd5b-280">**キー**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="8bd5b-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="8bd5b-281">**型**: *int*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-281">**Type**: *int*</span></span>  
<span data-ttu-id="8bd5b-282">**既定値**:5</span><span class="sxs-lookup"><span data-stu-id="8bd5b-282">**Default**: 5</span></span>  
<span data-ttu-id="8bd5b-283">**次を使用して設定**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="8bd5b-284">**環境変数**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="8bd5b-285">キーは `UseSetting` で *int* を受け取りますが (`.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")` など)、[UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 拡張メソッドは [TimeSpan](/dotnet/api/system.timespan) を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="8bd5b-286">タイムアウト期間中、ホスティングは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="8bd5b-287">[IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="8bd5b-288">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="8bd5b-289">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="8bd5b-290">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="8bd5b-291">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="8bd5b-292">スタートアップ アセンブリ</span><span class="sxs-lookup"><span data-stu-id="8bd5b-292">Startup Assembly</span></span>

<span data-ttu-id="8bd5b-293">`Startup` クラスを検索するアセンブリを決定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="8bd5b-294">**キー**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="8bd5b-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="8bd5b-295">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-295">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-296">**既定値**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="8bd5b-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="8bd5b-297">**次を使用して設定**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="8bd5b-298">**環境変数**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="8bd5b-299">名前 (`string`) または型 (`TStartup`) 別のアセンブリを参照できます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="8bd5b-300">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="8bd5b-301">Web ルート</span><span class="sxs-lookup"><span data-stu-id="8bd5b-301">Web root</span></span>

<span data-ttu-id="8bd5b-302">アプリの静的資産への相対パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="8bd5b-303">**キー**: webroot</span><span class="sxs-lookup"><span data-stu-id="8bd5b-303">**Key**: webroot</span></span>  
<span data-ttu-id="8bd5b-304">**型**: *文字列*</span><span class="sxs-lookup"><span data-stu-id="8bd5b-304">**Type**: *string*</span></span>  
<span data-ttu-id="8bd5b-305">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="8bd5b-306">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="8bd5b-307">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="8bd5b-308">**次を使用して設定**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="8bd5b-309">**環境変数**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="8bd5b-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="8bd5b-310">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="8bd5b-310">For more information, see:</span></span>

* [<span data-ttu-id="8bd5b-311">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="8bd5b-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="8bd5b-312">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="8bd5b-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="8bd5b-313">構成をオーバーライドする</span><span class="sxs-lookup"><span data-stu-id="8bd5b-313">Override configuration</span></span>

<span data-ttu-id="8bd5b-314">[Configuration](xref:fundamentals/configuration/index) を使用して、Web ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="8bd5b-315">次の例では、ホスト構成はオプションで *hostsettings.json* ファイルに指定されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="8bd5b-316">*hostsettings.json* ファイルから読み込まれた構成は、コマンド ライン引数でオーバーライドされる場合があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="8bd5b-317">(`config` の) ビルド構成は、[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) でホストを構成する場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="8bd5b-318">`IWebHostBuilder` 構成はアプリの構成に追加されますが、その逆は当てはまりません&mdash;`ConfigureAppConfiguration` は `IWebHostBuilder` の構成に影響しません。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="8bd5b-319">次のように、`UseUrls` で指定された構成をオーバーライドします (最初の構成は *hostsettings.json*、2 番目の構成はコマンドライン引数です)。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

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

<span data-ttu-id="8bd5b-320">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8bd5b-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="8bd5b-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 拡張メソッドでは、現在、`GetSection` (たとえば、`.UseConfiguration(Configuration.GetSection("section"))` によって返される構成セクションを解析することはできません。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-321">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="8bd5b-322">`GetSection` メソッドは要求されたセクションに対する構成キーをフィルター処理しますが、キーのセクション名はそのままになります (たとえば、`section:urls`、`section:environment`)。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-322">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="8bd5b-323">`UseConfiguration` メソッドは `WebHostBuilder` と一致するキーを予測します (たとえば、`urls`、`environment`)。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-323">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="8bd5b-324">キーにセクション名があると、セクションの値でホストを構成できなくなります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-324">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="8bd5b-325">この問題は、将来のリリースで対処される予定です。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-325">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="8bd5b-326">詳細と回避策については、「[Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839)」 (WebHostBuilder.UseConfiguration に構成セクションを渡すときにフル キーを使用する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-326">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>
>
> <span data-ttu-id="8bd5b-327">`UseConfiguration` は、指定された `IConfiguration` からホスト ビルダーの構成へのキーのみをコピーします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-327">`UseConfiguration` only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="8bd5b-328">そのため、JSON、INI、XML 設定のファイルに `reloadOnChange: true` を設定しても影響はありません。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-328">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="8bd5b-329">特定の URL でホストを実行するように指定する場合、[dotnet run](/dotnet/core/tools/dotnet-run) の実行時にコマンド プロンプトから必要な値を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-329">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="8bd5b-330">コマンドライン引数は *hostsettings.json* ファイルからの `urls` 値をオーバーライドし、サーバーはポート 8080 でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-330">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="8bd5b-331">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="8bd5b-331">Manage the host</span></span>

<span data-ttu-id="8bd5b-332">**実行**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-332">**Run**</span></span>

<span data-ttu-id="8bd5b-333">`Run` メソッドは Web アプリを起動し、ホストがシャットダウンするまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-333">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="8bd5b-334">**[開始]**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-334">**Start**</span></span>

<span data-ttu-id="8bd5b-335">`Start` メソッドを呼び出して、ブロックせずにホストを実行します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-335">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="8bd5b-336">URL のリストが `Start` メソッドに渡された場合、指定された URL でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-336">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

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

<span data-ttu-id="8bd5b-337">アプリは、便利な静的メソッドを使用し、`CreateDefaultBuilder` の構成済みの既定値を使用して、新しいホストを初期化して起動できます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-337">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="8bd5b-338">これらのメソッドは、コンソール出力なしでサーバーを起動します。その場合、[WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) を指定し、中断される (Ctrl-C/SIGINT または SIGTERM) まで待機します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-338">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="8bd5b-339">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-339">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="8bd5b-340">次のように `RequestDelegate` で始まります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-340">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="8bd5b-341">"Hello World!" という応答を受け取るには、ブラウザで `http://localhost:5000` に対する要求を行います。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-341">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="8bd5b-342">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-342">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="8bd5b-343">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-343">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="8bd5b-344">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-344">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="8bd5b-345">次のように、URL と `RequestDelegate` で始まります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-345">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="8bd5b-346">アプリが `http://localhost:8080` で応答する場合を除き、**Start(RequestDelegate app)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-346">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="8bd5b-347">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-347">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="8bd5b-348">次のように、`IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) のインスタンスを使用してルーティング ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-348">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

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

<span data-ttu-id="8bd5b-349">この例では、以下のブラウザー要求を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-349">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="8bd5b-350">要求</span><span class="sxs-lookup"><span data-stu-id="8bd5b-350">Request</span></span>                                    | <span data-ttu-id="8bd5b-351">応答</span><span class="sxs-lookup"><span data-stu-id="8bd5b-351">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="8bd5b-352">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="8bd5b-352">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="8bd5b-353">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="8bd5b-353">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="8bd5b-354">"ooops!" という文字列がある場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-354">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="8bd5b-355">"Uh oh!" という文字列がある場合は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-355">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="8bd5b-356">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="8bd5b-356">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="8bd5b-357">Hello World!</span><span class="sxs-lookup"><span data-stu-id="8bd5b-357">Hello World!</span></span>                             |

<span data-ttu-id="8bd5b-358">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-358">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="8bd5b-359">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-359">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="8bd5b-360">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-360">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="8bd5b-361">次のように、URL と `IRouteBuilder` のインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-361">Use a URL and an instance of `IRouteBuilder`:</span></span>

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

<span data-ttu-id="8bd5b-362">アプリが `http://localhost:8080` で応答する場合を除き、**Start(Action\<IRouteBuilder> routeBuilder)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-362">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="8bd5b-363">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-363">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="8bd5b-364">次のように、デリゲートを指定して `IApplicationBuilder` を構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-364">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="8bd5b-365">"Hello World!" という応答を受け取るには、ブラウザで `http://localhost:5000` に対する要求を行います。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-365">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="8bd5b-366">`WaitForShutdown` は、中断される (Ctrl-C/SIGINT または SIGTERM) までブロックします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-366">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="8bd5b-367">アプリは `Console.WriteLine` メッセージを表示し、キー入力が終わるまで待機します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-367">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="8bd5b-368">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="8bd5b-368">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="8bd5b-369">次のように、URL とデリゲートを指定して `IApplicationBuilder` を構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-369">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

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

<span data-ttu-id="8bd5b-370">アプリが `http://localhost:8080` で応答する場合を除き、**StartWith(Action\<IApplicationBuilder> app)** と同じ結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-370">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="8bd5b-371">IWebHostEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="8bd5b-371">IWebHostEnvironment interface</span></span>

<span data-ttu-id="8bd5b-372">`IWebHostEnvironment` インターフェイスでは、アプリの Web ホスティング環境に関する情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-372">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="8bd5b-373">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して `IWebHostEnvironment` を取得します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-373">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="8bd5b-374">[規則ベースのアプローチ](xref:fundamentals/environments#environment-based-startup-class-and-methods)を使用して、環境に基づいて起動時にアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-374">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="8bd5b-375">あるいは、次のように `ConfigureServices` で使用するために `IWebHostEnvironment` を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-375">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="8bd5b-376">`IsDevelopment` 拡張メソッドに加え、`IWebHostEnvironment` は `IsStaging`、`IsProduction`、`IsEnvironment(string environmentName)` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-376">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="8bd5b-377">詳細については、<xref:fundamentals/environments> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-377">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8bd5b-378">処理パイプラインを設定するために、次のように `IWebHostEnvironment` サービスを `Configure` メソッドに直接挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-378">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="8bd5b-379">カスタムの[ミドルウェア](xref:fundamentals/middleware/write)を作成する際に、次のように `IWebHostEnvironment` を `Invoke` メソッドに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-379">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="8bd5b-380">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="8bd5b-380">IHostingEnvironment interface</span></span>

<span data-ttu-id="8bd5b-381">[IHostingEnvironment インターフェイス](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)では、アプリの Web ホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-381">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="8bd5b-382">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して `IHostingEnvironment` を取得します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-382">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="8bd5b-383">[規則ベースのアプローチ](xref:fundamentals/environments#environment-based-startup-class-and-methods)を使用して、環境に基づいて起動時にアプリを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-383">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="8bd5b-384">あるいは、次のように `ConfigureServices` で使用するために `IHostingEnvironment` を `Startup` コンストラクターに挿入します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-384">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

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
> <span data-ttu-id="8bd5b-385">`IsDevelopment` 拡張メソッドに加え、`IHostingEnvironment` は `IsStaging`、`IsProduction`、`IsEnvironment(string environmentName)` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-385">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="8bd5b-386">詳細については、<xref:fundamentals/environments> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-386">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8bd5b-387">処理パイプラインを設定するために、次のように `IHostingEnvironment` サービスを `Configure` メソッドに直接挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-387">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

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

<span data-ttu-id="8bd5b-388">カスタムの[ミドルウェア](xref:fundamentals/middleware/write)を作成する際に、次のように `IHostingEnvironment` を `Invoke` メソッドに挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-388">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

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

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="8bd5b-389">IHostApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="8bd5b-389">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="8bd5b-390">`IHostApplicationLifetime` では、起動後とシャットダウンのアクティビティを実行できます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-390">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="8bd5b-391">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する `Action` メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-391">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="8bd5b-392">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="8bd5b-392">Cancellation Token</span></span>    | <span data-ttu-id="8bd5b-393">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="8bd5b-393">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="8bd5b-394">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-394">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="8bd5b-395">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-395">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="8bd5b-396">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-396">All requests should be processed.</span></span> <span data-ttu-id="8bd5b-397">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-397">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="8bd5b-398">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-398">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="8bd5b-399">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-399">Requests may still be processing.</span></span> <span data-ttu-id="8bd5b-400">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-400">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="8bd5b-401">`StopApplication` は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-401">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="8bd5b-402">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、`StopApplication` を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-402">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="8bd5b-403">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="8bd5b-403">IApplicationLifetime interface</span></span>

<span data-ttu-id="8bd5b-404">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) は、起動後とシャットダウンのアクティビティに適用できます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-404">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="8bd5b-405">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する `Action` メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-405">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="8bd5b-406">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="8bd5b-406">Cancellation Token</span></span>    | <span data-ttu-id="8bd5b-407">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="8bd5b-407">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="8bd5b-408">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="8bd5b-408">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="8bd5b-409">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-409">The host has fully started.</span></span> |
| [<span data-ttu-id="8bd5b-410">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="8bd5b-410">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="8bd5b-411">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-411">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="8bd5b-412">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-412">All requests should be processed.</span></span> <span data-ttu-id="8bd5b-413">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-413">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="8bd5b-414">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="8bd5b-414">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="8bd5b-415">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-415">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="8bd5b-416">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-416">Requests may still be processing.</span></span> <span data-ttu-id="8bd5b-417">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-417">Shutdown blocks until this event completes.</span></span> |

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

<span data-ttu-id="8bd5b-418">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-418">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="8bd5b-419">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、`StopApplication` を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-419">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="8bd5b-420">スコープの検証</span><span class="sxs-lookup"><span data-stu-id="8bd5b-420">Scope validation</span></span>

<span data-ttu-id="8bd5b-421">アプリの環境が開発の場合、[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) は [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-421">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="8bd5b-422">`ValidateScopes` が `true` に設定されていると、既定のサービス プロバイダーはチェックを実行して次のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-422">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="8bd5b-423">スコープ サービスが、ルート サービス プロバイダーによって直接的または間接的に解決されない。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-423">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="8bd5b-424">スコープ サービスが、シングルトンに直接または間接に挿入されない。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-424">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="8bd5b-425">[BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) が呼び出されると、ルート サービス プロバイダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-425">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="8bd5b-426">ルート サービス プロバイダーの有効期間は、プロバイダーがアプリで開始されるとアプリとサーバーの有効期間に対応し、アプリのシャットダウンには破棄されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-426">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="8bd5b-427">スコープ サービスは、それを作成したコンテナーによって破棄されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-427">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="8bd5b-428">ルート コンテナーにスコープ サービスが作成されると、サービスはアプリ/サーバーのシャットダウン時に、ルート コンテナーによってのみ破棄されるため、サービスの有効期間は実質的にシングルトンに昇格されます。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-428">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="8bd5b-429">`BuildServiceProvider` が呼び出されると、サービス スコープの検証がこれらの状況をキャッチします。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-429">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="8bd5b-430">運用環境も含めて、常にスコープを検証するには、ホスト ビルダー上の [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) で [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) を構成します。</span><span class="sxs-lookup"><span data-stu-id="8bd5b-430">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="8bd5b-431">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8bd5b-431">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
