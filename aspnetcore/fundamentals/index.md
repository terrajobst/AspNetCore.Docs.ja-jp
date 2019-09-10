---
title: ASP.NET Core の基礎
author: rick-anderson
description: ASP.NET Core アプリの構築に関する基本概念について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: fundamentals/index
ms.openlocfilehash: cff2afd62ed60648dc689d408dde56ecda18c261
ms.sourcegitcommit: 2d4c1732c4866ed26b83da35f7bc2ad021a9c701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815655"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="cc2c2-103">ASP.NET Core の基礎</span><span class="sxs-lookup"><span data-stu-id="cc2c2-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="cc2c2-104">この記事では、ASP.NET Core アプリの開発方法を理解するための主要なトピックをまとめています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="cc2c2-105">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="cc2c2-105">The Startup class</span></span>

<span data-ttu-id="cc2c2-106">`Startup` クラスとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="cc2c2-107">アプリで必要なサービスが構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="cc2c2-108">要求を処理するパイプラインが定義されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="cc2c2-109">*サービス*とは、アプリが使用するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="cc2c2-110">たとえば、ログ コンポーネントは、サービスです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-110">For example, a logging component is a service.</span></span> <span data-ttu-id="cc2c2-111">サービスを構成 (または*登録*) するコードが `Startup.ConfigureServices` メソッドに追加されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="cc2c2-112">要求を処理するパイプラインは、一連の*ミドルウェア* コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="cc2c2-113">たとえば、ミドルウェアは、静的ファイルに対する要求を処理したり、HTTPS に HTTP 要求をリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="cc2c2-114">各ミドルウェアは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="cc2c2-115">`Startup.Configure` メソッドには、要求を処理するパイプラインを構成するコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="cc2c2-116">`Startup` クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="cc2c2-117">詳細については、<xref:fundamentals/startup> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="cc2c2-118">依存性の注入 (サービス)</span><span class="sxs-lookup"><span data-stu-id="cc2c2-118">Dependency injection (services)</span></span>

<span data-ttu-id="cc2c2-119">ASP.NET Core には、アプリのクラスが構成済みのサービスを利用できるようにする依存性の注入 (DI) フレームワークが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="cc2c2-120">クラスのサービスのインスタンスを取得する 1 つの方法は、必要な型のパラメーターを使用したコンストラクターを作成することです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="cc2c2-121">このパラメーターには、サービスの種類またはインターフェイスが可能です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="cc2c2-122">DI システムは、実行時にこのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="cc2c2-123">Entity Framework Core コンテキスト オブジェクトを取得するために DI を使用するクラスを次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="cc2c2-124">強調表示されている行は、コンストラクターの挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="cc2c2-125">DI が組み込まれており、必要に応じてサードパーティ製の制御の反転 (IoC) コンテナーを組み込むことができるよう設計されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="cc2c2-126">詳細については、<xref:fundamentals/dependency-injection> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="cc2c2-127">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="cc2c2-127">Middleware</span></span>

<span data-ttu-id="cc2c2-128">要求を処理するパイプラインは、一連のミドルウェア コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="cc2c2-129">各コンポーネントは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="cc2c2-130">通常、ミドルウェア コンポーネントは、`Startup.Configure` メソッドのその `Use...` 拡張メソッドを呼び出してパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="cc2c2-131">たとえば、静的ファイルのレンダリングを有効にするには、`UseStaticFiles` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="cc2c2-132">次の例の強調表示されているコードは、要求を処理するパイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="cc2c2-133">ASP.NET Core にはミドルウェアのセットが豊富に組み込まれており、カスタム ミドルウェアをユーザーが記述できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="cc2c2-134">詳細については、<xref:fundamentals/middleware/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="cc2c2-135">Host</span><span class="sxs-lookup"><span data-stu-id="cc2c2-135">Host</span></span>

<span data-ttu-id="cc2c2-136">ASP.NET Core アプリは起動時に*ホスト*をビルドします。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="cc2c2-137">ホストとは、次などのアプリのすべてのリソースをカプセル化するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="cc2c2-138">HTTP サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="cc2c2-138">An HTTP server implementation</span></span>
* <span data-ttu-id="cc2c2-139">ミドルウェア コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cc2c2-139">Middleware components</span></span>
* <span data-ttu-id="cc2c2-140">ログの記録</span><span class="sxs-lookup"><span data-stu-id="cc2c2-140">Logging</span></span>
* <span data-ttu-id="cc2c2-141">DI</span><span class="sxs-lookup"><span data-stu-id="cc2c2-141">DI</span></span>
* <span data-ttu-id="cc2c2-142">構成</span><span class="sxs-lookup"><span data-stu-id="cc2c2-142">Configuration</span></span>

