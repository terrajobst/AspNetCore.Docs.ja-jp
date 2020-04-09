---
title: ASP.NET Core の基礎
author: rick-anderson
description: ASP.NET Core アプリの構築に関する基本概念について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: da2b42a7cf5d116a36d1dd9fa586d40ab31fc52d
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417648"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="83a04-103">ASP.NET Core の基礎</span><span class="sxs-lookup"><span data-stu-id="83a04-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83a04-104">この記事では、ASP.NET Core アプリの開発方法を理解するための主要なトピックをまとめています。</span><span class="sxs-lookup"><span data-stu-id="83a04-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="83a04-105">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="83a04-105">The Startup class</span></span>

<span data-ttu-id="83a04-106">`Startup` クラスとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="83a04-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="83a04-107">アプリで必要なサービスが構成されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="83a04-108">要求を処理するアプリのパイプラインは、一連のミドルウェア コンポーネントとして定義されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="83a04-109">`Startup` クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="83a04-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="83a04-110">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="83a04-111">依存性の注入 (サービス)</span><span class="sxs-lookup"><span data-stu-id="83a04-111">Dependency injection (services)</span></span>

<span data-ttu-id="83a04-112">ASP.NET Core には、構成済みのサービスをアプリ全体で利用できるようにする依存性の注入 (DI) フレームワークが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="83a04-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="83a04-113">たとえば、ログ コンポーネントは、サービスです。</span><span class="sxs-lookup"><span data-stu-id="83a04-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="83a04-114">サービスを構成 (または*登録*) するコードが `Startup.ConfigureServices` メソッドに追加されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="83a04-115">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="83a04-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="83a04-116">サービスは通常、コンストラクター挿入を使用して DI から解決されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="83a04-117">コンストラクター挿入では、必要な型またはインターフェイスのコンストラクター パラメーターがクラスで宣言されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="83a04-118">DI フレームワークでは、実行時にこのサービスのインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="83a04-119">次の例では、コンストラクター挿入を使用して、DI から `RazorPagesMovieContext` を解決します。</span><span class="sxs-lookup"><span data-stu-id="83a04-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="83a04-120">組み込みの制御の反転 (IoC) コンテナーがアプリのすべてのニーズを満たしていない場合は、代わりにサードパーティの IoC コンテナーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="83a04-121">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="83a04-122">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="83a04-122">Middleware</span></span>

<span data-ttu-id="83a04-123">要求を処理するパイプラインは、一連のミドルウェア コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="83a04-124">各コンポーネントによって、`HttpContext` に対して操作が実行され、パイプラインの次のミドルウェアが呼び出されるか、または要求が終了されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="83a04-125">通常、ミドルウェア コンポーネントは、`Startup.Configure` メソッドの `Use...` 拡張メソッドを呼び出すことでパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="83a04-126">たとえば、静的ファイルのレンダリングを有効にするには、`UseStaticFiles` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="83a04-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="83a04-127">次の例では、要求を処理するパイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="83a04-128">ASP.NET Core には、豊富な組み込みミドルウェアのセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="83a04-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="83a04-129">カスタム ミドルウェア コンポーネントを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="83a04-130">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="83a04-131">Host</span><span class="sxs-lookup"><span data-stu-id="83a04-131">Host</span></span>

<span data-ttu-id="83a04-132">起動時に、ASP.NET Core アプリによって*ホスト*がビルドされます。</span><span class="sxs-lookup"><span data-stu-id="83a04-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="83a04-133">ホストにより、次のようなアプリのすべてのリソースがカプセル化されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="83a04-134">HTTP サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="83a04-134">An HTTP server implementation</span></span>
* <span data-ttu-id="83a04-135">ミドルウェア コンポーネント</span><span class="sxs-lookup"><span data-stu-id="83a04-135">Middleware components</span></span>
* <span data-ttu-id="83a04-136">ログの記録</span><span class="sxs-lookup"><span data-stu-id="83a04-136">Logging</span></span>
* <span data-ttu-id="83a04-137">依存性の注入 (DI) サービス</span><span class="sxs-lookup"><span data-stu-id="83a04-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="83a04-138">構成</span><span class="sxs-lookup"><span data-stu-id="83a04-138">Configuration</span></span>

<span data-ttu-id="83a04-139">2 つの異なるホストがあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-139">There are two different hosts:</span></span> 

