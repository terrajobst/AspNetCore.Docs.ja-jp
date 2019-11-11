---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 2cb19d251ad69ebd7d18480c14857e948c69b747
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659968"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="e7085-103">.NET Core および ASP.NET Core でのログ記録</span><span class="sxs-lookup"><span data-stu-id="e7085-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="e7085-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="e7085-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="e7085-105">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="e7085-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="e7085-106">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e7085-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7085-107">この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。</span><span class="sxs-lookup"><span data-stu-id="e7085-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="e7085-108">これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="e7085-109">Web コンソール以外のアプリで汎用ホストを使用する方法については、[ホステッド サービス](xref:fundamentals/host/hosted-services)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-109">For information about how to use the Generic Host in non-web console apps, see [Hosted services](xref:fundamentals/host/hosted-services).</span></span>

<span data-ttu-id="e7085-110">汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="e7085-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="e7085-111">ホスト以外のコードの例については、記事のこれらのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="e7085-112">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="e7085-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="e7085-113">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="e7085-113">Add providers</span></span>

<span data-ttu-id="e7085-114">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="e7085-115">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="e7085-116">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7085-117">汎用ホストを使用するアプリにプロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e7085-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="e7085-118">ホスト コンソール以外のアプリでは、`LoggerFactory` を作成するときにプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e7085-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="e7085-119">`LoggerFactory` および `AddConsole` には、`Microsoft.Extensions.Logging` 用に `using` ステートメントが必要です。</span><span class="sxs-lookup"><span data-stu-id="e7085-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="e7085-120">既定の ASP.NET Core プロジェクト テンプレートからは <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="e7085-121">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-121">Console</span></span>
* <span data-ttu-id="e7085-122">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-122">Debug</span></span>
* <span data-ttu-id="e7085-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="e7085-123">EventSource</span></span>
* <span data-ttu-id="e7085-124">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="e7085-124">EventLog (only when running on Windows)</span></span>

<span data-ttu-id="e7085-125">既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="e7085-126"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="e7085-127">プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e7085-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="e7085-128">上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="e7085-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="e7085-129">既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="e7085-130">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-130">Console</span></span>
* <span data-ttu-id="e7085-131">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-131">Debug</span></span>
* <span data-ttu-id="e7085-132">EventSource (ASP.NET Core 2.2 以降)</span><span class="sxs-lookup"><span data-stu-id="e7085-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="e7085-133">`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="e7085-134"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="e7085-135">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="e7085-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="e7085-136">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="e7085-136">Create logs</span></span>

<span data-ttu-id="e7085-137">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="e7085-138">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="e7085-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="e7085-139">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="e7085-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="e7085-140">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e7085-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="e7085-141">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="e7085-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="e7085-142">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="e7085-143">次のホスト コンソール以外のアプリの例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="e7085-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="e7085-144">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="e7085-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="e7085-145">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="e7085-146">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="e7085-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="e7085-147">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="e7085-147">Create logs in the Program class</span></span>

<span data-ttu-id="e7085-148">ASP.NET Core アプリの `Program` クラスでログを書き込むには、ホストをビルドした後に DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="e7085-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="e7085-149">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e7085-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="e7085-150">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-150">However, a separate logger can be used.</span></span> <span data-ttu-id="e7085-151">次の例では、`CreateHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="e7085-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="e7085-152">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="e7085-153">Startup クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="e7085-153">Create logs in the Startup class</span></span>

<span data-ttu-id="e7085-154">ASP.NET Core アプリの `Startup.Configure` メソッドでログを書き込むには、メソッド シグネチャに `ILogger` パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="e7085-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="e7085-155">`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e7085-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="e7085-156">`Startup` コンストラクターへのロガーの挿入はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e7085-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="e7085-157">`Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="e7085-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="e7085-158">この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。</span><span class="sxs-lookup"><span data-stu-id="e7085-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="e7085-159">DI コンテナーは、`ConfigureServices` が完了するまで設定されません。</span><span class="sxs-lookup"><span data-stu-id="e7085-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="e7085-160">ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。</span><span class="sxs-lookup"><span data-stu-id="e7085-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="e7085-161">汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="e7085-162">`ILogger<T>` に依存するサービスを構成する必要がある場合でも、コンストラクターの挿入を使用するか、ファクトリ メソッドを用意して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="e7085-163">ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e7085-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="e7085-164">たとえば、DI のサービスを使用してプロパティを設定する必要があるとします。</span><span class="sxs-lookup"><span data-stu-id="e7085-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="e7085-165">前の強調表示されているコードは、DI コンテナーで `MyService` のインスタンスが初めて作成されるときに実行される `Func` です。</span><span class="sxs-lookup"><span data-stu-id="e7085-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="e7085-166">この方法では、任意の登録済みサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e7085-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="e7085-167">Startup でログを作成する</span><span class="sxs-lookup"><span data-stu-id="e7085-167">Create logs in Startup</span></span>

<span data-ttu-id="e7085-168">`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="e7085-169">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="e7085-169">Create logs in the Program class</span></span>