<span data-ttu-id="cc2c2-143">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc2c2-144">汎用ホストと Web ホストの 2 つのホストが利用可能です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="cc2c2-145">汎用ホストが推奨されます。Web ホストは下位互換性のためにのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="cc2c2-146">ホストを作成するコードは `Program.Main` にあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="cc2c2-147">`CreateDefaultBuilder` メソッドと `ConfigureWebHostDefaults` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="cc2c2-148">Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="cc2c2-149">*appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="cc2c2-150">ログ出力をコンソールとデバッグ プロバイダーに送ります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="cc2c2-151">詳細については、<xref:fundamentals/host/generic-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc2c2-152">Web ホストと汎用ホストの 2 つのホストが利用可能です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="cc2c2-153">ASP.NET core 2.x では、汎用ホストは Web 以外のシナリオのみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="cc2c2-154">ホストを作成するコードは `Program.Main` にあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="cc2c2-155">`CreateDefaultBuilder` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="cc2c2-156">Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="cc2c2-157">*appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="cc2c2-158">ログ出力をコンソールとデバッグ プロバイダーに送ります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="cc2c2-159">詳細については、<xref:fundamentals/host/web-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="cc2c2-160">Web 以外のシナリオ</span><span class="sxs-lookup"><span data-stu-id="cc2c2-160">Non-web scenarios</span></span>

<span data-ttu-id="cc2c2-161">汎用ホストにより、他の種類のアプリで、ログ記録、依存性の注入 (DI)、構成、およびアプリの有効期間管理などの横断的なフレームワーク拡張機能を使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="cc2c2-162">詳細については、次のトピックを参照してください。 <xref:fundamentals/host/generic-host> および <xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="cc2c2-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="cc2c2-163">サーバー</span><span class="sxs-lookup"><span data-stu-id="cc2c2-163">Servers</span></span>

<span data-ttu-id="cc2c2-164">ASP.NET Core アプリは、HTTP 要求をリッスンするために HTTP サーバー実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="cc2c2-165">サーバーは、`HttpContext` に構成した[要求機能](xref:fundamentals/request-features)のセットとして、アプリへの要求を公開します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="cc2c2-166">Windows</span><span class="sxs-lookup"><span data-stu-id="cc2c2-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="cc2c2-167">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="cc2c2-168">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="cc2c2-169">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="cc2c2-170">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="cc2c2-171">*IIS HTTP サーバー*は、IIS を使用する Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="cc2c2-172">このサーバーでは、ASP.NET Core アプリと IIS が同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="cc2c2-173">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="cc2c2-174">macOS</span><span class="sxs-lookup"><span data-stu-id="cc2c2-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="cc2c2-175">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cc2c2-176">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cc2c2-177">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="cc2c2-178">Linux</span><span class="sxs-lookup"><span data-stu-id="cc2c2-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="cc2c2-179">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cc2c2-180">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cc2c2-181">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="cc2c2-182">Windows</span><span class="sxs-lookup"><span data-stu-id="cc2c2-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="cc2c2-183">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="cc2c2-184">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="cc2c2-185">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="cc2c2-186">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="cc2c2-187">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="cc2c2-188">macOS</span><span class="sxs-lookup"><span data-stu-id="cc2c2-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="cc2c2-189">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cc2c2-190">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cc2c2-191">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="cc2c2-192">Linux</span><span class="sxs-lookup"><span data-stu-id="cc2c2-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="cc2c2-193">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cc2c2-194">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cc2c2-195">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="cc2c2-196">詳細については、<xref:fundamentals/servers/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="cc2c2-197">構成</span><span class="sxs-lookup"><span data-stu-id="cc2c2-197">Configuration</span></span>