* <span data-ttu-id="83a04-140">.NET での汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="83a04-140">.NET Generic Host</span></span>
* <span data-ttu-id="83a04-141">ASP.NET Core の Web ホスト</span><span class="sxs-lookup"><span data-stu-id="83a04-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="83a04-142">.NET での汎用ホストをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83a04-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="83a04-143">ASP.NET Core の Web ホストは、下位互換性のためにのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="83a04-144">次の例では、.NET での汎用ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="83a04-145">`CreateDefaultBuilder` と `ConfigureWebHostDefaults` メソッドでは、次のような既定のオプションのセットを使用してホストが構成されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="83a04-146">Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="83a04-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="83a04-147">*appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="83a04-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="83a04-148">ログ出力をコンソールとデバッグ プロバイダーに送ります。</span><span class="sxs-lookup"><span data-stu-id="83a04-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="83a04-149">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="83a04-150">Web 以外のシナリオ</span><span class="sxs-lookup"><span data-stu-id="83a04-150">Non-web scenarios</span></span>

<span data-ttu-id="83a04-151">汎用ホストにより、他の種類のアプリで、ログ記録、依存性の注入 (DI)、構成、およびアプリの有効期間管理などの横断的なフレームワーク拡張機能を使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="83a04-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="83a04-152">詳細については、次のトピックを参照してください。 <xref:fundamentals/host/generic-host> および <xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="83a04-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="83a04-153">サーバー</span><span class="sxs-lookup"><span data-stu-id="83a04-153">Servers</span></span>

<span data-ttu-id="83a04-154">ASP.NET Core アプリは、HTTP 要求をリッスンするために HTTP サーバー実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="83a04-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="83a04-155">サーバーは、`HttpContext` に構成した[要求機能](xref:fundamentals/request-features)のセットとして、アプリへの要求を公開します。</span><span class="sxs-lookup"><span data-stu-id="83a04-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="83a04-156">Windows</span><span class="sxs-lookup"><span data-stu-id="83a04-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="83a04-157">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="83a04-158">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="83a04-159">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="83a04-160">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="83a04-161">*IIS HTTP サーバー*は、IIS を使用する Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="83a04-162">このサーバーでは、ASP.NET Core アプリと IIS が同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="83a04-163">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="83a04-164">macOS</span><span class="sxs-lookup"><span data-stu-id="83a04-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="83a04-165">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83a04-166">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83a04-167">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="83a04-168">Linux</span><span class="sxs-lookup"><span data-stu-id="83a04-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="83a04-169">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83a04-170">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83a04-171">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="83a04-172">詳細については、「<xref:fundamentals/servers/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="83a04-173">構成</span><span class="sxs-lookup"><span data-stu-id="83a04-173">Configuration</span></span>