<span data-ttu-id="e7085-170">`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="e7085-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="e7085-171">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e7085-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="e7085-172">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-172">However, a separate logger can be used.</span></span> <span data-ttu-id="e7085-173">次の例では、`CreateWebHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="e7085-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="e7085-174">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="e7085-175">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="e7085-175">No asynchronous logger methods</span></span>

<span data-ttu-id="e7085-176">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="e7085-177">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="e7085-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="e7085-178">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="e7085-179">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="e7085-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="e7085-180">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="e7085-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="e7085-181">構成</span><span class="sxs-lookup"><span data-stu-id="e7085-181">Configuration</span></span>

<span data-ttu-id="e7085-182">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-182">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="e7085-183">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="e7085-183">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="e7085-184">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="e7085-184">Command-line arguments.</span></span>
* <span data-ttu-id="e7085-185">環境変数。</span><span class="sxs-lookup"><span data-stu-id="e7085-185">Environment variables.</span></span>
* <span data-ttu-id="e7085-186">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="e7085-186">In-memory .NET objects.</span></span>
* <span data-ttu-id="e7085-187">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="e7085-187">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="e7085-188">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="e7085-188">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="e7085-189">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="e7085-189">Custom providers (installed or created).</span></span>

<span data-ttu-id="e7085-190">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-190">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="e7085-191">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="e7085-191">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="e7085-192">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-192">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="e7085-193">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-193">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="e7085-194">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="e7085-194">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="e7085-195">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-195">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="e7085-196">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="e7085-196">The example is for the Console provider.</span></span> <span data-ttu-id="e7085-197">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-197">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="e7085-198">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-198">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="e7085-199">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-199">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="e7085-200">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="e7085-200">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

<span data-ttu-id="e7085-201">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e7085-201">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="e7085-202">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="e7085-202">Sample logging output</span></span>

<span data-ttu-id="e7085-203">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-203">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="e7085-204">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e7085-204">Here's an example of console output:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

<span data-ttu-id="e7085-205">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="e7085-205">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="e7085-206">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-206">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="e7085-207">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApiSample" から始まります。</span><span class="sxs-lookup"><span data-stu-id="e7085-207">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="e7085-208">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="e7085-208">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="e7085-209">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="e7085-209">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="e7085-210">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。</span><span class="sxs-lookup"><span data-stu-id="e7085-210">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="e7085-211">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="e7085-211">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="e7085-212">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="e7085-212">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="e7085-213">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e7085-213">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="e7085-214">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="e7085-214">NuGet packages</span></span>

<span data-ttu-id="e7085-215">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="e7085-215">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="e7085-216">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="e7085-216">Log category</span></span>

<span data-ttu-id="e7085-217">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-217">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="e7085-218">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="e7085-218">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="e7085-219">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="e7085-219">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="e7085-220">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="e7085-220">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="e7085-221">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e7085-221">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="e7085-222">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="e7085-222">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="e7085-223">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="e7085-223">Log level</span></span>

<span data-ttu-id="e7085-224">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-224">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="e7085-225">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-225">The log level indicates the severity or importance.</span></span> <span data-ttu-id="e7085-226">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-226">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="e7085-227">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-227">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="e7085-228">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="e7085-228">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="e7085-229">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="e7085-229">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="e7085-230">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="e7085-230">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="e7085-231">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="e7085-231">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="e7085-232">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-232">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="e7085-233">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="e7085-233">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="e7085-234">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-234">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="e7085-235">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="e7085-235">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="e7085-236">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="e7085-236">Trace = 0</span></span>

  <span data-ttu-id="e7085-237">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="e7085-237">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="e7085-238">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e7085-238">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="e7085-239">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="e7085-239">*Disabled by default.*</span></span>