<span data-ttu-id="cc2c2-198">ASP.NET Core は、構成プロバイダーの順序付けされたセットから、名前と値のペアの設定を取得する構成フレームワークとなります。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="cc2c2-199">*.json* ファイル、 *.xml* ファイル、環境変数、コマンドライン引数など、さまざまなソース用に構成プロバイダーが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="cc2c2-200">独自のカスタム構成プロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="cc2c2-201">たとえば、構成は *appsettings.json* と環境変数から取得したものであると指定できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="cc2c2-202">このとき *ConnectionString* 値が要求されると、フレームワークはまず *appsettings.json* ファイルを参照します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="cc2c2-203">値がそこにあり、しかし環境変数にもある場合、環境変数の値が優先されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="cc2c2-204">ASP.NET Core には、パスワードなどの機密の構成データの管理に[シークレット マネージャー ツール](xref:security/app-secrets)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cc2c2-205">実稼働の機密情報には、[Azure Key Vault](xref:security/key-vault-configuration) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="cc2c2-206">詳細については、<xref:fundamentals/configuration/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="cc2c2-207">オプション</span><span class="sxs-lookup"><span data-stu-id="cc2c2-207">Options</span></span>

<span data-ttu-id="cc2c2-208">ASP.NET Core では、構成値の格納と取得に、可能な限り*オプション パターン*を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="cc2c2-209">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="cc2c2-210">たとえば、以下のコードでは WebSockets のオプションが設定されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="cc2c2-211">詳細については、<xref:fundamentals/configuration/options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="cc2c2-212">環境</span><span class="sxs-lookup"><span data-stu-id="cc2c2-212">Environments</span></span>

<span data-ttu-id="cc2c2-213">*開発*、*ステージング*、および*実稼働*などの実行環境は ASP.NET Core の最上の概念です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="cc2c2-214">アプリが実行している環境は、`ASPNETCORE_ENVIRONMENT` 環境変数を設定することにより指定できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="cc2c2-215">ASP.NET Core は、アプリの起動時にその環境変数を読み取り、その値を `IHostingEnvironment` 実装に格納します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="cc2c2-216">この環境オブジェクトは、DI を介しアプリの任意の場所で使用されます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="cc2c2-217">`Startup` クラスの次のサンプル コードは、それが開発環境で実行された場合のみ、詳細なエラー情報を提供するようアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="cc2c2-218">詳細については、<xref:fundamentals/environments> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="cc2c2-219">ログの記録</span><span class="sxs-lookup"><span data-stu-id="cc2c2-219">Logging</span></span>

<span data-ttu-id="cc2c2-220">ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="cc2c2-221">利用可能なプロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-221">Available providers include the following:</span></span>

* <span data-ttu-id="cc2c2-222">コンソール</span><span class="sxs-lookup"><span data-stu-id="cc2c2-222">Console</span></span>
* <span data-ttu-id="cc2c2-223">デバッグ</span><span class="sxs-lookup"><span data-stu-id="cc2c2-223">Debug</span></span>
* <span data-ttu-id="cc2c2-224">Event Tracing on Windows</span><span class="sxs-lookup"><span data-stu-id="cc2c2-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="cc2c2-225">Windows イベント ログ</span><span class="sxs-lookup"><span data-stu-id="cc2c2-225">Windows Event Log</span></span>
* <span data-ttu-id="cc2c2-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="cc2c2-226">TraceSource</span></span>
* <span data-ttu-id="cc2c2-227">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cc2c2-227">Azure App Service</span></span>
* <span data-ttu-id="cc2c2-228">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="cc2c2-228">Azure Application Insights</span></span>