<span data-ttu-id="83a04-174">ASP.NET Core は、構成プロバイダーの順序付けされたセットから、名前と値のペアの設定を取得する構成フレームワークとなります。</span><span class="sxs-lookup"><span data-stu-id="83a04-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="83a04-175">組み込み構成プロバイダーは、 *.json* ファイル、 *.xml* ファイル、環境変数、コマンドライン引数などのさまざまなソースで使用できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="83a04-176">他のソースをサポートするには、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="83a04-177">[既定](xref:fundamentals/configuration/index#default)では、ASP.NET Core アプリは *appsettings. json*、環境変数、コマンドラインなどから読み取るように構成されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="83a04-178">アプリの構成が読み込まれると、環境変数の値によって *appsettings.json* の値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="83a04-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="83a04-179">関連する構成値を読み取る方法としては、[オプション パターン](xref:fundamentals/configuration/options)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83a04-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="83a04-180">詳細については、「[オプションパターンを使用して、階層型の構成データをバインドします](xref:fundamentals/configuration/index#optpat)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="83a04-181">ASP.NET Core には、パスワードなどの機密の構成データの管理に[シークレット マネージャー](xref:security/app-secrets#secret-manager)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="83a04-182">実稼働の機密情報には、[Azure Key Vault](xref:security/key-vault-configuration) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83a04-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="83a04-183">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="83a04-184">環境</span><span class="sxs-lookup"><span data-stu-id="83a04-184">Environments</span></span>

<span data-ttu-id="83a04-185">`Development`、`Staging`、`Production` などの実行環境は ASP.NET Core の最上の概念です。</span><span class="sxs-lookup"><span data-stu-id="83a04-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="83a04-186">アプリが実行している環境は、`ASPNETCORE_ENVIRONMENT` 環境変数を設定することにより指定します。</span><span class="sxs-lookup"><span data-stu-id="83a04-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="83a04-187">ASP.NET Core は、アプリの起動時にその環境変数を読み取り、その値を `IWebHostEnvironment` 実装に格納します。</span><span class="sxs-lookup"><span data-stu-id="83a04-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="83a04-188">この実装は、依存性の注入 (DI) を介して、アプリ内の任意の場所で使用できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="83a04-189">次の例では、`Development` 環境で実行するときに詳細なエラー情報を提供するようにアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="83a04-190">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="83a04-191">ログの記録</span><span class="sxs-lookup"><span data-stu-id="83a04-191">Logging</span></span>

<span data-ttu-id="83a04-192">ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="83a04-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="83a04-193">利用可能なプロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="83a04-193">Available providers include:</span></span>

* <span data-ttu-id="83a04-194">コンソール</span><span class="sxs-lookup"><span data-stu-id="83a04-194">Console</span></span>
* <span data-ttu-id="83a04-195">デバッグ</span><span class="sxs-lookup"><span data-stu-id="83a04-195">Debug</span></span>
* <span data-ttu-id="83a04-196">Event Tracing on Windows</span><span class="sxs-lookup"><span data-stu-id="83a04-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="83a04-197">Windows イベント ログ</span><span class="sxs-lookup"><span data-stu-id="83a04-197">Windows Event Log</span></span>
* <span data-ttu-id="83a04-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="83a04-198">TraceSource</span></span>
* <span data-ttu-id="83a04-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="83a04-199">Azure App Service</span></span>
* <span data-ttu-id="83a04-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="83a04-200">Azure Application Insights</span></span>

<span data-ttu-id="83a04-201">ログを作成するには、依存性の挿入 (DI) から <xref:Microsoft.Extensions.Logging.ILogger%601> サービスを解決し、<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> などのログ メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="83a04-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="83a04-202">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="83a04-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="83a04-203">`LogInformation` などのログ メソッドでは、任意の数のフィールドがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="83a04-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="83a04-204">これらのフィールドは、一般的にメッセージ `string` を構築するために使用しますが、一部のログ プロバイダーは、それらを個別のフィールドとしてデータ ストアに送信します。</span><span class="sxs-lookup"><span data-stu-id="83a04-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="83a04-205">この機能は、[構造化ロギングとも呼ばれるセマンティック ロギング](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をログ プロバイダーが実装するのを可能にします。</span><span class="sxs-lookup"><span data-stu-id="83a04-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="83a04-206">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="83a04-207">ルーティング</span><span class="sxs-lookup"><span data-stu-id="83a04-207">Routing</span></span>

<span data-ttu-id="83a04-208">*ルート*とは、ハンドラーにマップされている URL のパターンです。</span><span class="sxs-lookup"><span data-stu-id="83a04-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="83a04-209">このハンドラーは一般的には Razor Pages、MVC コントローラーのアクション メソッドまたはミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="83a04-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="83a04-210">ASP.NET Core のルーティングでは、アプリで使用する URL を制御できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="83a04-211">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="83a04-212">エラー処理</span><span class="sxs-lookup"><span data-stu-id="83a04-212">Error handling</span></span>

<span data-ttu-id="83a04-213">ASP.NET Core には、次などのエラー処理用の機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="83a04-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="83a04-214">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="83a04-214">A developer exception page</span></span>
* <span data-ttu-id="83a04-215">カスタム エラー ページ</span><span class="sxs-lookup"><span data-stu-id="83a04-215">Custom error pages</span></span>
* <span data-ttu-id="83a04-216">静的状態コード ページ</span><span class="sxs-lookup"><span data-stu-id="83a04-216">Static status code pages</span></span>
* <span data-ttu-id="83a04-217">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="83a04-217">Startup exception handling</span></span>

<span data-ttu-id="83a04-218">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="83a04-219">HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="83a04-219">Make HTTP requests</span></span>

<span data-ttu-id="83a04-220">`HttpClient` インスタンスの作成に、`IHttpClientFactory` の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="83a04-221">ファクトリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="83a04-221">The factory:</span></span>

* <span data-ttu-id="83a04-222">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="83a04-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="83a04-223">たとえば、GitHub にアクセスするために、*github* クライアントを登録して構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="83a04-224">既定のクライアントを別の目的で登録して構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="83a04-225">複数のデリゲート ハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築するのをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="83a04-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="83a04-226">このパターンは、ASP.NET Core の受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="83a04-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="83a04-227">このパターンでは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムが提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="83a04-228">一時的な障害処理用の人気のサードパーティ製ライブラリ、*Polly* と統合できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="83a04-229">基になっている `HttpClientHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="83a04-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="83a04-230">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、構成可能なログ エクスペリエンスを <xref:Microsoft.Extensions.Logging.ILogger> を介して追加します。</span><span class="sxs-lookup"><span data-stu-id="83a04-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="83a04-231">詳細については、「<xref:fundamentals/http-requests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="83a04-232">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="83a04-232">Content root</span></span>

<span data-ttu-id="83a04-233">コンテンツ ルートは、以下に対する基本パスです。</span><span class="sxs-lookup"><span data-stu-id="83a04-233">The content root is the base path for:</span></span>

* <span data-ttu-id="83a04-234">アプリをホストしている実行可能ファイル ( *.exe*)。</span><span class="sxs-lookup"><span data-stu-id="83a04-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="83a04-235">アプリを構成するコンパイル済みアセンブリ ( *.dll*)。</span><span class="sxs-lookup"><span data-stu-id="83a04-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="83a04-236">次のような、アプリで使用されるコンテンツ ファイル。</span><span class="sxs-lookup"><span data-stu-id="83a04-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="83a04-237">Razor ファイル ( *.cshtml*、 *.razor*)</span><span class="sxs-lookup"><span data-stu-id="83a04-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="83a04-238">構成ファイル ( *.json*、 *.xml*)</span><span class="sxs-lookup"><span data-stu-id="83a04-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="83a04-239">データ ファイル ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="83a04-239">Data files (*.db*)</span></span>
* <span data-ttu-id="83a04-240">[Web ルート](#web-root) (通常は *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="83a04-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="83a04-241">開発中、コンテンツ ルートの既定値は、プロジェクトのルート ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="83a04-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="83a04-242">このディレクトリは、アプリのコンテンツ ファイルと [Web ルート](#web-root)の両方の基本パスでもあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="83a04-243">[ホストを構築するとき](#host)は、それ自体のパスを設定して別のコンテンツ ルートを指定します。</span><span class="sxs-lookup"><span data-stu-id="83a04-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="83a04-244">詳細については、[コンテンツ ルート](xref:fundamentals/host/generic-host#contentrootpath-1)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentrootpath-1).</span></span>

## <a name="web-root"></a><span data-ttu-id="83a04-245">Web ルート</span><span class="sxs-lookup"><span data-stu-id="83a04-245">Web root</span></span>

<span data-ttu-id="83a04-246">Web ルートは、次のような、パブリックで静的なリソース ファイルへの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="83a04-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="83a04-247">スタイルシート ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="83a04-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="83a04-248">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="83a04-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="83a04-249">画像 ( *.png*、*jpg*)</span><span class="sxs-lookup"><span data-stu-id="83a04-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="83a04-250">既定では、静的ファイルは Web ルート ディレクトリとそのサブディレクトリからのみ提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="83a04-251">Web ルートのパスの既定値は、 *{コンテンツ ルート}/wwwroot* です。</span><span class="sxs-lookup"><span data-stu-id="83a04-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="83a04-252">[ホストを構築するとき](#host)は、それ自体のパスを設定して別の Web ルートを指定します。</span><span class="sxs-lookup"><span data-stu-id="83a04-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="83a04-253">詳細については、「[Web ルート](xref:fundamentals/host/generic-host#webroot-1)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot-1).</span></span>

<span data-ttu-id="83a04-254">プロジェクト ファイル内の [\<コンテンツ > プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items#content) を使用して *wwwroot* にファイルを発行できないようにします。</span><span class="sxs-lookup"><span data-stu-id="83a04-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="83a04-255">次の例では、*wwwroot/local* とそのサブディレクトリにコンテンツを公開しないようにします。</span><span class="sxs-lookup"><span data-stu-id="83a04-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="83a04-256">Razor *.cshtml* ファイルの場合、チルダとスラッシュ (`~/`) が Web ルートを指します。</span><span class="sxs-lookup"><span data-stu-id="83a04-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="83a04-257">`~/` で始まるパスは、"*仮想パス*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="83a04-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="83a04-258">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83a04-259">この記事では、ASP.NET Core アプリの開発方法を理解するための主要なトピックをまとめています。</span><span class="sxs-lookup"><span data-stu-id="83a04-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="83a04-260">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="83a04-260">The Startup class</span></span>

<span data-ttu-id="83a04-261">`Startup` クラスとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="83a04-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="83a04-262">アプリで必要なサービスが構成されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="83a04-263">要求を処理するパイプラインが定義されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="83a04-264">*サービス*とは、アプリが使用するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="83a04-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="83a04-265">たとえば、ログ コンポーネントは、サービスです。</span><span class="sxs-lookup"><span data-stu-id="83a04-265">For example, a logging component is a service.</span></span> <span data-ttu-id="83a04-266">サービスを構成 (または*登録*) するコードが `Startup.ConfigureServices` メソッドに追加されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="83a04-267">要求を処理するパイプラインは、一連の*ミドルウェア* コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="83a04-268">たとえば、ミドルウェアは、静的ファイルに対する要求を処理したり、HTTPS に HTTP 要求をリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="83a04-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="83a04-269">各ミドルウェアは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="83a04-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="83a04-270">`Startup.Configure` メソッドには、要求を処理するパイプラインを構成するコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="83a04-271">`Startup` クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="83a04-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="83a04-272">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="83a04-273">依存性の注入 (サービス)</span><span class="sxs-lookup"><span data-stu-id="83a04-273">Dependency injection (services)</span></span>

<span data-ttu-id="83a04-274">ASP.NET Core には、アプリのクラスが構成済みのサービスを利用できるようにする依存性の注入 (DI) フレームワークが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="83a04-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="83a04-275">クラスのサービスのインスタンスを取得する 1 つの方法は、必要な型のパラメーターを使用したコンストラクターを作成することです。</span><span class="sxs-lookup"><span data-stu-id="83a04-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="83a04-276">このパラメーターには、サービスの種類またはインターフェイスが可能です。</span><span class="sxs-lookup"><span data-stu-id="83a04-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="83a04-277">DI システムは、実行時にこのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="83a04-278">Entity Framework Core コンテキスト オブジェクトを取得するために DI を使用するクラスを次に示します。</span><span class="sxs-lookup"><span data-stu-id="83a04-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="83a04-279">強調表示されている行は、コンストラクターの挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="83a04-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="83a04-280">DI が組み込まれており、必要に応じてサードパーティ製の制御の反転 (IoC) コンテナーを組み込むことができるよう設計されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="83a04-281">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="83a04-282">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="83a04-282">Middleware</span></span>

<span data-ttu-id="83a04-283">要求を処理するパイプラインは、一連のミドルウェア コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="83a04-284">各コンポーネントは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="83a04-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="83a04-285">通常、ミドルウェア コンポーネントは、`Startup.Configure` メソッドのその `Use...` 拡張メソッドを呼び出してパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="83a04-286">たとえば、静的ファイルのレンダリングを有効にするには、`UseStaticFiles` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="83a04-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="83a04-287">次の例の強調表示されているコードは、要求を処理するパイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="83a04-288">ASP.NET Core にはミドルウェアのセットが豊富に組み込まれており、カスタム ミドルウェアをユーザーが記述できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="83a04-289">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="83a04-290">Host</span><span class="sxs-lookup"><span data-stu-id="83a04-290">Host</span></span>

<span data-ttu-id="83a04-291">ASP.NET Core アプリは起動時に*ホスト*をビルドします。</span><span class="sxs-lookup"><span data-stu-id="83a04-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="83a04-292">ホストとは、次などのアプリのすべてのリソースをカプセル化するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="83a04-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="83a04-293">HTTP サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="83a04-293">An HTTP server implementation</span></span>
* <span data-ttu-id="83a04-294">ミドルウェア コンポーネント</span><span class="sxs-lookup"><span data-stu-id="83a04-294">Middleware components</span></span>
* <span data-ttu-id="83a04-295">ログの記録</span><span class="sxs-lookup"><span data-stu-id="83a04-295">Logging</span></span>
* <span data-ttu-id="83a04-296">DI</span><span class="sxs-lookup"><span data-stu-id="83a04-296">DI</span></span>
* <span data-ttu-id="83a04-297">構成</span><span class="sxs-lookup"><span data-stu-id="83a04-297">Configuration</span></span>

<span data-ttu-id="83a04-298">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="83a04-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="83a04-299">Web ホストと汎用ホストの 2 つのホストが利用可能です。</span><span class="sxs-lookup"><span data-stu-id="83a04-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="83a04-300">ASP.NET core 2.x では、汎用ホストは Web 以外のシナリオのみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="83a04-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="83a04-301">ホストを作成するコードは `Program.Main` にあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="83a04-302">`CreateDefaultBuilder` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="83a04-303">Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="83a04-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="83a04-304">*appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="83a04-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="83a04-305">ログ出力をコンソールとデバッグ プロバイダーに送ります。</span><span class="sxs-lookup"><span data-stu-id="83a04-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="83a04-306">詳細については、「<xref:fundamentals/host/web-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="83a04-307">Web 以外のシナリオ</span><span class="sxs-lookup"><span data-stu-id="83a04-307">Non-web scenarios</span></span>

<span data-ttu-id="83a04-308">汎用ホストにより、他の種類のアプリで、ログ記録、依存性の注入 (DI)、構成、およびアプリの有効期間管理などの横断的なフレームワーク拡張機能を使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="83a04-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="83a04-309">詳細については、次のトピックを参照してください。 <xref:fundamentals/host/generic-host> および <xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="83a04-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="83a04-310">サーバー</span><span class="sxs-lookup"><span data-stu-id="83a04-310">Servers</span></span>

<span data-ttu-id="83a04-311">ASP.NET Core アプリは、HTTP 要求をリッスンするために HTTP サーバー実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="83a04-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="83a04-312">サーバーは、`HttpContext` に構成した[要求機能](xref:fundamentals/request-features)のセットとして、アプリへの要求を公開します。</span><span class="sxs-lookup"><span data-stu-id="83a04-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="83a04-313">Windows</span><span class="sxs-lookup"><span data-stu-id="83a04-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="83a04-314">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="83a04-315">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="83a04-316">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="83a04-317">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="83a04-318">*IIS HTTP サーバー*は、IIS を使用する Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="83a04-319">このサーバーでは、ASP.NET Core アプリと IIS が同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="83a04-320">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="83a04-321">macOS</span><span class="sxs-lookup"><span data-stu-id="83a04-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="83a04-322">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83a04-323">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83a04-324">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="83a04-325">Linux</span><span class="sxs-lookup"><span data-stu-id="83a04-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="83a04-326">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83a04-327">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83a04-328">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="83a04-329">Windows</span><span class="sxs-lookup"><span data-stu-id="83a04-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="83a04-330">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="83a04-331">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="83a04-332">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="83a04-333">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="83a04-334">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="83a04-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="83a04-335">macOS</span><span class="sxs-lookup"><span data-stu-id="83a04-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="83a04-336">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83a04-337">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83a04-338">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="83a04-339">Linux</span><span class="sxs-lookup"><span data-stu-id="83a04-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="83a04-340">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83a04-341">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83a04-342">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="83a04-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83a04-343">詳細については、「<xref:fundamentals/servers/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="83a04-344">構成</span><span class="sxs-lookup"><span data-stu-id="83a04-344">Configuration</span></span>

<span data-ttu-id="83a04-345">ASP.NET Core は、構成プロバイダーの順序付けされたセットから、名前と値のペアの設定を取得する構成フレームワークとなります。</span><span class="sxs-lookup"><span data-stu-id="83a04-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="83a04-346">*.json* ファイル、 *.xml* ファイル、環境変数、コマンドライン引数など、さまざまなソース用に構成プロバイダーが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="83a04-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="83a04-347">独自のカスタム構成プロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="83a04-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="83a04-348">たとえば、構成は *appsettings.json* と環境変数から取得したものであると指定できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="83a04-349">このとき *ConnectionString* 値が要求されると、フレームワークはまず *appsettings.json* ファイルを参照します。</span><span class="sxs-lookup"><span data-stu-id="83a04-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="83a04-350">値がそこにあり、しかし環境変数にもある場合、環境変数の値が優先されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="83a04-351">ASP.NET Core には、パスワードなどの機密の構成データの管理に[シークレット マネージャー ツール](xref:security/app-secrets)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="83a04-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="83a04-352">実稼働の機密情報には、[Azure Key Vault](xref:security/key-vault-configuration) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="83a04-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="83a04-353">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="83a04-354">オプション</span><span class="sxs-lookup"><span data-stu-id="83a04-354">Options</span></span>

<span data-ttu-id="83a04-355">ASP.NET Core では、構成値の格納と取得に、可能な限り*オプション パターン*を使用します。</span><span class="sxs-lookup"><span data-stu-id="83a04-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="83a04-356">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="83a04-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="83a04-357">たとえば、以下のコードでは WebSockets のオプションが設定されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="83a04-358">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="83a04-359">環境</span><span class="sxs-lookup"><span data-stu-id="83a04-359">Environments</span></span>

<span data-ttu-id="83a04-360">*開発*、*ステージング*、および*実稼働*などの実行環境は ASP.NET Core の最上の概念です。</span><span class="sxs-lookup"><span data-stu-id="83a04-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="83a04-361">アプリが実行している環境は、`ASPNETCORE_ENVIRONMENT` 環境変数を設定することにより指定できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="83a04-362">ASP.NET Core は、アプリの起動時にその環境変数を読み取り、その値を `IHostingEnvironment` 実装に格納します。</span><span class="sxs-lookup"><span data-stu-id="83a04-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="83a04-363">この環境オブジェクトは、DI を介しアプリの任意の場所で使用されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="83a04-364">`Startup` クラスの次のサンプル コードは、それが開発環境で実行された場合のみ、詳細なエラー情報を提供するようアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="83a04-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="83a04-365">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="83a04-366">ログの記録</span><span class="sxs-lookup"><span data-stu-id="83a04-366">Logging</span></span>

<span data-ttu-id="83a04-367">ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="83a04-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="83a04-368">利用可能なプロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="83a04-368">Available providers include the following:</span></span>

* <span data-ttu-id="83a04-369">コンソール</span><span class="sxs-lookup"><span data-stu-id="83a04-369">Console</span></span>
* <span data-ttu-id="83a04-370">デバッグ</span><span class="sxs-lookup"><span data-stu-id="83a04-370">Debug</span></span>
* <span data-ttu-id="83a04-371">Event Tracing on Windows</span><span class="sxs-lookup"><span data-stu-id="83a04-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="83a04-372">Windows イベント ログ</span><span class="sxs-lookup"><span data-stu-id="83a04-372">Windows Event Log</span></span>
* <span data-ttu-id="83a04-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="83a04-373">TraceSource</span></span>
* <span data-ttu-id="83a04-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="83a04-374">Azure App Service</span></span>
* <span data-ttu-id="83a04-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="83a04-375">Azure Application Insights</span></span>

<span data-ttu-id="83a04-376">DI からの `ILogger` オブジェクトの取得およびログ メソッドの呼び出しによってアプリのコードの任意の場所からログを記述します。</span><span class="sxs-lookup"><span data-stu-id="83a04-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="83a04-377">コンストラクターの挿入とログ記録メソッドの呼び出しが強調表示されている `ILogger` オブジェクトを使用するサンプル コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="83a04-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="83a04-378">`ILogger` インターフェイスは、ログ プロバイダーに任意の数のフィールドを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="83a04-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="83a04-379">このフィールドは、一般的にメッセージの文字列を構築するために使用しますが、プロバイダーがデータ ストアに別のフィールドとして送信することも可能です。</span><span class="sxs-lookup"><span data-stu-id="83a04-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="83a04-380">この機能は、[構造化ロギングとも呼ばれるセマンティック ロギング](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をログ プロバイダーが実装するのを可能にします。</span><span class="sxs-lookup"><span data-stu-id="83a04-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="83a04-381">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="83a04-382">ルーティング</span><span class="sxs-lookup"><span data-stu-id="83a04-382">Routing</span></span>

<span data-ttu-id="83a04-383">*ルート*とは、ハンドラーにマップされている URL のパターンです。</span><span class="sxs-lookup"><span data-stu-id="83a04-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="83a04-384">このハンドラーは一般的には Razor Pages、MVC コントローラーのアクション メソッドまたはミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="83a04-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="83a04-385">ASP.NET Core のルーティングでは、アプリで使用する URL を制御できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="83a04-386">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="83a04-387">エラー処理</span><span class="sxs-lookup"><span data-stu-id="83a04-387">Error handling</span></span>

<span data-ttu-id="83a04-388">ASP.NET Core には、次などのエラー処理用の機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="83a04-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="83a04-389">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="83a04-389">A developer exception page</span></span>
* <span data-ttu-id="83a04-390">カスタム エラー ページ</span><span class="sxs-lookup"><span data-stu-id="83a04-390">Custom error pages</span></span>
* <span data-ttu-id="83a04-391">静的状態コード ページ</span><span class="sxs-lookup"><span data-stu-id="83a04-391">Static status code pages</span></span>
* <span data-ttu-id="83a04-392">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="83a04-392">Startup exception handling</span></span>

<span data-ttu-id="83a04-393">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="83a04-394">HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="83a04-394">Make HTTP requests</span></span>

<span data-ttu-id="83a04-395">`HttpClient` インスタンスの作成に、`IHttpClientFactory` の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="83a04-396">ファクトリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="83a04-396">The factory:</span></span>

* <span data-ttu-id="83a04-397">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="83a04-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="83a04-398">たとえば、*github* クライアントを登録して、GitHub にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="83a04-399">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="83a04-400">複数のデリゲート ハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築するのをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="83a04-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="83a04-401">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="83a04-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="83a04-402">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="83a04-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="83a04-403">一時的な障害処理用の人気のサードパーティ製ライブラリ、*Polly* と統合できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="83a04-404">基になっている `HttpClientHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="83a04-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="83a04-405">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="83a04-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="83a04-406">詳細については、「<xref:fundamentals/http-requests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="83a04-407">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="83a04-407">Content root</span></span>

<span data-ttu-id="83a04-408">コンテンツ ルートは、以下に対する基本パスです。</span><span class="sxs-lookup"><span data-stu-id="83a04-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="83a04-409">アプリをホストしている実行可能ファイル ( *.exe*)。</span><span class="sxs-lookup"><span data-stu-id="83a04-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="83a04-410">アプリを構成するコンパイル済みアセンブリ ( *.dll*)。</span><span class="sxs-lookup"><span data-stu-id="83a04-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="83a04-411">次のような、アプリで使用される非コード コンテンツ ファイル。</span><span class="sxs-lookup"><span data-stu-id="83a04-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="83a04-412">Razor ファイル ( *.cshtml*、 *.razor*)</span><span class="sxs-lookup"><span data-stu-id="83a04-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="83a04-413">構成ファイル ( *.json*、 *.xml*)</span><span class="sxs-lookup"><span data-stu-id="83a04-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="83a04-414">データ ファイル ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="83a04-414">Data files (*.db*)</span></span>
* <span data-ttu-id="83a04-415">[Web ルート](#web-root) (通常、発行された *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="83a04-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="83a04-416">開発中:</span><span class="sxs-lookup"><span data-stu-id="83a04-416">During development:</span></span>

* <span data-ttu-id="83a04-417">コンテンツ ルートの規定値は、プロジェクトのルート ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="83a04-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="83a04-418">プロジェクトのルート ディレクトリは、次を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="83a04-419">プロジェクトのルート ディレクトリ内にある、アプリの非コード コンテンツ ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="83a04-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="83a04-420">[Web ルート](#web-root) (通常は、プロジェクトのルート ディレクトリ内の *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="83a04-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="83a04-421">[ホストの構築時](#host)には、別のコンテンツ ルート パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="83a04-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="83a04-422">詳細については、「<xref:fundamentals/host/web-host#content-root>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="83a04-423">Web ルート</span><span class="sxs-lookup"><span data-stu-id="83a04-423">Web root</span></span>

<span data-ttu-id="83a04-424">Web ルートは、次のような、パブリックで非コードの静的なリソース ファイルへの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="83a04-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="83a04-425">スタイルシート ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="83a04-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="83a04-426">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="83a04-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="83a04-427">画像 ( *.png*、*jpg*)</span><span class="sxs-lookup"><span data-stu-id="83a04-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="83a04-428">既定で、静的ファイルは Web ルート ディレクトリ (とサブディレクトリ) からのみ提供されます。</span><span class="sxs-lookup"><span data-stu-id="83a04-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="83a04-429">Web ルートのパスの既定値は、" *{コンテンツ ルート}/wwwroot*" ですが、[ホストの構築](#host)時に別の Web ルートを指定することも可能です。</span><span class="sxs-lookup"><span data-stu-id="83a04-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="83a04-430">詳細については、「[Web ルート](xref:fundamentals/host/web-host#web-root)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="83a04-431">プロジェクト ファイル内の [\<コンテンツ > プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items#content) を使用して *wwwroot* にファイルを発行できないようにします。</span><span class="sxs-lookup"><span data-stu-id="83a04-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="83a04-432">次の例では、*wwwroot/local* ディレクトリおよびサブディレクトリにコンテンツを公開しないようにします。</span><span class="sxs-lookup"><span data-stu-id="83a04-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="83a04-433">Razor ( *.cshtml*) ファイルの場合、チルダとスラッシュ (`~/`) が Web ルートを指します。</span><span class="sxs-lookup"><span data-stu-id="83a04-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="83a04-434">`~/` で始まるパスは、"*仮想パス*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="83a04-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="83a04-435">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83a04-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
