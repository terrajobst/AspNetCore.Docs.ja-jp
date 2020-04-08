---
title: ASP.NET Core でのアプリケーションのスタートアップ
author: rick-anderson
description: ASP.NET Core の Startup クラスがサービスとアプリケーションの要求パイプラインをどのように構成しているかを説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/startup
ms.openlocfilehash: e3249df4b7388beeff13fe4b4e0ff481c35725c5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78651014"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="36985-103">ASP.NET Core でのアプリケーションのスタートアップ</span><span class="sxs-lookup"><span data-stu-id="36985-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="36985-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="36985-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="36985-105">`Startup` クラスはサービスとアプリケーションの要求パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="36985-105">The `Startup` class configures services and the app's request pipeline.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="36985-106">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="36985-106">The Startup class</span></span>

<span data-ttu-id="36985-107">ASP.NET Core アプリケーションでは `Startup` クラスが使用されています。このクラスは規約に従って `Startup` と名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="36985-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="36985-108">`Startup` クラスには次の特徴があります。</span><span class="sxs-lookup"><span data-stu-id="36985-108">The `Startup` class:</span></span>

* <span data-ttu-id="36985-109">必要に応じて <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッドを含め、アプリの*サービス*を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="36985-110">サービスとは、アプリ機能を提供する再利用可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="36985-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="36985-111">サービスは  *に*登録され`ConfigureServices`、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> を介してアプリ全体で利用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="36985-112">アプリの要求処理パイプラインを作成するために <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドを含めます。</span><span class="sxs-lookup"><span data-stu-id="36985-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="36985-113">`ConfigureServices` と `Configure` はアプリの起動時に ASP.NET Core ランタイムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36985-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="36985-114">上記のサンプルは [Razor Pages](xref:razor-pages/index) 用です。MVC バージョンは似ています。</span><span class="sxs-lookup"><span data-stu-id="36985-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>