* <span data-ttu-id="e7085-240">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="e7085-240">Debug = 1</span></span>

  <span data-ttu-id="e7085-241">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="e7085-241">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="e7085-242">例:`Entering method Configure with flag set to true.` `Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="e7085-242">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="e7085-243">Information = 2</span><span class="sxs-lookup"><span data-stu-id="e7085-243">Information = 2</span></span>

  <span data-ttu-id="e7085-244">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="e7085-244">For tracking the general flow of the app.</span></span> <span data-ttu-id="e7085-245">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-245">These logs typically have some long-term value.</span></span> <span data-ttu-id="e7085-246">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="e7085-246">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="e7085-247">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="e7085-247">Warning = 3</span></span>

  <span data-ttu-id="e7085-248">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="e7085-248">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="e7085-249">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-249">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="e7085-250">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-250">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="e7085-251">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="e7085-251">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="e7085-252">Error = 4</span><span class="sxs-lookup"><span data-stu-id="e7085-252">Error = 4</span></span>

  <span data-ttu-id="e7085-253">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="e7085-253">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="e7085-254">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-254">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="e7085-255">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="e7085-255">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="e7085-256">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="e7085-256">Critical = 5</span></span>

  <span data-ttu-id="e7085-257">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="e7085-257">For failures that require immediate attention.</span></span> <span data-ttu-id="e7085-258">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="e7085-258">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="e7085-259">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="e7085-259">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="e7085-260">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-260">For example:</span></span>

* <span data-ttu-id="e7085-261">運用環境:</span><span class="sxs-lookup"><span data-stu-id="e7085-261">In production:</span></span>
  * <span data-ttu-id="e7085-262">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-262">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="e7085-263">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="e7085-263">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="e7085-264">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-264">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="e7085-265">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="e7085-265">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="e7085-266">開発中:</span><span class="sxs-lookup"><span data-stu-id="e7085-266">During development:</span></span>
  * <span data-ttu-id="e7085-267">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="e7085-267">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="e7085-268">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-268">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="e7085-269">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e7085-269">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="e7085-270">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="e7085-270">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="e7085-271">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="e7085-271">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="e7085-272">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-272">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a><span data-ttu-id="e7085-273">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="e7085-273">Log event ID</span></span>

<span data-ttu-id="e7085-274">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-274">Each log can specify an *event ID*.</span></span> <span data-ttu-id="e7085-275">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-275">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="e7085-276">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="e7085-276">An event ID associates a set of events.</span></span> <span data-ttu-id="e7085-277">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="e7085-277">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="e7085-278">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-278">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="e7085-279">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="e7085-279">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="e7085-280">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-280">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="e7085-281">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="e7085-281">Log message template</span></span>

<span data-ttu-id="e7085-282">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-282">Each log specifies a message template.</span></span> <span data-ttu-id="e7085-283">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-283">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="e7085-284">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-284">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="e7085-285">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="e7085-285">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="e7085-286">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-286">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="e7085-287">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-287">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="e7085-288">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="e7085-288">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="e7085-289">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-289">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="e7085-290">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-290">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="e7085-291">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e7085-291">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="e7085-292">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-292">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="e7085-293">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e7085-293">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="e7085-294">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="e7085-294">Logging exceptions</span></span>

<span data-ttu-id="e7085-295">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="e7085-295">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="e7085-296">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="e7085-296">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="e7085-297">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-297">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="e7085-298">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="e7085-298">Log filtering</span></span>

<span data-ttu-id="e7085-299">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-299">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="e7085-300">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="e7085-300">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="e7085-301">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="e7085-301">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="e7085-302">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="e7085-302">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="e7085-303">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="e7085-303">Create filter rules in configuration</span></span>

<span data-ttu-id="e7085-304">プロジェクト テンプレートのコードでは `CreateDefaultBuilder` が呼び出され、Console プロバイダーと Debug プロバイダーのログ記録が設定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-304">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="e7085-305">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-305">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="e7085-306">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="e7085-306">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="e7085-307">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="e7085-307">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="e7085-308">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-308">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="e7085-309">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="e7085-309">Filter rules in code</span></span>

<span data-ttu-id="e7085-310">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-310">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="e7085-311">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="e7085-311">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="e7085-312">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-312">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="e7085-313">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="e7085-313">How filtering rules are applied</span></span>

<span data-ttu-id="e7085-314">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="e7085-314">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="e7085-315">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="e7085-315">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="e7085-316">数値</span><span class="sxs-lookup"><span data-stu-id="e7085-316">Number</span></span> | <span data-ttu-id="e7085-317">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-317">Provider</span></span>      | <span data-ttu-id="e7085-318">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="e7085-318">Categories that begin with ...</span></span>          | <span data-ttu-id="e7085-319">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="e7085-319">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="e7085-320">1</span><span class="sxs-lookup"><span data-stu-id="e7085-320">1</span></span>      | <span data-ttu-id="e7085-321">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-321">Debug</span></span>         | <span data-ttu-id="e7085-322">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="e7085-322">All categories</span></span>                          | <span data-ttu-id="e7085-323">情報</span><span class="sxs-lookup"><span data-stu-id="e7085-323">Information</span></span>       |
| <span data-ttu-id="e7085-324">2</span><span class="sxs-lookup"><span data-stu-id="e7085-324">2</span></span>      | <span data-ttu-id="e7085-325">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-325">Console</span></span>       | <span data-ttu-id="e7085-326">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="e7085-326">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="e7085-327">警告</span><span class="sxs-lookup"><span data-stu-id="e7085-327">Warning</span></span>           |
| <span data-ttu-id="e7085-328">3</span><span class="sxs-lookup"><span data-stu-id="e7085-328">3</span></span>      | <span data-ttu-id="e7085-329">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-329">Console</span></span>       | <span data-ttu-id="e7085-330">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="e7085-330">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="e7085-331">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-331">Debug</span></span>             |
| <span data-ttu-id="e7085-332">4</span><span class="sxs-lookup"><span data-stu-id="e7085-332">4</span></span>      | <span data-ttu-id="e7085-333">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-333">Console</span></span>       | <span data-ttu-id="e7085-334">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="e7085-334">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="e7085-335">Error</span><span class="sxs-lookup"><span data-stu-id="e7085-335">Error</span></span>             |
| <span data-ttu-id="e7085-336">5</span><span class="sxs-lookup"><span data-stu-id="e7085-336">5</span></span>      | <span data-ttu-id="e7085-337">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-337">Console</span></span>       | <span data-ttu-id="e7085-338">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="e7085-338">All categories</span></span>                          | <span data-ttu-id="e7085-339">情報</span><span class="sxs-lookup"><span data-stu-id="e7085-339">Information</span></span>       |
| <span data-ttu-id="e7085-340">6</span><span class="sxs-lookup"><span data-stu-id="e7085-340">6</span></span>      | <span data-ttu-id="e7085-341">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-341">All providers</span></span> | <span data-ttu-id="e7085-342">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="e7085-342">All categories</span></span>                          | <span data-ttu-id="e7085-343">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-343">Debug</span></span>             |
| <span data-ttu-id="e7085-344">7</span><span class="sxs-lookup"><span data-stu-id="e7085-344">7</span></span>      | <span data-ttu-id="e7085-345">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-345">All providers</span></span> | <span data-ttu-id="e7085-346">システム</span><span class="sxs-lookup"><span data-stu-id="e7085-346">System</span></span>                                  | <span data-ttu-id="e7085-347">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-347">Debug</span></span>             |
| <span data-ttu-id="e7085-348">8</span><span class="sxs-lookup"><span data-stu-id="e7085-348">8</span></span>      | <span data-ttu-id="e7085-349">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-349">Debug</span></span>         | <span data-ttu-id="e7085-350">Microsoft</span><span class="sxs-lookup"><span data-stu-id="e7085-350">Microsoft</span></span>                               | <span data-ttu-id="e7085-351">トレース</span><span class="sxs-lookup"><span data-stu-id="e7085-351">Trace</span></span>             |

<span data-ttu-id="e7085-352">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-352">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="e7085-353">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-353">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="e7085-354">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-354">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="e7085-355">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-355">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="e7085-356">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-356">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="e7085-357">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-357">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="e7085-358">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-358">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="e7085-359">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-359">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="e7085-360">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-360">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="e7085-361">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-361">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="e7085-362">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="e7085-362">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="e7085-363">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-363">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="e7085-364">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-364">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="e7085-365">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-365">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="e7085-366">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="e7085-366">Rule 3 is most specific.</span></span>

<span data-ttu-id="e7085-367">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-367">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="e7085-368">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-368">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="e7085-369">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="e7085-369">Provider aliases</span></span>

<span data-ttu-id="e7085-370">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-370">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="e7085-371">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-371">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="e7085-372">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-372">Console</span></span>
* <span data-ttu-id="e7085-373">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-373">Debug</span></span>
* <span data-ttu-id="e7085-374">EventSource</span><span class="sxs-lookup"><span data-stu-id="e7085-374">EventSource</span></span>
* <span data-ttu-id="e7085-375">EventLog</span><span class="sxs-lookup"><span data-stu-id="e7085-375">EventLog</span></span>
* <span data-ttu-id="e7085-376">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e7085-376">TraceSource</span></span>
* <span data-ttu-id="e7085-377">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="e7085-377">AzureAppServicesFile</span></span>
* <span data-ttu-id="e7085-378">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="e7085-378">AzureAppServicesBlob</span></span>
* <span data-ttu-id="e7085-379">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="e7085-379">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="e7085-380">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="e7085-380">Default minimum level</span></span>

<span data-ttu-id="e7085-381">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-381">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="e7085-382">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-382">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="e7085-383">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e7085-383">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="e7085-384">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="e7085-384">Filter functions</span></span>

<span data-ttu-id="e7085-385">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-385">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="e7085-386">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-386">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="e7085-387">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-387">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="e7085-388">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="e7085-388">System categories and levels</span></span>

<span data-ttu-id="e7085-389">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="e7085-389">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="e7085-390">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="e7085-390">Category</span></span>                            | <span data-ttu-id="e7085-391">メモ</span><span class="sxs-lookup"><span data-stu-id="e7085-391">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="e7085-392">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="e7085-392">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="e7085-393">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="e7085-393">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="e7085-394">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="e7085-394">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="e7085-395">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="e7085-395">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="e7085-396">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="e7085-396">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="e7085-397">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="e7085-397">Hosts allowed.</span></span> |
| <span data-ttu-id="e7085-398">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="e7085-398">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="e7085-399">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="e7085-399">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="e7085-400">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="e7085-400">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="e7085-401">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="e7085-401">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="e7085-402">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="e7085-402">MVC and Razor diagnostics.</span></span> <span data-ttu-id="e7085-403">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="e7085-403">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="e7085-404">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="e7085-404">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="e7085-405">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="e7085-405">Route matching information.</span></span> |
| <span data-ttu-id="e7085-406">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="e7085-406">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="e7085-407">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="e7085-407">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="e7085-408">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="e7085-408">HTTPS certificate information.</span></span> |
| <span data-ttu-id="e7085-409">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="e7085-409">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="e7085-410">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="e7085-410">Files served.</span></span> |
| <span data-ttu-id="e7085-411">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="e7085-411">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="e7085-412">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="e7085-412">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="e7085-413">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="e7085-413">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="e7085-414">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="e7085-414">Log scopes</span></span>

 <span data-ttu-id="e7085-415">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-415">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="e7085-416">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-416">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="e7085-417">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-417">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="e7085-418">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="e7085-418">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="e7085-419">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-419">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="e7085-420">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="e7085-420">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="e7085-421">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7085-421">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e7085-422">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-422">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="e7085-423">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e7085-423">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="e7085-424">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e7085-424">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="e7085-425">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-425">Built-in logging providers</span></span>

<span data-ttu-id="e7085-426">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="e7085-426">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="e7085-427">コンソール</span><span class="sxs-lookup"><span data-stu-id="e7085-427">Console</span></span>](#console-provider)
* [<span data-ttu-id="e7085-428">デバッグ</span><span class="sxs-lookup"><span data-stu-id="e7085-428">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="e7085-429">EventSource</span><span class="sxs-lookup"><span data-stu-id="e7085-429">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="e7085-430">EventLog</span><span class="sxs-lookup"><span data-stu-id="e7085-430">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="e7085-431">TraceSource</span><span class="sxs-lookup"><span data-stu-id="e7085-431">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="e7085-432">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="e7085-432">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="e7085-433">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="e7085-433">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="e7085-434">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="e7085-434">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="e7085-435">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-435">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="e7085-436">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-436">Console provider</span></span>

<span data-ttu-id="e7085-437">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="e7085-437">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="e7085-438">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="e7085-438">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="e7085-439">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-439">Debug provider</span></span>

<span data-ttu-id="e7085-440">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="e7085-440">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="e7085-441">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="e7085-441">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="eventsource-provider"></a><span data-ttu-id="e7085-442">EventSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-442">EventSource provider</span></span>

<span data-ttu-id="e7085-443">ASP.NET Core 1.1.0 以降をターゲットとするアプリの場合、[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージは、イベントのトレースを実装できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-443">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="e7085-444">Windows では、[ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) を使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-444">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="e7085-445">プロバイダーはクロスプラットフォームですが、Linux または macOS 用のイベント コレクションはまだ存在せず、ツールは表示されません。</span><span class="sxs-lookup"><span data-stu-id="e7085-445">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="e7085-446">ログの収集と表示には、[PerfView utility](https://github.com/Microsoft/perfview) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e7085-446">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="e7085-447">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e7085-447">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="e7085-448">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="e7085-448">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="e7085-449">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="e7085-449">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="e7085-451">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-451">Windows EventLog provider</span></span>

<span data-ttu-id="e7085-452">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="e7085-452">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="e7085-453">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-453">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span>

### <a name="tracesource-provider"></a><span data-ttu-id="e7085-454">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-454">TraceSource provider</span></span>

<span data-ttu-id="e7085-455">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-455">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="e7085-456">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-456">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="e7085-457">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e7085-457">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="e7085-458">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-458">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="e7085-459">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-459">Azure App Service provider</span></span>

<span data-ttu-id="e7085-460">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="e7085-460">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7085-461">プロバイダー パッケージは、共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="e7085-461">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="e7085-462">プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-462">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="e7085-463">プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。</span><span class="sxs-lookup"><span data-stu-id="e7085-463">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="e7085-464">.NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-464">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7085-465">プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-465">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e7085-466">プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="e7085-466">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="e7085-467"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="e7085-467">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="e7085-468">設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。</span><span class="sxs-lookup"><span data-stu-id="e7085-468">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="e7085-469">("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)</span><span class="sxs-lookup"><span data-stu-id="e7085-469">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="e7085-470">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-470">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="e7085-471">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="e7085-471">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="e7085-472">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="e7085-472">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="e7085-473">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="e7085-473">**Application Logging (Blob)**</span></span>

<span data-ttu-id="e7085-474">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="e7085-474">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="e7085-475">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="e7085-475">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="e7085-476">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="e7085-476">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="e7085-477">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="e7085-477">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="e7085-478">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="e7085-478">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="e7085-479">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="e7085-479">Azure log streaming</span></span>

<span data-ttu-id="e7085-480">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-480">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="e7085-481">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="e7085-481">The app server</span></span>
* <span data-ttu-id="e7085-482">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="e7085-482">The web server</span></span>
* <span data-ttu-id="e7085-483">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="e7085-483">Failed request tracing</span></span>

<span data-ttu-id="e7085-484">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="e7085-484">To configure Azure log streaming:</span></span>

* <span data-ttu-id="e7085-485">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="e7085-485">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="e7085-486">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="e7085-486">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="e7085-487">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e7085-487">Choose the log **Level**.</span></span>

<span data-ttu-id="e7085-488">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="e7085-488">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="e7085-489">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="e7085-489">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="e7085-490">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="e7085-490">Azure Application Insights trace logging</span></span>

<span data-ttu-id="e7085-491">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="e7085-491">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="e7085-492">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="e7085-492">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="e7085-493">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-493">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="e7085-494">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="e7085-494">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="e7085-495">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e7085-495">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="e7085-496">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="e7085-496">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="e7085-497">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e7085-497">For more information, see the following resources:</span></span>

* [<span data-ttu-id="e7085-498">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="e7085-498">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="e7085-499">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="e7085-499">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="e7085-500">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="e7085-500">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="e7085-501">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="e7085-501">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="e7085-502">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="e7085-502">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="e7085-503">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="e7085-503">Third-party logging providers</span></span>

<span data-ttu-id="e7085-504">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="e7085-504">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="e7085-505">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="e7085-505">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="e7085-506">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="e7085-506">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="e7085-507">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="e7085-507">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="e7085-508">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="e7085-508">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="e7085-509">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="e7085-509">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="e7085-510">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="e7085-510">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="e7085-511">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="e7085-511">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="e7085-512">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="e7085-512">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="e7085-513">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="e7085-513">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="e7085-514">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="e7085-514">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="e7085-515">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="e7085-515">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="e7085-516">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="e7085-516">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="e7085-517">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="e7085-517">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="e7085-518">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e7085-518">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="e7085-519">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e7085-519">For more information, see each provider's documentation.</span></span> <span data-ttu-id="e7085-520">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="e7085-520">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7085-521">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="e7085-521">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
