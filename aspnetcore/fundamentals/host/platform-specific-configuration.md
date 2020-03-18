---
title: ASP.NET Core でホスティング スタートアップ アセンブリを使用する
author: rick-anderson
description: IHostingStartup 実装を使用して、外部アセンブリから ASP.NET Core アプリを拡張する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: 71fd5cf1934b5374e0a393e055db23b98c03b62f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647912"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="76b33-103">ASP.NET Core でホスティング スタートアップ アセンブリを使用する</span><span class="sxs-lookup"><span data-stu-id="76b33-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="76b33-104">作成者: [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="76b33-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="76b33-105"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (ホスティング スタートアップ) の実装を使うと、外部アセンブリからの起動時にアプリに拡張機能を追加できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="76b33-106">たとえば、外部ライブラリではホスティング スタートアップ実装を使用して、追加の構成プロバイダーまたはサービスをアプリに提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="76b33-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="76b33-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="76b33-108">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="76b33-108">HostingStartup attribute</span></span>

<span data-ttu-id="76b33-109">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性は、実行時にアクティブ化されるホスティング スタートアップ アセンブリが存在することを示しています。</span><span class="sxs-lookup"><span data-stu-id="76b33-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="76b33-110">入力アセンブリまたは `Startup` クラスを含むアセンブリは、`HostingStartup` 属性について自動的にスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="76b33-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-111">`HostingStartup` 属性を検索するアセンブリの一覧は、[WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 内の構成からランタイム時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="76b33-112">検出から除外されるアセンブリの一覧は、[WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="76b33-113">次に例では、ホスティング スタートアップ アセンブリの名前空間は `StartupEnhancement` となります。</span><span class="sxs-lookup"><span data-stu-id="76b33-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="76b33-114">ホスティング スタートアップ コードを含むクラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="76b33-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="76b33-115">`HostingStartup` 属性は、通常、ホスティング スタートアップ アセンブリの `IHostingStartup` 実装クラス ファイル内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="76b33-116">読み込まれたホスティング スタートアップ アセンブリを見つける</span><span class="sxs-lookup"><span data-stu-id="76b33-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="76b33-117">読み込まれたホスティング スタートアップ アセンブリを検出するには、ログ記録を有効にしてアプリのログを確認します。</span><span class="sxs-lookup"><span data-stu-id="76b33-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="76b33-118">アセンブリの読み込み時に発生したエラーがログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="76b33-119">読み込まれたホスティング スタートアップ アセンブリは、デバッグ レベルでログ記録され、すべてのエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="76b33-120">ホスティング スタートアップ アセンブリの自動読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="76b33-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="76b33-121">ホスティング スタートアップ アセンブリの自動読み込みを無効にするには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="76b33-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="76b33-122">すべてのホスティング スタートアップ アセンブリの読み込みを回避するには、次のいずれかを `true` または `1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="76b33-123">[ホスティング スタートアップを回避する] ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="76b33-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="76b33-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="76b33-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="76b33-125">特定のホスティング スタートアップ アセンブリの読み込みを回避するには、次に示すいずれかを、起動時に除外するホスティング スタートアップ アセンブリを示すセミコロンで区切られた文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="76b33-126">[Hosting Startup Exclude Assemblies]\(除外するホスティング スタートアップ アセンブリ\) ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="76b33-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="76b33-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="76b33-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="76b33-128">ホスト構成設定と環境変数が両方とも設定されている場合は、ホスト設定によって動作が制御されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="76b33-129">ホスト設定または環境変数を使用してホスティング スタートアップ アセンブリを無効にすると、アセンブリがグローバルに無効になり、場合によってはアプリのいくつかの属性も無効になります。</span><span class="sxs-lookup"><span data-stu-id="76b33-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="76b33-130">Project</span><span class="sxs-lookup"><span data-stu-id="76b33-130">Project</span></span>

<span data-ttu-id="76b33-131">次のいずれかの種類のプロジェクトを使用して、ホスティング スタートアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="76b33-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="76b33-132">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="76b33-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="76b33-133">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="76b33-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="76b33-134">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="76b33-134">Class library</span></span>

<span data-ttu-id="76b33-135">クラス ライブラリでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="76b33-136">このライブラリには `HostingStartup` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="76b33-137">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)には、Razor Pages アプリ、*HostingStartupApp*、クラス ライブラリ、および *HostingStartupLibrary* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="76b33-138">クラス ライブラリには次のものが含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-138">The class library:</span></span>

* <span data-ttu-id="76b33-139">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="76b33-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="76b33-140">`ServiceKeyInjection` では、メモリ内の構成プロバイダー ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) を使用して、サービスの文字列のペアがアプリの構成に追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="76b33-141">ホスティング スタートアップの名前空間とクラスを識別する `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="76b33-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="76b33-142">`ServiceKeyInjection` クラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="76b33-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="76b33-144">アプリのインデックス ページでは、クラス ライブラリのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="76b33-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="76b33-146">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) には、別のホスティング スタートアップ *HostingStartupPackage* を提供する NuGet パッケージ プロジェクトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="76b33-147">パッケージには、前に説明したクラス ライブラリと同じ特性があります。</span><span class="sxs-lookup"><span data-stu-id="76b33-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="76b33-148">パッケージには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-148">The package:</span></span>

* <span data-ttu-id="76b33-149">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="76b33-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="76b33-150">`ServiceKeyInjection` では、アプリの構成にサービス文字列のペアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="76b33-151">`HostingStartup`属性。</span><span class="sxs-lookup"><span data-stu-id="76b33-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="76b33-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="76b33-153">アプリのインデックス ページでは、パッケージのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="76b33-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="76b33-155">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="76b33-155">Console app without an entry point</span></span>