<span data-ttu-id="cc2c2-229">DI からの `ILogger` オブジェクトの取得およびログ メソッドの呼び出しによってアプリのコードの任意の場所からログを記述します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="cc2c2-230">コンストラクターの挿入とログ記録メソッドの呼び出しが強調表示されている `ILogger` オブジェクトを使用するサンプル コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="cc2c2-231">`ILogger` インターフェイスは、ログ プロバイダーに任意の数のフィールドを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="cc2c2-232">このフィールドは、一般的にメッセージの文字列を構築するために使用しますが、プロバイダーがデータ ストアに別のフィールドとして送信することも可能です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="cc2c2-233">この機能は、[構造化ロギングとも呼ばれるセマンティック ロギング](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をログ プロバイダーが実装するのを可能にします。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="cc2c2-234">詳細については、<xref:fundamentals/logging/index> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="cc2c2-235">ルーティング</span><span class="sxs-lookup"><span data-stu-id="cc2c2-235">Routing</span></span>

<span data-ttu-id="cc2c2-236">*ルート*とは、ハンドラーにマップされている URL のパターンです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="cc2c2-237">このハンドラーは一般的には Razor Pages、MVC コントローラーのアクション メソッドまたはミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="cc2c2-238">ASP.NET Core のルーティングでは、アプリで使用する URL を制御できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="cc2c2-239">詳細については、<xref:fundamentals/routing> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="cc2c2-240">エラー処理</span><span class="sxs-lookup"><span data-stu-id="cc2c2-240">Error handling</span></span>

<span data-ttu-id="cc2c2-241">ASP.NET Core には、次などのエラー処理用の機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="cc2c2-242">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="cc2c2-242">A developer exception page</span></span>
* <span data-ttu-id="cc2c2-243">カスタム エラー ページ</span><span class="sxs-lookup"><span data-stu-id="cc2c2-243">Custom error pages</span></span>
* <span data-ttu-id="cc2c2-244">静的状態コード ページ</span><span class="sxs-lookup"><span data-stu-id="cc2c2-244">Static status code pages</span></span>
* <span data-ttu-id="cc2c2-245">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="cc2c2-245">Startup exception handling</span></span>

<span data-ttu-id="cc2c2-246">詳細については、<xref:fundamentals/error-handling> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="cc2c2-247">HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="cc2c2-247">Make HTTP requests</span></span>

<span data-ttu-id="cc2c2-248">`HttpClient` インスタンスの作成に、`IHttpClientFactory` の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="cc2c2-249">ファクトリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-249">The factory:</span></span>

* <span data-ttu-id="cc2c2-250">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="cc2c2-251">たとえば、*github* クライアントを登録して、GitHub にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="cc2c2-252">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="cc2c2-253">複数のデリゲート ハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築するのをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="cc2c2-254">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="cc2c2-255">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="cc2c2-256">一時的な障害処理用の人気のサードパーティ製ライブラリ、*Polly* と統合できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="cc2c2-257">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="cc2c2-258">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="cc2c2-259">詳細については、<xref:fundamentals/http-requests> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="cc2c2-260">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="cc2c2-260">Content root</span></span>

<span data-ttu-id="cc2c2-261">このコンテンツ ルートは、その Razor ファイルなど、アプリで使用されるすべてのプライベート コンテンツへの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-261">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="cc2c2-262">既定では、コンテンツ ルートは、アプリをホストする実行可能ファイルの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-262">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="cc2c2-263">[ホストの構築時](#host)には、別の場所を指定できます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-263">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc2c2-264">詳細については、[コンテンツ ルート](xref:fundamentals/host/generic-host#content-root)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-264">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc2c2-265">詳細については、[コンテンツ ルート](xref:fundamentals/host/web-host#content-root)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-265">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="cc2c2-266">Web ルート</span><span class="sxs-lookup"><span data-stu-id="cc2c2-266">Web root</span></span>

<span data-ttu-id="cc2c2-267">(*webroot* としても知られている) Web ルートは、CSS、JavaScript、およびイメージ ファイルなど、パブリックな静的リソースへの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-267">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="cc2c2-268">既定で、この静的ファイル ミドルウェアは、Web ルート ディレクトリ (とそのサブディレクトリ) のファイルにのみサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-268">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="cc2c2-269">Web ルートのパスの既定値は、" *{コンテンツ ルート}/wwwroot*" ですが、[ホストの構築](#host)時に別の場所を指定することも可能です。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-269">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc2c2-270">詳細については、「[WebRoot](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#webroot)」を参照してください</span><span class="sxs-lookup"><span data-stu-id="cc2c2-270">For more information, see [WebRoot](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#webroot)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc2c2-271">詳細については、「[Web ルート](/aspnet/core/fundamentals/host/web-host#webroot)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-271">For more information, see [Web root](/aspnet/core/fundamentals/host/web-host#webroot).</span></span>

::: moniker-end

<span data-ttu-id="cc2c2-272">Razor ( *.cshtml*) のファイルの場合、チルダとスラッシュ `~/` が webroot を指します。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-272">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="cc2c2-273">`~/` で始まるパスは仮想パスと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-273">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="cc2c2-274">詳細については、<xref:fundamentals/static-files> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc2c2-274">For more information, see <xref:fundamentals/static-files>.</span></span>