<span data-ttu-id="36985-115">アプリの`Startup`ホスト[がビルドされるときに、](xref:fundamentals/index#host) クラスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="36985-116">`Startup` クラスは通常、ホスト ビルダーで [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) メソッドを呼び出すことで指定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="36985-117">ホストには、`Startup` クラス コンストラクターで使用できるサービスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="36985-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="36985-118">アプリケーションから `ConfigureServices` 経由でサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="36985-119">ホスト サービスとアプリ サービスの両方が `Configure` 内とアプリ全体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="36985-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="36985-120">`Startup`汎用ホスト[ (](xref:fundamentals/host/generic-host)) を使用すると、次のサービスの種類のみを <xref:Microsoft.Extensions.Hosting.IHostBuilder> コンストラクターに挿入できます。</span><span class="sxs-lookup"><span data-stu-id="36985-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="36985-121">ほとんどのサービスは、`Configure` メソッドが呼び出されるまで使用できません。</span><span class="sxs-lookup"><span data-stu-id="36985-121">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="36985-122">マルチ スタートアップ</span><span class="sxs-lookup"><span data-stu-id="36985-122">Multiple Startup</span></span>

<span data-ttu-id="36985-123">アプリケーションの環境 (たとえば `Startup`) ごとに個別の `StartupDevelopment` クラスが定義されると、実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="36985-123">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="36985-124">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="36985-124">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="36985-125">アプリケーションが Development 環境で実行され、`Startup` クラスと `StartupDevelopment` クラスの両方が含まれている場合は、`StartupDevelopment` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-125">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="36985-126">詳細については、「[Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods)」(複数の環境の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-126">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="36985-127">ホストの詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-127">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="36985-128">スタートアップ時のエラー処理については、「[Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling)」(スタートアップ例外処理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-128">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="36985-129">ConfigureServices メソッド</span><span class="sxs-lookup"><span data-stu-id="36985-129">The ConfigureServices method</span></span>

<span data-ttu-id="36985-130"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="36985-130">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="36985-131">省略可能。</span><span class="sxs-lookup"><span data-stu-id="36985-131">Optional.</span></span>
* <span data-ttu-id="36985-132">アプリケーションのサービスを構成する `Configure` メソッドの前にホストによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36985-132">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="36985-133">規約によって[構成オプション](xref:fundamentals/configuration/index)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-133">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="36985-134">ホストでは、`Startup` メソッドが呼び出される前にいくつかのサービスを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-134">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="36985-135">詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-135">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="36985-136">多くの設定が必要な機能には、`Add{Service}` 上の <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 拡張メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="36985-136">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="36985-137">たとえば、**Add**DbContext、**Add**DefaultIdentity、**Add**EntityFrameworkStores、**Add**RazorPages です。</span><span class="sxs-lookup"><span data-stu-id="36985-137">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

<span data-ttu-id="36985-138">サービス コンテナーにサービスを追加すると、アプリケーション内と `Configure` メソッド内でサービスを利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="36985-138">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="36985-139">サービスは[依存関係の挿入](xref:fundamentals/dependency-injection)または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="36985-139">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="36985-140">Configure メソッド</span><span class="sxs-lookup"><span data-stu-id="36985-140">The Configure method</span></span>

<span data-ttu-id="36985-141"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドは、アプリケーションが HTTP 要求にどのように応答するかを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-141">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="36985-142">要求パイプラインは、[ミドルウェア](xref:fundamentals/middleware/index) コンポーネントを <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> インスタンスに追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="36985-142">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="36985-143">`IApplicationBuilder` は `Configure` メソッドから使用できますが、サービス コンテナーに登録されていません。</span><span class="sxs-lookup"><span data-stu-id="36985-143">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="36985-144">ホスティングによって `IApplicationBuilder` が作成され、`Configure` に直接渡されます。</span><span class="sxs-lookup"><span data-stu-id="36985-144">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="36985-145">[ASP.NET Core テンプレート](/dotnet/core/tools/dotnet-new)では、次をサポートするパイプラインが構成されます。</span><span class="sxs-lookup"><span data-stu-id="36985-145">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="36985-146">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="36985-146">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="36985-147">例外ハンドラー</span><span class="sxs-lookup"><span data-stu-id="36985-147">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="36985-148">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="36985-148">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="36985-149">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="36985-149">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="36985-150">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="36985-150">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="36985-151">ASP.NET Core [MVC](xref:mvc/overview) と [Razor ページ](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="36985-151">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="36985-152">上記のサンプルは [Razor Pages](xref:razor-pages/index) 用です。MVC バージョンは似ています。</span><span class="sxs-lookup"><span data-stu-id="36985-152">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

<span data-ttu-id="36985-153">各 `Use` 拡張メソッドによって、要求パイプラインに 1 つまたは複数のミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-153">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="36985-154">たとえば、<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> では、[静的ファイル](xref:fundamentals/middleware/index)を提供するように、[ミドルウェア](xref:fundamentals/static-files)を構成します。</span><span class="sxs-lookup"><span data-stu-id="36985-154">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="36985-155">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、妥当な場合はチェーンをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="36985-155">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="36985-156">`IWebHostEnvironment`、`ILoggerFactory`、または `ConfigureServices` で定義された任意のものなどの追加サービスを `Configure` メソッド シグネチャで指定できます。</span><span class="sxs-lookup"><span data-stu-id="36985-156">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="36985-157">使用可能な場合、これらのサービスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="36985-157">These services are injected if they're available.</span></span>

<span data-ttu-id="36985-158">`IApplicationBuilder` の使用方法およびミドルウェアの処理の順序については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-158">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="36985-159">スタートアップを使用せずにサービスを構成する</span><span class="sxs-lookup"><span data-stu-id="36985-159">Configure services without Startup</span></span>

<span data-ttu-id="36985-160">`Startup` クラスを使用せず、サービスと要求処理パイプラインを構成するには、ホスト ビルダーで便利なメソッド、`ConfigureServices` と `Configure` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="36985-160">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="36985-161">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="36985-162">`Configure` メソッドが複数回呼び出された場合、最後の `Configure` 呼び出しが使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-162">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="36985-163">スタートアップ フィルターを使用した Startup の拡張</span><span class="sxs-lookup"><span data-stu-id="36985-163">Extend Startup with startup filters</span></span>

<span data-ttu-id="36985-164"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter> を使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="36985-164">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="36985-165">[ を明示的に呼び出さずに、アプリの ](#the-configure-method)Configure`Use{Middleware}` ミドルウェア パイプラインの先頭または末尾でミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="36985-165">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="36985-166">`IStartupFilter` は、アプリの作成者が既定のミドルウェアを明示的に登録する必要がないよう、パイプラインの先頭に既定値を追加するために、ASP.NET Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-166">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="36985-167">`IStartupFilter` は、アプリの作成者に代わって、別のコンポーネント呼び出し `Use{Middleware}` を許可します。</span><span class="sxs-lookup"><span data-stu-id="36985-167">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="36985-168">`Configure` メソッドのパイプラインを作成します。</span><span class="sxs-lookup"><span data-stu-id="36985-168">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="36985-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) では、ライブラリによって追加されたミドルウェアの前後に実行するように、ミドルウェアを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="36985-170">`IStartupFilter` には、<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> を受け取って返す `Action<IApplicationBuilder>` が実装されています。</span><span class="sxs-lookup"><span data-stu-id="36985-170">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="36985-171"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で、アプリケーションの要求パイプラインを構成するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="36985-171">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="36985-172">詳細については、「[Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)」(IApplicationBuilder を使用したミドルウェア パイプラインの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-172">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="36985-173">各 `IStartupFilter` によって、要求パイプラインで 1 つまたは複数のミドルウェアを追加できます。</span><span class="sxs-lookup"><span data-stu-id="36985-173">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="36985-174">フィルターは、サービス コンテナーに追加された順に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36985-174">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="36985-175">フィルターは、コントロールを次のフィルターに渡す前または後にミドルウェアを追加できるため、アプリケーション パイプラインの先頭または末尾に追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-175">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="36985-176">`IStartupFilter` でミドルウェアを登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="36985-176">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="36985-177">`RequestSetOptionsMiddleware` ミドルウェアによって、クエリ文字列パラメーターからオプション値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-177">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="36985-178">`RequestSetOptionsMiddleware` は `RequestSetOptionsStartupFilter` クラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="36985-178">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="36985-179">`IStartupFilter` は <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> のサービス コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="36985-179">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

<span data-ttu-id="36985-180">`option` のクエリ文字列パラメーターを指定すると、ミドルウェアでは ASP.NET Core ミドルウェアによって応答がレンダリングされる前に値の割り当てを処理します。</span><span class="sxs-lookup"><span data-stu-id="36985-180">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="36985-181">ミドルウェアの実行順序は、`IStartupFilter` の登録順に設定されています。</span><span class="sxs-lookup"><span data-stu-id="36985-181">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="36985-182">複数の `IStartupFilter` の実装が、同じオブジェクトとやり取りする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="36985-182">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="36985-183">順序が重要な場合は、ミドルウェアの実行順序に合わせて `IStartupFilter` サービスの登録順序を指定してください。</span><span class="sxs-lookup"><span data-stu-id="36985-183">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="36985-184">`IStartupFilter` に登録された他のアプリケーション ミドルウェアの前または後に実行される `IStartupFilter` の実装が 1 つまたは複数あるミドルウェアを、ライブラリで追加することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-184">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="36985-185">ライブラリの `IStartupFilter` よって追加されたミドルウェアの前に `IStartupFilter` ミドルウェアを呼び出すには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="36985-185">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="36985-186">サービス コンテナーにライブラリを追加する前に、サービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="36985-186">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="36985-187">後で呼び出すために、ライブラリが追加された後にサービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="36985-187">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="36985-188">外部アセンブリからの起動時に構成を追加する</span><span class="sxs-lookup"><span data-stu-id="36985-188">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="36985-189"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="36985-189">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="36985-190">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-190">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36985-191">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="36985-191">Additional resources</span></span>

* [<span data-ttu-id="36985-192">ホスト</span><span class="sxs-lookup"><span data-stu-id="36985-192">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="36985-193">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="36985-193">The Startup class</span></span>

<span data-ttu-id="36985-194">ASP.NET Core アプリケーションでは `Startup` クラスが使用されています。このクラスは規約に従って `Startup` と名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="36985-194">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="36985-195">`Startup` クラスには次の特徴があります。</span><span class="sxs-lookup"><span data-stu-id="36985-195">The `Startup` class:</span></span>

* <span data-ttu-id="36985-196">必要に応じて <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッドを含め、アプリの*サービス*を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-196">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="36985-197">サービスとは、アプリ機能を提供する再利用可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="36985-197">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="36985-198">サービスは  *に*登録され`ConfigureServices`、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> を介してアプリ全体で利用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-198">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="36985-199">アプリの要求処理パイプラインを作成するために <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドを含めます。</span><span class="sxs-lookup"><span data-stu-id="36985-199">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="36985-200">`ConfigureServices` と `Configure` はアプリの起動時に ASP.NET Core ランタイムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36985-200">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

<span data-ttu-id="36985-201">アプリの`Startup`ホスト[がビルドされるときに、](xref:fundamentals/index#host) クラスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-201">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="36985-202">`Startup` クラスは通常、ホスト ビルダーで [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) メソッドを呼び出すことで指定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-202">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="36985-203">ホストには、`Startup` クラス コンストラクターで使用できるサービスが用意されています。</span><span class="sxs-lookup"><span data-stu-id="36985-203">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="36985-204">アプリケーションから `ConfigureServices` 経由でサービスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-204">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="36985-205">これで、ホスト サービスとアプリケーション サービスの両方が `Configure` 内とアプリ全体で使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="36985-205">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="36985-206">[ クラスへの](xref:fundamentals/dependency-injection)依存関係挿入`Startup`の一般的な用途は、以下を挿入する場合です。</span><span class="sxs-lookup"><span data-stu-id="36985-206">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="36985-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> (環境別にサービスを構成するため)。</span><span class="sxs-lookup"><span data-stu-id="36985-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="36985-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> (構成を読み取るため)。</span><span class="sxs-lookup"><span data-stu-id="36985-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="36985-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> (`Startup.ConfigureServices` でロガーを作成するため)。</span><span class="sxs-lookup"><span data-stu-id="36985-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="36985-210">ほとんどのサービスは、`Configure` メソッドが呼び出されるまで使用できません。</span><span class="sxs-lookup"><span data-stu-id="36985-210">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="36985-211">マルチ スタートアップ</span><span class="sxs-lookup"><span data-stu-id="36985-211">Multiple Startup</span></span>

<span data-ttu-id="36985-212">アプリケーションの環境 (たとえば `Startup`) ごとに個別の `StartupDevelopment` クラスが定義されると、実行時に適切な `Startup` クラスが選択されます。</span><span class="sxs-lookup"><span data-stu-id="36985-212">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="36985-213">名前のサフィックスが現在の環境と一致するクラスが優先されます。</span><span class="sxs-lookup"><span data-stu-id="36985-213">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="36985-214">アプリケーションが Development 環境で実行され、`Startup` クラスと `StartupDevelopment` クラスの両方が含まれている場合は、`StartupDevelopment` クラスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-214">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="36985-215">詳細については、「[Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods)」(複数の環境の使用) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-215">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="36985-216">ホストの詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-216">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="36985-217">スタートアップ時のエラー処理については、「[Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling)」(スタートアップ例外処理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-217">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="36985-218">ConfigureServices メソッド</span><span class="sxs-lookup"><span data-stu-id="36985-218">The ConfigureServices method</span></span>

<span data-ttu-id="36985-219"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="36985-219">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="36985-220">省略可能。</span><span class="sxs-lookup"><span data-stu-id="36985-220">Optional.</span></span>
* <span data-ttu-id="36985-221">アプリケーションのサービスを構成する `Configure` メソッドの前にホストによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36985-221">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="36985-222">規約によって[構成オプション](xref:fundamentals/configuration/index)が設定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-222">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="36985-223">ホストでは、`Startup` メソッドが呼び出される前にいくつかのサービスを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-223">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="36985-224">詳細については、「[ホスト](xref:fundamentals/index#host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-224">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="36985-225">多くの設定が必要な機能には、`Add{Service}` 上の <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 拡張メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="36985-225">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="36985-226">たとえば、**Add**DbContext、**Add**DefaultIdentity、**Add**EntityFrameworkStores、**Add**RazorPages です。</span><span class="sxs-lookup"><span data-stu-id="36985-226">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="36985-227">サービス コンテナーにサービスを追加すると、アプリケーション内と `Configure` メソッド内でサービスを利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="36985-227">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="36985-228">サービスは[依存関係の挿入](xref:fundamentals/dependency-injection)または <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> によって解決されます。</span><span class="sxs-lookup"><span data-stu-id="36985-228">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="36985-229">[ について詳しくは、「](xref:mvc/compatibility-version)SetCompatibilityVersion`SetCompatibilityVersion`」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="36985-229">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="36985-230">Configure メソッド</span><span class="sxs-lookup"><span data-stu-id="36985-230">The Configure method</span></span>

<span data-ttu-id="36985-231"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> メソッドは、アプリケーションが HTTP 要求にどのように応答するかを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-231">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="36985-232">要求パイプラインは、[ミドルウェア](xref:fundamentals/middleware/index) コンポーネントを <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> インスタンスに追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="36985-232">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="36985-233">`IApplicationBuilder` は `Configure` メソッドから使用できますが、サービス コンテナーに登録されていません。</span><span class="sxs-lookup"><span data-stu-id="36985-233">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="36985-234">ホスティングによって `IApplicationBuilder` が作成され、`Configure` に直接渡されます。</span><span class="sxs-lookup"><span data-stu-id="36985-234">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="36985-235">[ASP.NET Core テンプレート](/dotnet/core/tools/dotnet-new)では、次をサポートするパイプラインが構成されます。</span><span class="sxs-lookup"><span data-stu-id="36985-235">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="36985-236">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="36985-236">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="36985-237">例外ハンドラー</span><span class="sxs-lookup"><span data-stu-id="36985-237">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="36985-238">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="36985-238">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="36985-239">HTTPS リダイレクト</span><span class="sxs-lookup"><span data-stu-id="36985-239">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="36985-240">静的ファイル</span><span class="sxs-lookup"><span data-stu-id="36985-240">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="36985-241">ASP.NET Core [MVC](xref:mvc/overview) と [Razor ページ](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="36985-241">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>
* [<span data-ttu-id="36985-242">一般データ保護規制 (GDPR)</span><span class="sxs-lookup"><span data-stu-id="36985-242">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="36985-243">各 `Use` 拡張メソッドによって、要求パイプラインに 1 つまたは複数のミドルウェア コンポーネントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-243">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="36985-244">たとえば、<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> では、[静的ファイル](xref:fundamentals/middleware/index)を提供するように、[ミドルウェア](xref:fundamentals/static-files)を構成します。</span><span class="sxs-lookup"><span data-stu-id="36985-244">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="36985-245">要求パイプライン内の各ミドルウェア コンポーネントは、パイプラインの次のコンポーネントを呼び出すか、妥当な場合はチェーンをショートさせます。</span><span class="sxs-lookup"><span data-stu-id="36985-245">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="36985-246">`IHostingEnvironment` や `ILoggerFactory`、または `ConfigureServices` で定義された任意のものなどの追加サービスを `Configure` メソッド シグネチャで指定できます。</span><span class="sxs-lookup"><span data-stu-id="36985-246">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="36985-247">使用可能な場合、これらのサービスが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="36985-247">These services are injected if they're available.</span></span>

<span data-ttu-id="36985-248">`IApplicationBuilder` の使用方法およびミドルウェアの処理の順序については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-248">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="36985-249">スタートアップを使用せずにサービスを構成する</span><span class="sxs-lookup"><span data-stu-id="36985-249">Configure services without Startup</span></span>

<span data-ttu-id="36985-250">`Startup` クラスを使用せず、サービスと要求処理パイプラインを構成するには、ホスト ビルダーで便利なメソッド、`ConfigureServices` と `Configure` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="36985-250">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="36985-251">`ConfigureServices` の複数回の呼び出しでは、互いに追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-251">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="36985-252">`Configure` メソッドが複数回呼び出された場合、最後の `Configure` 呼び出しが使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-252">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="36985-253">スタートアップ フィルターを使用した Startup の拡張</span><span class="sxs-lookup"><span data-stu-id="36985-253">Extend Startup with startup filters</span></span>

<span data-ttu-id="36985-254"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter> を使用して次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="36985-254">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="36985-255">[ を明示的に呼び出さずに、アプリの ](#the-configure-method)Configure`Use{Middleware}` ミドルウェア パイプラインの先頭または末尾でミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="36985-255">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="36985-256">`IStartupFilter` は、アプリの作成者が既定のミドルウェアを明示的に登録する必要がないよう、パイプラインの先頭に既定値を追加するために、ASP.NET Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="36985-256">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="36985-257">`IStartupFilter` は、アプリの作成者に代わって、別のコンポーネント呼び出し `Use{Middleware}` を許可します。</span><span class="sxs-lookup"><span data-stu-id="36985-257">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="36985-258">`Configure` メソッドのパイプラインを作成します。</span><span class="sxs-lookup"><span data-stu-id="36985-258">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="36985-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) では、ライブラリによって追加されたミドルウェアの前後に実行するように、ミドルウェアを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="36985-260">`IStartupFilter` には、<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> を受け取って返す `Action<IApplicationBuilder>` が実装されています。</span><span class="sxs-lookup"><span data-stu-id="36985-260">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="36985-261"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> で、アプリケーションの要求パイプラインを構成するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="36985-261">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="36985-262">詳細については、「[Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)」(IApplicationBuilder を使用したミドルウェア パイプラインの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-262">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="36985-263">各 `IStartupFilter` によって、要求パイプラインで 1 つまたは複数のミドルウェアを追加できます。</span><span class="sxs-lookup"><span data-stu-id="36985-263">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="36985-264">フィルターは、サービス コンテナーに追加された順に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="36985-264">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="36985-265">フィルターは、コントロールを次のフィルターに渡す前または後にミドルウェアを追加できるため、アプリケーション パイプラインの先頭または末尾に追加されます。</span><span class="sxs-lookup"><span data-stu-id="36985-265">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="36985-266">`IStartupFilter` でミドルウェアを登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="36985-266">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="36985-267">`RequestSetOptionsMiddleware` ミドルウェアによって、クエリ文字列パラメーターからオプション値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="36985-267">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="36985-268">`RequestSetOptionsMiddleware` は `RequestSetOptionsStartupFilter` クラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="36985-268">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="36985-269">`IStartupFilter` は <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> のサービス コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="36985-269">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="36985-270">`option` のクエリ文字列パラメーターを指定すると、ミドルウェアでは ASP.NET Core ミドルウェアによって応答がレンダリングされる前に値の割り当てを処理します。</span><span class="sxs-lookup"><span data-stu-id="36985-270">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="36985-271">ミドルウェアの実行順序は、`IStartupFilter` の登録順に設定されています。</span><span class="sxs-lookup"><span data-stu-id="36985-271">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="36985-272">複数の `IStartupFilter` の実装が、同じオブジェクトとやり取りする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="36985-272">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="36985-273">順序が重要な場合は、ミドルウェアの実行順序に合わせて `IStartupFilter` サービスの登録順序を指定してください。</span><span class="sxs-lookup"><span data-stu-id="36985-273">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="36985-274">`IStartupFilter` に登録された他のアプリケーション ミドルウェアの前または後に実行される `IStartupFilter` の実装が 1 つまたは複数あるミドルウェアを、ライブラリで追加することができます。</span><span class="sxs-lookup"><span data-stu-id="36985-274">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="36985-275">ライブラリの `IStartupFilter` よって追加されたミドルウェアの前に `IStartupFilter` ミドルウェアを呼び出すには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="36985-275">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="36985-276">サービス コンテナーにライブラリを追加する前に、サービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="36985-276">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="36985-277">後で呼び出すために、ライブラリが追加された後にサービス登録を配置する</span><span class="sxs-lookup"><span data-stu-id="36985-277">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="36985-278">外部アセンブリからの起動時に構成を追加する</span><span class="sxs-lookup"><span data-stu-id="36985-278">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="36985-279"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="36985-279">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="36985-280">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="36985-280">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36985-281">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="36985-281">Additional resources</span></span>

* [<span data-ttu-id="36985-282">ホスト</span><span class="sxs-lookup"><span data-stu-id="36985-282">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end