<span data-ttu-id="76b33-156">*このアプローチは、.NET Core アプリでのみ利用でき、.NET Framework では利用できません。*</span><span class="sxs-lookup"><span data-stu-id="76b33-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="76b33-157">`HostingStartup` 属性を含むエントリ ポイントがないコンソール アプリ内では、アクティブ化に関するコンパイル時参照を必要としない動的ホスティング スタートアップ拡張機能を指定できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-158">コンソール アプリを発行すると、ランタイム ストアから使用できるホスティング スタートアップ アセンブリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="76b33-159">このプロセスにおいてエントリ ポイントのないコンソール アプリが使用される理由は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76b33-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="76b33-160">ホスティング スタートアップ アセンブリ内のホスティング スタートアップを使用するには、依存関係ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="76b33-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="76b33-161">依存関係ファイルは、ライブラリではなくアプリを発行することによって生成される、実行可能なアプリ資産です。</span><span class="sxs-lookup"><span data-stu-id="76b33-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="76b33-162">[ランタイム パッケージ ストア](/dotnet/core/deploying/runtime-store) にライブラリを直接追加することはできません。それには、共有ランタイムを対象とする実行可能なプロジェクトが必要です。</span><span class="sxs-lookup"><span data-stu-id="76b33-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="76b33-163">動的ホスティング スタートアップを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="76b33-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="76b33-164">次のようなエントリ ポイントがないコンソール アプリからホスティング スタートアップ アセンブリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="76b33-165">`IHostingStartup` の実装を含むクラスを含んでいる。</span><span class="sxs-lookup"><span data-stu-id="76b33-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="76b33-166">`IHostingStartup` 実装クラスを識別するための [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="76b33-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="76b33-167">ホスティング スタートアップの依存関係を取得するには、コンソール アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="76b33-168">コンソール アプリを発行すると、その結果として、依存関係ファイルから未使用の依存関係が切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="76b33-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="76b33-169">ホスティング スタートアップ アセンブリのランタイムの場所を設定するために、依存関係ファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="76b33-170">ホスティング スタートアップ アセンブリとその依存関係ファイルは、ランタイム パッケージ ストアに配置されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="76b33-171">ホスティング スタートアップ アセンブリとその依存関係ファイルを検出する場合、それらは環境変数のペアに記載されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="76b33-172">コンソール アセンブリでは、[Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) パッケージが参照されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="76b33-173">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を使うと、<xref:Microsoft.AspNetCore.Hosting.IWebHost> のビルド時に、読み込みと実行のための `IHostingStartup` の実装としてクラスが識別されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="76b33-174">次の例では、名前空間は `StartupEnhancement`、クラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="76b33-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="76b33-175">クラスは `IHostingStartup` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="76b33-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="76b33-176">このクラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="76b33-177">ホスティング スタートアップ アセンブリ内の `IHostingStartup.Configure` は、ユーザー コード内の `Startup.Configure` よりも前にランタイムによって呼び出されます。このため、ホスティング スタートアップ アセンブリによって提供されるすべての構成をユーザー コードで上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="76b33-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="76b33-178">`IHostingStartup` プロジェクトのビルド時に、依存関係ファイル ( *.deps.json*) によってアセンブリの `runtime` の場所が *bin* フォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="76b33-179">ファイルの一部のみが示されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-179">Only part of the file is shown.</span></span> <span data-ttu-id="76b33-180">例のアセンブリ名は `StartupEnhancement` です。</span><span class="sxs-lookup"><span data-stu-id="76b33-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="76b33-181">ホスティング スタートアップによって指定される構成</span><span class="sxs-lookup"><span data-stu-id="76b33-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="76b33-182">ホスティング スタートアップの構成、アプリの構成のどちらを優先させるかによって、2 つの異なる方法で構成を処理できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="76b33-183">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行した後で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="76b33-184">この方法を使うと、ホスティング スタートアップの構成の方がアプリの構成よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="76b33-185">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行する前に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="76b33-186">この方法を使うと、アプリの構成値の方がホスティング スタートアップにより指定されるものよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="76b33-187">ホスティング スタートアップ アセンブリを指定する</span><span class="sxs-lookup"><span data-stu-id="76b33-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="76b33-188">クラス ライブラリまたはコンソール アプリのいずれかで提供されるホスティング スタートアップの場合は、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数内にホスティング スタートアップ アセンブリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="76b33-189">環境変数はアセンブリのセミコロン区切りのリストです。</span><span class="sxs-lookup"><span data-stu-id="76b33-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="76b33-190">ホスティング スタートアップ アセンブリのみが、`HostingStartup` 属性に対してスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="76b33-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-191">サンプル アプリ *HostingStartupApp* では、前述したホスティング スタートアップを検出するために、環境変数が次の値に設定されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="76b33-192">また、ホスティング スタートアップ アセンブリは、[ホスティング スタートアップ アセンブリ] のホスト構成設定を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="76b33-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="76b33-193">複数のホスティング スタートアップ アセンブリが存在する場合、それらの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドは、アセンブリが一覧表示されている順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="76b33-194">アクティベーション</span><span class="sxs-lookup"><span data-stu-id="76b33-194">Activation</span></span>

<span data-ttu-id="76b33-195">ホスティング スタートアップのアクティブ化のオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76b33-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="76b33-196">[ランタイム ストア](#runtime-store) &ndash; アクティブ化では、アクティブ化に関するコンパイル時参照を必要としません。</span><span class="sxs-lookup"><span data-stu-id="76b33-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="76b33-197">サンプル アプリでは、ホスティング スタートアップ アセンブリおよび依存関係のファイルが *deployment* フォルダーに配置されています。これにより、複数のコンピューターから成る環境でのホスティング スタートアップの配置が容易になります。</span><span class="sxs-lookup"><span data-stu-id="76b33-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="76b33-198">*deployment* フォルダーには、ホスティングスタートアップが有効になるように配置システム上で環境変数を作成および変更する PowerShell スクリプトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="76b33-199">アクティブ化で必須のコンパイル時参照</span><span class="sxs-lookup"><span data-stu-id="76b33-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="76b33-200">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="76b33-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="76b33-201">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="76b33-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="76b33-202">ランタイム ストア</span><span class="sxs-lookup"><span data-stu-id="76b33-202">Runtime store</span></span>

<span data-ttu-id="76b33-203">ホスティング スタートアップ実装は、[ランタイム ストア](/dotnet/core/deploying/runtime-store)に置かれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="76b33-204">アセンブリへのコンパイル時参照は、機能強化されたアプリで必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="76b33-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="76b33-205">ホスティング スタートアップをビルドした後、プロジェクトのマニフェスト ファイルと [dotnet store](/dotnet/core/tools/dotnet-store) コマンドの使用によってランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="76b33-206">サンプル アプリ (*RuntimeStore* プロジェクト) では、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="76b33-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="76b33-207">ランタイムでランタイム ストアを検出できるように、ランタイム ストアの場所を環境変数 `DOTNET_SHARED_STORE` に追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="76b33-208">**ホスティング スタートアップの依存関係ファイルを変更して配置する**</span><span class="sxs-lookup"><span data-stu-id="76b33-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="76b33-209">拡張機能へのパッケージ参照なしで拡張機能をアクティブ化するには、`additionalDeps` を使用してランタイムに追加の依存関係を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="76b33-210">`additionalDeps` を使用すると、次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="76b33-211">スタートアップ時にアプリの独自の *.deps.json* ファイルとマージさせる追加の *.deps.json* ファイルのセットを指定することで、アプリのライブラリのグラフを拡張します。</span><span class="sxs-lookup"><span data-stu-id="76b33-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="76b33-212">ホスティング スタートアップ アセンブリを検出可能および読み込み可能にします。</span><span class="sxs-lookup"><span data-stu-id="76b33-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="76b33-213">追加の依存関係ファイルを生成するために推奨される方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76b33-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="76b33-214">前のセクションで参照したランタイム ストアのマニフェスト ファイルに対して `dotnet publish` を実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="76b33-215">ライブラリと、結果として得られる *deps.json* ファイルの `runtime` セクションから、マニフェスト参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="76b33-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="76b33-216">プロジェクトの例では、`targets` と `libraries` セクションから `store.manifest/1.0.0` プロパティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="76b33-217">次の場所に *.deps.json* ファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="76b33-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="76b33-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; `DOTNET_ADDITIONAL_DEPS` 環境変数に追加される場所。</span><span class="sxs-lookup"><span data-stu-id="76b33-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="76b33-219">`{SHARED FRAMEWORK NAME}` &ndash; この追加の依存関係ファイルに必要な共有フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="76b33-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="76b33-220">`{SHARED FRAMEWORK VERSION}` &ndash; 共有フレームワークの最小バージョン。</span><span class="sxs-lookup"><span data-stu-id="76b33-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="76b33-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; 拡張機能のアセンブリ名。</span><span class="sxs-lookup"><span data-stu-id="76b33-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="76b33-222">サンプル アプリ (*RuntimeStore* プロジェクト) では、追加の依存関係ファイルは以下の場所に配置されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="76b33-223">ランタイムでランタイム ストアの場所を検出できるように、追加の依存関係ファイルの場所が環境変数 `DOTNET_ADDITIONAL_DEPS` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="76b33-224">サンプル アプリ (*RuntimeStore* プロジェクト) では、[PowerShell](/powershell/scripting/powershell-scripting) スクリプトを使用してランタイム ストアのビルドと追加の依存関係ファイルの生成を行います。</span><span class="sxs-lookup"><span data-stu-id="76b33-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="76b33-225">さまざまなオペレーティング システムの環境変数を設定する方法の例については、[複数の環境の使用](xref:fundamentals/environments)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="76b33-226">**配置**</span><span class="sxs-lookup"><span data-stu-id="76b33-226">**Deployment**</span></span>

<span data-ttu-id="76b33-227">サンプル アプリでは、複数のコンピューターから成る環境へのホスティング スタートアップの配置を容易にするために、発行された出力内に、次を含む *deployment* フォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="76b33-228">ホスティング スタートアップのランタイム ストア。</span><span class="sxs-lookup"><span data-stu-id="76b33-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="76b33-229">ホスティング スタートアップの依存関係ファイル。</span><span class="sxs-lookup"><span data-stu-id="76b33-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="76b33-230">ホスティング スタートアップのアクティブ化をサポートできるように、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE`、`DOTNET_ADDITIONAL_DEPS` を作成および変更する PowerShell スクリプト。</span><span class="sxs-lookup"><span data-stu-id="76b33-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="76b33-231">このスクリプトは、配置システム上の PowerShell 管理用コマンド プロンプトから実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="76b33-232">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="76b33-232">NuGet package</span></span>

<span data-ttu-id="76b33-233">NuGet パッケージでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="76b33-234">このパッケージには `HostingStartup` 属性があります。</span><span class="sxs-lookup"><span data-stu-id="76b33-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-235">このパッケージで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="76b33-236">機能強化されたアプリのプロジェクト ファイルでは、アプリのプロジェクト ファイル内のホスティング スタートアップに対するパッケージ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="76b33-237">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="76b33-238">このアプローチは、[nuget.org](https://www.nuget.org/) に発行されるホスティング スタートアップ アセンブリ パッケージに適用されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="76b33-239">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="76b33-240">NuGet パッケージとランタイム ストアの詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="76b33-241">クロスプラットフォーム ツールを使用して NuGet パッケージを作成する方法</span><span class="sxs-lookup"><span data-stu-id="76b33-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="76b33-242">パッケージを公開する</span><span class="sxs-lookup"><span data-stu-id="76b33-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="76b33-243">ランタイム パッケージ ストア</span><span class="sxs-lookup"><span data-stu-id="76b33-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="76b33-244">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="76b33-244">Project bin folder</span></span>

<span data-ttu-id="76b33-245">ホスティング スタートアップの拡張機能は、機能強化されたアプリ内の *bin* 配置アセンブリによって提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="76b33-246">このアセンブリで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="76b33-247">機能強化されたアプリのプロジェクト ファイルでは、ホスティング スタートアップへのアセンブリ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="76b33-248">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="76b33-249">この手法は、展開シナリオによって、ホスティング スタートアップのアセンブリ ( *.dll* ファイル) に対してコンパイル時参照を作成し、次のいずれかにそのアセンブリを移動させることが要請される場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="76b33-250">使用するプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="76b33-250">The consuming project.</span></span>
  * <span data-ttu-id="76b33-251">使用するプロジェクトでアクセスできる場所。</span><span class="sxs-lookup"><span data-stu-id="76b33-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="76b33-252">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="76b33-253">.NET Framework を対象にするとき、アセンブリは既定の読み込みコンテキストで読み込み可能です。これは、.NET Framework の場合、アセンブリが次のいずれかの場所にあることを意味します。</span><span class="sxs-lookup"><span data-stu-id="76b33-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="76b33-254">アプリケーション ベース パス &ndash; アプリの実行可能ファイル ( *.exe*) が置かれている *bin* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="76b33-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="76b33-255">グローバル アセンブリ キャッシュ (GAC) &ndash; GAC には、複数の .NET Framework アプリで共有されるアセンブリが格納されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="76b33-256">詳細については、[ アセンブリをグローバル アセンブリ キャッシュにインストールする](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)」を参照してください。これは .NET Framework ドキュメントにあります。</span><span class="sxs-lookup"><span data-stu-id="76b33-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="76b33-257">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="76b33-257">Sample code</span></span>

<span data-ttu-id="76b33-258">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([ダウンロードする方法](xref:index#how-to-download-a-sample)) では、ホスティング スタートアップ実装のシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="76b33-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="76b33-259">2 つのホスティング スタートアップ アセンブリ (クラス ライブラリ) ではそれぞれ、メモリ内の構成のキーと値のペアの組が設定されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="76b33-260">NuGet パッケージ (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="76b33-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="76b33-261">クラス ライブラリ (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="76b33-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="76b33-262">ホスティング スタートアップは、ランタイム ストア配置アセンブリ (*StartupDiagnostics*) からアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="76b33-263">このアセンブリによってスタートアップ時に 2 つのミドルウェアがアプリに追加され、これにより診断情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="76b33-264">登録サービス</span><span class="sxs-lookup"><span data-stu-id="76b33-264">Registered services</span></span>
  * <span data-ttu-id="76b33-265">アドレス (スキーム、ホスト、パス ベース、パス、クエリ文字列)</span><span class="sxs-lookup"><span data-stu-id="76b33-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="76b33-266">接続 (リモート IP、リモート ポート、ローカル IP、ローカル ポート、クライアント証明書)</span><span class="sxs-lookup"><span data-stu-id="76b33-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="76b33-267">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="76b33-267">Request headers</span></span>
  * <span data-ttu-id="76b33-268">環境変数</span><span class="sxs-lookup"><span data-stu-id="76b33-268">Environment variables</span></span>

<span data-ttu-id="76b33-269">サンプルを実行するには</span><span class="sxs-lookup"><span data-stu-id="76b33-269">To run the sample:</span></span>

<span data-ttu-id="76b33-270">**NuGet パッケージからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="76b33-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="76b33-271">[dotnet pack](/dotnet/core/tools/dotnet-pack) コマンドを使用して *HostingStartupPackage* パッケージをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="76b33-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="76b33-272">*HostingStartupPackage* のパッケージのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="76b33-273">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-273">Compile and run the app.</span></span> <span data-ttu-id="76b33-274">機能拡張されたアプリにはパッケージ参照 (コンパイル時参照) が存在します。</span><span class="sxs-lookup"><span data-stu-id="76b33-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="76b33-275">アプリのプロジェクト ファイル内の `<PropertyGroup>` では、パッケージ プロジェクトの出力 ( *../HostingStartupPackage/Bin/debug*) がパッケージ ソースとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="76b33-276">これにより、[nuget.org](https://www.nuget.org/) にパッケージをアップロードすることなく、アプリによりパッケージが使用されるようになります。詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="76b33-277">インデックス ページで表示されるサービス構成キー値が、パッケージの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="76b33-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="76b33-278">*HostingStartupPackage* プロジェクトに変更を加えてそれをコンパイルする場合は、ローカル キャッシュから、古いパッケージではなく更新されたパッケージが、*HostingStartupApp* によって確実に受信されるように、ローカルの NuGet パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="76b33-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="76b33-279">ローカルの NuGet キャッシュをクリアするには、次の [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="76b33-280">**クラス ライブラリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="76b33-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="76b33-281">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドを使用して *HostingStartupLibrary* クラス ライブラリをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="76b33-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="76b33-282">*HostingStartupLibrary* のクラス ライブラリのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="76b33-283">クラス ライブラリのアセンブリをアプリに *bin* 配置するには、クラス ライブラリのコンパイルされた出力からアプリの *bin/Debug* フォルダーに *HostingStartupLibrary.dll* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="76b33-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="76b33-284">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-284">Compile and run the app.</span></span> <span data-ttu-id="76b33-285">アプリのプロジェクト ファイル内の `<ItemGroup>` により、クラス ライブラリのアセンブリ ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) が参照されます (コンパイル時参照)。</span><span class="sxs-lookup"><span data-stu-id="76b33-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="76b33-286">詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="76b33-287">インデックス ページで表示されるサービス構成キー値が、クラス ライブラリの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="76b33-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="76b33-288">**ランタイム ストア配置アセンブリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="76b33-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="76b33-289">*StartupDiagnostics* プロジェクトでは、[PowerShell](/powershell/scripting/powershell-scripting) を使用して、その *StartupDiagnostics.deps.json* ファイルを変更します。</span><span class="sxs-lookup"><span data-stu-id="76b33-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="76b33-290">既定では、PowerShell は Windows 7 SP1 と Windows Server 2008 R2 SP1 以降の Windows 上にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="76b33-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="76b33-291">他のプラットフォームで PowerShell を取得するには、「[Windows PowerShell のインストール](/powershell/scripting/setup/installing-powershell#powershell-core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-291">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="76b33-292">*RuntimeStore* フォルダーにある *build.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="76b33-293">スクリプトでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="76b33-293">The script:</span></span>
   * <span data-ttu-id="76b33-294">*obj\packages* フォルダーに `StartupDiagnostics` パッケージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="76b33-295">*store* フォルダー内に `StartupDiagnostics` 用のランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="76b33-296">スクリプト内の `dotnet store` コマンドでは、Windows に展開されているホスティング スタートアップの `win7-x64`[ランタイム識別子 (RID)](/dotnet/core/rid-catalog) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="76b33-297">別のランタイムに対してホスティング スタートアップを指定する場合は、スクリプトの 37 行目を適切な RID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="76b33-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="76b33-298">`StartupDiagnostics` のランタイム ストアは、後で、アセンブリが使用されるコンピューター上のユーザーまたはシステムのランタイム ストアに移動されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="76b33-299">`StartupDiagnostics` アセンブリのユーザー ランタイム ストアのインストール場所は、 *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll* です。</span><span class="sxs-lookup"><span data-stu-id="76b33-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="76b33-300">*additionalDeps* フォルダーに `StartupDiagnostics` の `additionalDeps` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="76b33-301">追加の依存関係は、後でユーザーまたはシステムの追加の依存関係に移動されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="76b33-302">ユーザーの `StartupDiagnostics` に関する追加の依存関係のインストール場所は、 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json* です。</span><span class="sxs-lookup"><span data-stu-id="76b33-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="76b33-303">*deploy.ps1* ファイルが *deployment* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="76b33-304">*deployment* フォルダーにある *deploy.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="76b33-305">スクリプトでは以下のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-305">The script appends:</span></span>
   * <span data-ttu-id="76b33-306">`StartupDiagnostics` が `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="76b33-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="76b33-307">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ホスティング スタートアップの依存関係のパスが、`DOTNET_ADDITIONAL_DEPS` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="76b33-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="76b33-308">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ランタイム ストアのパスが、`DOTNET_SHARED_STORE` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="76b33-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="76b33-309">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-309">Run the sample app.</span></span>
1. <span data-ttu-id="76b33-310">`/services` エンドポイントを要求して、アプリの登録サービスを参照します。</span><span class="sxs-lookup"><span data-stu-id="76b33-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="76b33-311">`/diag` エンドポイントを要求して、診断情報を参照します。</span><span class="sxs-lookup"><span data-stu-id="76b33-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="76b33-312"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (ホスティング スタートアップ) の実装を使うと、外部アセンブリからの起動時にアプリに拡張機能を追加できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="76b33-313">たとえば、外部ライブラリではホスティング スタートアップ実装を使用して、追加の構成プロバイダーまたはサービスをアプリに提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="76b33-314">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="76b33-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="76b33-315">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="76b33-315">HostingStartup attribute</span></span>

<span data-ttu-id="76b33-316">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性は、実行時にアクティブ化されるホスティング スタートアップ アセンブリが存在することを示しています。</span><span class="sxs-lookup"><span data-stu-id="76b33-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="76b33-317">入力アセンブリまたは `Startup` クラスを含むアセンブリは、`HostingStartup` 属性について自動的にスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="76b33-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-318">`HostingStartup` 属性を検索するアセンブリの一覧は、[WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 内の構成からランタイム時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="76b33-319">検出から除外されるアセンブリの一覧は、[WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="76b33-320">詳細については、「[Web ホスト: ホスティング スタートアップ アセンブリ](xref:fundamentals/host/web-host#hosting-startup-assemblies)と[Web ホスト: 除外するホスティング スタートアップ アセンブリ](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="76b33-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="76b33-321">次に例では、ホスティング スタートアップ アセンブリの名前空間は `StartupEnhancement` となります。</span><span class="sxs-lookup"><span data-stu-id="76b33-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="76b33-322">ホスティング スタートアップ コードを含むクラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="76b33-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="76b33-323">`HostingStartup` 属性は、通常、ホスティング スタートアップ アセンブリの `IHostingStartup` 実装クラス ファイル内に置かれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="76b33-324">読み込まれたホスティング スタートアップ アセンブリを見つける</span><span class="sxs-lookup"><span data-stu-id="76b33-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="76b33-325">読み込まれたホスティング スタートアップ アセンブリを検出するには、ログ記録を有効にしてアプリのログを確認します。</span><span class="sxs-lookup"><span data-stu-id="76b33-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="76b33-326">アセンブリの読み込み時に発生したエラーがログ記録されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="76b33-327">読み込まれたホスティング スタートアップ アセンブリは、デバッグ レベルでログ記録され、すべてのエラーが記録されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="76b33-328">ホスティング スタートアップ アセンブリの自動読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="76b33-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="76b33-329">ホスティング スタートアップ アセンブリの自動読み込みを無効にするには、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="76b33-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="76b33-330">すべてのホスティング スタートアップ アセンブリの読み込みを回避するには、次のいずれかを `true` または `1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="76b33-331">[[ホスティング スタートアップを回避する]](xref:fundamentals/host/web-host#prevent-hosting-startup) ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="76b33-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="76b33-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="76b33-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="76b33-333">特定のホスティング スタートアップ アセンブリの読み込みを回避するには、次に示すいずれかを、起動時に除外するホスティング スタートアップ アセンブリを示すセミコロンで区切られた文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="76b33-334">[[Hosting Startup Exclude Assemblies]\(除外するホスティング スタートアップ アセンブリ\)](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ホスト構成設定。</span><span class="sxs-lookup"><span data-stu-id="76b33-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="76b33-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="76b33-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="76b33-336">ホスト構成設定と環境変数が両方とも設定されている場合は、ホスト設定によって動作が制御されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="76b33-337">ホスト設定または環境変数を使用してホスティング スタートアップ アセンブリを無効にすると、アセンブリがグローバルに無効になり、場合によってはアプリのいくつかの属性も無効になります。</span><span class="sxs-lookup"><span data-stu-id="76b33-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="76b33-338">Project</span><span class="sxs-lookup"><span data-stu-id="76b33-338">Project</span></span>

<span data-ttu-id="76b33-339">次のいずれかの種類のプロジェクトを使用して、ホスティング スタートアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="76b33-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="76b33-340">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="76b33-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="76b33-341">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="76b33-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="76b33-342">クラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="76b33-342">Class library</span></span>

<span data-ttu-id="76b33-343">クラス ライブラリでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="76b33-344">このライブラリには `HostingStartup` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="76b33-345">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)には、Razor Pages アプリ、*HostingStartupApp*、クラス ライブラリ、および *HostingStartupLibrary* が含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="76b33-346">クラス ライブラリには次のものが含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-346">The class library:</span></span>

* <span data-ttu-id="76b33-347">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="76b33-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="76b33-348">`ServiceKeyInjection` では、メモリ内の構成プロバイダー ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) を使用して、サービスの文字列のペアがアプリの構成に追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="76b33-349">ホスティング スタートアップの名前空間とクラスを識別する `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="76b33-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="76b33-350">`ServiceKeyInjection` クラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="76b33-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="76b33-352">アプリのインデックス ページでは、クラス ライブラリのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="76b33-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="76b33-354">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) には、別のホスティング スタートアップ *HostingStartupPackage* を提供する NuGet パッケージ プロジェクトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="76b33-355">パッケージには、前に説明したクラス ライブラリと同じ特性があります。</span><span class="sxs-lookup"><span data-stu-id="76b33-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="76b33-356">パッケージには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-356">The package:</span></span>

* <span data-ttu-id="76b33-357">`IHostingStartup` を実装するホスティング スタートアップ クラス `ServiceKeyInjection`。</span><span class="sxs-lookup"><span data-stu-id="76b33-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="76b33-358">`ServiceKeyInjection` では、アプリの構成にサービス文字列のペアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="76b33-359">`HostingStartup`属性。</span><span class="sxs-lookup"><span data-stu-id="76b33-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="76b33-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="76b33-361">アプリのインデックス ページでは、パッケージのホスティング スタートアップ アセンブリによって設定された 2 つのキーの構成値が読み取られて表示されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="76b33-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="76b33-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="76b33-363">エントリ ポイントのないコンソール アプリ</span><span class="sxs-lookup"><span data-stu-id="76b33-363">Console app without an entry point</span></span>

<span data-ttu-id="76b33-364">*このアプローチは、.NET Core アプリでのみ利用でき、.NET Framework では利用できません。*</span><span class="sxs-lookup"><span data-stu-id="76b33-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="76b33-365">`HostingStartup` 属性を含むエントリ ポイントがないコンソール アプリ内では、アクティブ化に関するコンパイル時参照を必要としない動的ホスティング スタートアップ拡張機能を指定できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-366">コンソール アプリを発行すると、ランタイム ストアから使用できるホスティング スタートアップ アセンブリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="76b33-367">このプロセスにおいてエントリ ポイントのないコンソール アプリが使用される理由は、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76b33-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="76b33-368">ホスティング スタートアップ アセンブリ内のホスティング スタートアップを使用するには、依存関係ファイルが必要です。</span><span class="sxs-lookup"><span data-stu-id="76b33-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="76b33-369">依存関係ファイルは、ライブラリではなくアプリを発行することによって生成される、実行可能なアプリ資産です。</span><span class="sxs-lookup"><span data-stu-id="76b33-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="76b33-370">[ランタイム パッケージ ストア](/dotnet/core/deploying/runtime-store) にライブラリを直接追加することはできません。それには、共有ランタイムを対象とする実行可能なプロジェクトが必要です。</span><span class="sxs-lookup"><span data-stu-id="76b33-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="76b33-371">動的ホスティング スタートアップを作成する場合:</span><span class="sxs-lookup"><span data-stu-id="76b33-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="76b33-372">次のようなエントリ ポイントがないコンソール アプリからホスティング スタートアップ アセンブリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="76b33-373">`IHostingStartup` の実装を含むクラスを含んでいる。</span><span class="sxs-lookup"><span data-stu-id="76b33-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="76b33-374">`IHostingStartup` 実装クラスを識別するための [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を含んでいる。</span><span class="sxs-lookup"><span data-stu-id="76b33-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="76b33-375">ホスティング スタートアップの依存関係を取得するには、コンソール アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="76b33-376">コンソール アプリを発行すると、その結果として、依存関係ファイルから未使用の依存関係が切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="76b33-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="76b33-377">ホスティング スタートアップ アセンブリのランタイムの場所を設定するために、依存関係ファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="76b33-378">ホスティング スタートアップ アセンブリとその依存関係ファイルは、ランタイム パッケージ ストアに配置されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="76b33-379">ホスティング スタートアップ アセンブリとその依存関係ファイルを検出する場合、それらは環境変数のペアに記載されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="76b33-380">コンソール アセンブリでは、[Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) パッケージが参照されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="76b33-381">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性を使うと、<xref:Microsoft.AspNetCore.Hosting.IWebHost> のビルド時に、読み込みと実行のための `IHostingStartup` の実装としてクラスが識別されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="76b33-382">次の例では、名前空間は `StartupEnhancement`、クラスは `StartupEnhancementHostingStartup` です。</span><span class="sxs-lookup"><span data-stu-id="76b33-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="76b33-383">クラスは `IHostingStartup` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="76b33-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="76b33-384">このクラスの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドでは、<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> を使ってアプリに拡張機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="76b33-385">ホスティング スタートアップ アセンブリ内の `IHostingStartup.Configure` は、ユーザー コード内の `Startup.Configure` よりも前にランタイムによって呼び出されます。このため、ホスティング スタートアップ アセンブリによって提供されるすべての構成をユーザー コードで上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="76b33-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="76b33-386">`IHostingStartup` プロジェクトのビルド時に、依存関係ファイル ( *.deps.json*) によってアセンブリの `runtime` の場所が *bin* フォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="76b33-387">ファイルの一部のみが示されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-387">Only part of the file is shown.</span></span> <span data-ttu-id="76b33-388">例のアセンブリ名は `StartupEnhancement` です。</span><span class="sxs-lookup"><span data-stu-id="76b33-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="76b33-389">ホスティング スタートアップによって指定される構成</span><span class="sxs-lookup"><span data-stu-id="76b33-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="76b33-390">ホスティング スタートアップの構成、アプリの構成のどちらを優先させるかによって、2 つの異なる方法で構成を処理できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="76b33-391">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行した後で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="76b33-392">この方法を使うと、ホスティング スタートアップの構成の方がアプリの構成よりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="76b33-393">アプリの <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> デリゲートを実行する前に <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を使って構成を読み込み、アプリに構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="76b33-394">この方法を使うと、アプリの構成値の方がホスティング スタートアップにより指定されるものよりも優先されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="76b33-395">ホスティング スタートアップ アセンブリを指定する</span><span class="sxs-lookup"><span data-stu-id="76b33-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="76b33-396">クラス ライブラリまたはコンソール アプリのいずれかで提供されるホスティング スタートアップの場合は、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数内にホスティング スタートアップ アセンブリの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="76b33-397">環境変数はアセンブリのセミコロン区切りのリストです。</span><span class="sxs-lookup"><span data-stu-id="76b33-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="76b33-398">ホスティング スタートアップ アセンブリのみが、`HostingStartup` 属性に対してスキャンされます。</span><span class="sxs-lookup"><span data-stu-id="76b33-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-399">サンプル アプリ *HostingStartupApp* では、前述したホスティング スタートアップを検出するために、環境変数が次の値に設定されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="76b33-400">ホスティング スタートアップ アセンブリはまた、[[ホスティング スタートアップ アセンブリ]](xref:fundamentals/host/web-host#hosting-startup-assemblies) ホスト構成設定を使用して設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="76b33-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="76b33-401">複数のホスティング スタートアップ アセンブリが存在する場合、それらの <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> メソッドは、アセンブリが一覧表示されている順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="76b33-402">アクティベーション</span><span class="sxs-lookup"><span data-stu-id="76b33-402">Activation</span></span>

<span data-ttu-id="76b33-403">ホスティング スタートアップのアクティブ化のオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76b33-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="76b33-404">[ランタイム ストア](#runtime-store) &ndash; アクティブ化では、アクティブ化に関するコンパイル時参照を必要としません。</span><span class="sxs-lookup"><span data-stu-id="76b33-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="76b33-405">サンプル アプリでは、ホスティング スタートアップ アセンブリおよび依存関係のファイルが *deployment* フォルダーに配置されています。これにより、複数のコンピューターから成る環境でのホスティング スタートアップの配置が容易になります。</span><span class="sxs-lookup"><span data-stu-id="76b33-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="76b33-406">*deployment* フォルダーには、ホスティングスタートアップが有効になるように配置システム上で環境変数を作成および変更する PowerShell スクリプトも含まれています。</span><span class="sxs-lookup"><span data-stu-id="76b33-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="76b33-407">アクティブ化で必須のコンパイル時参照</span><span class="sxs-lookup"><span data-stu-id="76b33-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="76b33-408">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="76b33-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="76b33-409">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="76b33-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="76b33-410">ランタイム ストア</span><span class="sxs-lookup"><span data-stu-id="76b33-410">Runtime store</span></span>

<span data-ttu-id="76b33-411">ホスティング スタートアップ実装は、[ランタイム ストア](/dotnet/core/deploying/runtime-store)に置かれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="76b33-412">アセンブリへのコンパイル時参照は、機能強化されたアプリで必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="76b33-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="76b33-413">ホスティング スタートアップをビルドした後、プロジェクトのマニフェスト ファイルと [dotnet store](/dotnet/core/tools/dotnet-store) コマンドの使用によってランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="76b33-414">サンプル アプリ (*RuntimeStore* プロジェクト) では、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="76b33-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="76b33-415">ランタイムでランタイム ストアを検出できるように、ランタイム ストアの場所を環境変数 `DOTNET_SHARED_STORE` に追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="76b33-416">**ホスティング スタートアップの依存関係ファイルを変更して配置する**</span><span class="sxs-lookup"><span data-stu-id="76b33-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="76b33-417">拡張機能へのパッケージ参照なしで拡張機能をアクティブ化するには、`additionalDeps` を使用してランタイムに追加の依存関係を指定します。</span><span class="sxs-lookup"><span data-stu-id="76b33-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="76b33-418">`additionalDeps` を使用すると、次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="76b33-419">スタートアップ時にアプリの独自の *.deps.json* ファイルとマージさせる追加の *.deps.json* ファイルのセットを指定することで、アプリのライブラリのグラフを拡張します。</span><span class="sxs-lookup"><span data-stu-id="76b33-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="76b33-420">ホスティング スタートアップ アセンブリを検出可能および読み込み可能にします。</span><span class="sxs-lookup"><span data-stu-id="76b33-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="76b33-421">追加の依存関係ファイルを生成するために推奨される方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="76b33-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="76b33-422">前のセクションで参照したランタイム ストアのマニフェスト ファイルに対して `dotnet publish` を実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="76b33-423">ライブラリと、結果として得られる *deps.json* ファイルの `runtime` セクションから、マニフェスト参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="76b33-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="76b33-424">プロジェクトの例では、`targets` と `libraries` セクションから `store.manifest/1.0.0` プロパティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="76b33-425">次の場所に *.deps.json* ファイルを配置します。</span><span class="sxs-lookup"><span data-stu-id="76b33-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="76b33-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; `DOTNET_ADDITIONAL_DEPS` 環境変数に追加される場所。</span><span class="sxs-lookup"><span data-stu-id="76b33-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="76b33-427">`{SHARED FRAMEWORK NAME}` &ndash; この追加の依存関係ファイルに必要な共有フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="76b33-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="76b33-428">`{SHARED FRAMEWORK VERSION}` &ndash; 共有フレームワークの最小バージョン。</span><span class="sxs-lookup"><span data-stu-id="76b33-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="76b33-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; 拡張機能のアセンブリ名。</span><span class="sxs-lookup"><span data-stu-id="76b33-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="76b33-430">サンプル アプリ (*RuntimeStore* プロジェクト) では、追加の依存関係ファイルは以下の場所に配置されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="76b33-431">ランタイムでランタイム ストアの場所を検出できるように、追加の依存関係ファイルの場所が環境変数 `DOTNET_ADDITIONAL_DEPS` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="76b33-432">サンプル アプリ (*RuntimeStore* プロジェクト) では、[PowerShell](/powershell/scripting/powershell-scripting) スクリプトを使用してランタイム ストアのビルドと追加の依存関係ファイルの生成を行います。</span><span class="sxs-lookup"><span data-stu-id="76b33-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="76b33-433">さまざまなオペレーティング システムの環境変数を設定する方法の例については、[複数の環境の使用](xref:fundamentals/environments)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="76b33-434">**配置**</span><span class="sxs-lookup"><span data-stu-id="76b33-434">**Deployment**</span></span>

<span data-ttu-id="76b33-435">サンプル アプリでは、複数のコンピューターから成る環境へのホスティング スタートアップの配置を容易にするために、発行された出力内に、次を含む *deployment* フォルダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="76b33-436">ホスティング スタートアップのランタイム ストア。</span><span class="sxs-lookup"><span data-stu-id="76b33-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="76b33-437">ホスティング スタートアップの依存関係ファイル。</span><span class="sxs-lookup"><span data-stu-id="76b33-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="76b33-438">ホスティング スタートアップのアクティブ化をサポートできるように、`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE`、`DOTNET_ADDITIONAL_DEPS` を作成および変更する PowerShell スクリプト。</span><span class="sxs-lookup"><span data-stu-id="76b33-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="76b33-439">このスクリプトは、配置システム上の PowerShell 管理用コマンド プロンプトから実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="76b33-440">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="76b33-440">NuGet package</span></span>

<span data-ttu-id="76b33-441">NuGet パッケージでは、ホスティング スタートアップの拡張機能を提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="76b33-442">このパッケージには `HostingStartup` 属性があります。</span><span class="sxs-lookup"><span data-stu-id="76b33-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="76b33-443">このパッケージで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="76b33-444">機能強化されたアプリのプロジェクト ファイルでは、アプリのプロジェクト ファイル内のホスティング スタートアップに対するパッケージ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="76b33-445">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="76b33-446">このアプローチは、[nuget.org](https://www.nuget.org/) に発行されるホスティング スタートアップ アセンブリ パッケージに適用されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="76b33-447">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="76b33-448">NuGet パッケージとランタイム ストアの詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="76b33-449">クロスプラットフォーム ツールを使用して NuGet パッケージを作成する方法</span><span class="sxs-lookup"><span data-stu-id="76b33-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="76b33-450">パッケージを公開する</span><span class="sxs-lookup"><span data-stu-id="76b33-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="76b33-451">ランタイム パッケージ ストア</span><span class="sxs-lookup"><span data-stu-id="76b33-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="76b33-452">プロジェクトの bin フォルダー</span><span class="sxs-lookup"><span data-stu-id="76b33-452">Project bin folder</span></span>

<span data-ttu-id="76b33-453">ホスティング スタートアップの拡張機能は、機能強化されたアプリ内の *bin* 配置アセンブリによって提供できます。</span><span class="sxs-lookup"><span data-stu-id="76b33-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="76b33-454">このアセンブリで提供される種類のホスティング スタートアップは、次のアプローチのいずれかを使用してアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="76b33-455">機能強化されたアプリのプロジェクト ファイルでは、ホスティング スタートアップへのアセンブリ参照 (コンパイル時参照) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="76b33-456">コンパイル時参照を適用すると、ホスティング スタートアップ アセンブリとその依存関係のすべてが、アプリの依存関係ファイル ( *.deps.json*) に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="76b33-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="76b33-457">この手法は、展開シナリオによって、ホスティング スタートアップのアセンブリ ( *.dll* ファイル) に対してコンパイル時参照を作成し、次のいずれかにそのアセンブリを移動させることが要請される場合に適用されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="76b33-458">使用するプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="76b33-458">The consuming project.</span></span>
  * <span data-ttu-id="76b33-459">使用するプロジェクトでアクセスできる場所。</span><span class="sxs-lookup"><span data-stu-id="76b33-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="76b33-460">ホスティング スタートアップの依存関係ファイルは、「[ランタイム ストア](#runtime-store)」のセクションで説明にしたように (コンパイル参照がない場合)、機能強化されたアプリで利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="76b33-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="76b33-461">.NET Framework を対象にするとき、アセンブリは既定の読み込みコンテキストで読み込み可能です。これは、.NET Framework の場合、アセンブリが次のいずれかの場所にあることを意味します。</span><span class="sxs-lookup"><span data-stu-id="76b33-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="76b33-462">アプリケーション ベース パス &ndash; アプリの実行可能ファイル ( *.exe*) が置かれている *bin* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="76b33-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="76b33-463">グローバル アセンブリ キャッシュ (GAC) &ndash; GAC には、複数の .NET Framework アプリで共有されるアセンブリが格納されています。</span><span class="sxs-lookup"><span data-stu-id="76b33-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="76b33-464">詳細については、[ アセンブリをグローバル アセンブリ キャッシュにインストールする](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)」を参照してください。これは .NET Framework ドキュメントにあります。</span><span class="sxs-lookup"><span data-stu-id="76b33-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="76b33-465">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="76b33-465">Sample code</span></span>

<span data-ttu-id="76b33-466">[サンプル コード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([ダウンロードする方法](xref:index#how-to-download-a-sample)) では、ホスティング スタートアップ実装のシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="76b33-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="76b33-467">2 つのホスティング スタートアップ アセンブリ (クラス ライブラリ) ではそれぞれ、メモリ内の構成のキーと値のペアの組が設定されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="76b33-468">NuGet パッケージ (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="76b33-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="76b33-469">クラス ライブラリ (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="76b33-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="76b33-470">ホスティング スタートアップは、ランタイム ストア配置アセンブリ (*StartupDiagnostics*) からアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="76b33-471">このアセンブリによってスタートアップ時に 2 つのミドルウェアがアプリに追加され、これにより診断情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="76b33-472">登録サービス</span><span class="sxs-lookup"><span data-stu-id="76b33-472">Registered services</span></span>
  * <span data-ttu-id="76b33-473">アドレス (スキーム、ホスト、パス ベース、パス、クエリ文字列)</span><span class="sxs-lookup"><span data-stu-id="76b33-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="76b33-474">接続 (リモート IP、リモート ポート、ローカル IP、ローカル ポート、クライアント証明書)</span><span class="sxs-lookup"><span data-stu-id="76b33-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="76b33-475">要求ヘッダー</span><span class="sxs-lookup"><span data-stu-id="76b33-475">Request headers</span></span>
  * <span data-ttu-id="76b33-476">環境変数</span><span class="sxs-lookup"><span data-stu-id="76b33-476">Environment variables</span></span>

<span data-ttu-id="76b33-477">サンプルを実行するには</span><span class="sxs-lookup"><span data-stu-id="76b33-477">To run the sample:</span></span>

<span data-ttu-id="76b33-478">**NuGet パッケージからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="76b33-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="76b33-479">[dotnet pack](/dotnet/core/tools/dotnet-pack) コマンドを使用して *HostingStartupPackage* パッケージをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="76b33-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="76b33-480">*HostingStartupPackage* のパッケージのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="76b33-481">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-481">Compile and run the app.</span></span> <span data-ttu-id="76b33-482">機能拡張されたアプリにはパッケージ参照 (コンパイル時参照) が存在します。</span><span class="sxs-lookup"><span data-stu-id="76b33-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="76b33-483">アプリのプロジェクト ファイル内の `<PropertyGroup>` では、パッケージ プロジェクトの出力 ( *../HostingStartupPackage/Bin/debug*) がパッケージ ソースとして指定されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="76b33-484">これにより、[nuget.org](https://www.nuget.org/) にパッケージをアップロードすることなく、アプリによりパッケージが使用されるようになります。詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="76b33-485">インデックス ページで表示されるサービス構成キー値が、パッケージの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="76b33-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="76b33-486">*HostingStartupPackage* プロジェクトに変更を加えてそれをコンパイルする場合は、ローカル キャッシュから、古いパッケージではなく更新されたパッケージが、*HostingStartupApp* によって確実に受信されるように、ローカルの NuGet パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="76b33-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="76b33-487">ローカルの NuGet キャッシュをクリアするには、次の [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="76b33-488">**クラス ライブラリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="76b33-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="76b33-489">[dotnet build](/dotnet/core/tools/dotnet-build) コマンドを使用して *HostingStartupLibrary* クラス ライブラリをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="76b33-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="76b33-490">*HostingStartupLibrary* のクラス ライブラリのアセンブリ名を `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="76b33-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="76b33-491">クラス ライブラリのアセンブリをアプリに *bin* 配置するには、クラス ライブラリのコンパイルされた出力からアプリの *bin/Debug* フォルダーに *HostingStartupLibrary.dll* ファイルをコピーします。</span><span class="sxs-lookup"><span data-stu-id="76b33-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="76b33-492">アプリをコンパイルして実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-492">Compile and run the app.</span></span> <span data-ttu-id="76b33-493">アプリのプロジェクト ファイル内の `<ItemGroup>` は、クラス ライブラリのアセンブリ ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) を参照します (コンパイル時参照)。</span><span class="sxs-lookup"><span data-stu-id="76b33-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="76b33-494">詳細については、HostingStartupApp のプロジェクト ファイル内の注を参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="76b33-495">インデックス ページで表示されるサービス構成キー値が、クラス ライブラリの `ServiceKeyInjection.Configure` メソッドによって設定された値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="76b33-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="76b33-496">**ランタイム ストア配置アセンブリからのアクティブ化**</span><span class="sxs-lookup"><span data-stu-id="76b33-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="76b33-497">*StartupDiagnostics* プロジェクトでは、[PowerShell](/powershell/scripting/powershell-scripting) を使用して、その *StartupDiagnostics.deps.json* ファイルを変更します。</span><span class="sxs-lookup"><span data-stu-id="76b33-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="76b33-498">既定では、PowerShell は Windows 7 SP1 と Windows Server 2008 R2 SP1 以降の Windows 上にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="76b33-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="76b33-499">他のプラットフォームで PowerShell を取得するには、「[Windows PowerShell のインストール](/powershell/scripting/setup/installing-powershell#powershell-core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="76b33-499">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="76b33-500">*RuntimeStore* フォルダーにある *build.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="76b33-501">スクリプトでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="76b33-501">The script:</span></span>
   * <span data-ttu-id="76b33-502">*obj\packages* フォルダーに `StartupDiagnostics` パッケージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="76b33-503">*store* フォルダー内に `StartupDiagnostics` 用のランタイム ストアが生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="76b33-504">スクリプト内の `dotnet store` コマンドでは、Windows に展開されているホスティング スタートアップの `win7-x64`[ランタイム識別子 (RID)](/dotnet/core/rid-catalog) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="76b33-505">別のランタイムに対してホスティング スタートアップを指定する場合は、スクリプトの 37 行目を適切な RID に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="76b33-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="76b33-506">`StartupDiagnostics` のランタイム ストアは、後で、アセンブリが使用されるコンピューター上のユーザーまたはシステムのランタイム ストアに移動されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="76b33-507">`StartupDiagnostics` アセンブリのユーザー ランタイム ストアのインストール場所は、 *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll* です。</span><span class="sxs-lookup"><span data-stu-id="76b33-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="76b33-508">*additionalDeps* フォルダーに `StartupDiagnostics` の `additionalDeps` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="76b33-509">追加の依存関係は、後でユーザーまたはシステムの追加の依存関係に移動されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="76b33-510">ユーザーの `StartupDiagnostics` に関する追加の依存関係のインストール場所は、 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json* です。</span><span class="sxs-lookup"><span data-stu-id="76b33-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="76b33-511">*deploy.ps1* ファイルが *deployment* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="76b33-512">*deployment* フォルダーにある *deploy.ps1* スクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="76b33-513">スクリプトでは以下のものが追加されます。</span><span class="sxs-lookup"><span data-stu-id="76b33-513">The script appends:</span></span>
   * <span data-ttu-id="76b33-514">`StartupDiagnostics` が `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="76b33-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="76b33-515">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ホスティング スタートアップの依存関係のパスが、`DOTNET_ADDITIONAL_DEPS` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="76b33-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="76b33-516">(RuntimeStore プロジェクトの *deployment*  フォルダーにある) ランタイム ストアのパスが、`DOTNET_SHARED_STORE` 環境変数に。</span><span class="sxs-lookup"><span data-stu-id="76b33-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="76b33-517">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="76b33-517">Run the sample app.</span></span>
1. <span data-ttu-id="76b33-518">`/services` エンドポイントを要求して、アプリの登録サービスを参照します。</span><span class="sxs-lookup"><span data-stu-id="76b33-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="76b33-519">`/diag` エンドポイントを要求して、診断情報を参照します。</span><span class="sxs-lookup"><span data-stu-id="76b33-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
