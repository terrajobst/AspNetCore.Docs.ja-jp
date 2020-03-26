---
title: ASP.NET Core の基礎
author: rick-anderson
description: ASP.NET Core アプリの構築に関する基本概念について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: 7533242140c31a937f32cc9082d760103347ce25
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219182"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="9dbbc-103">ASP.NET Core の基礎</span><span class="sxs-lookup"><span data-stu-id="9dbbc-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9dbbc-104">この記事では、ASP.NET Core アプリの開発方法を理解するための主要なトピックをまとめています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="9dbbc-105">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="9dbbc-105">The Startup class</span></span>

<span data-ttu-id="9dbbc-106">`Startup` クラスとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="9dbbc-107">アプリで必要なサービスが構成されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="9dbbc-108">要求を処理するパイプラインが定義されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="9dbbc-109">*サービス*とは、アプリが使用するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="9dbbc-110">たとえば、ログ コンポーネントは、サービスです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-110">For example, a logging component is a service.</span></span> <span data-ttu-id="9dbbc-111">サービスを構成 (または*登録*) するコードが `Startup.ConfigureServices` メソッドに追加されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="9dbbc-112">要求を処理するパイプラインは、一連の*ミドルウェア* コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="9dbbc-113">たとえば、ミドルウェアは、静的ファイルに対する要求を処理したり、HTTPS に HTTP 要求をリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="9dbbc-114">各ミドルウェアは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="9dbbc-115">`Startup.Configure` メソッドには、要求を処理するパイプラインを構成するコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="9dbbc-116">`Startup` クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="9dbbc-117">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="9dbbc-118">依存性の注入 (サービス)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-118">Dependency injection (services)</span></span>

<span data-ttu-id="9dbbc-119">ASP.NET Core には、アプリのクラスが構成済みのサービスを利用できるようにする依存性の注入 (DI) フレームワークが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="9dbbc-120">クラスのサービスのインスタンスを取得する 1 つの方法は、必要な型のパラメーターを使用したコンストラクターを作成することです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="9dbbc-121">このパラメーターには、サービスの種類またはインターフェイスが可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="9dbbc-122">DI システムは、実行時にこのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="9dbbc-123">Entity Framework Core コンテキスト オブジェクトを取得するために DI を使用するクラスを次に示します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="9dbbc-124">強調表示されている行は、コンストラクターの挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="9dbbc-125">DI が組み込まれており、必要に応じてサードパーティ製の制御の反転 (IoC) コンテナーを組み込むことができるよう設計されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="9dbbc-126">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="9dbbc-127">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9dbbc-127">Middleware</span></span>

<span data-ttu-id="9dbbc-128">要求を処理するパイプラインは、一連のミドルウェア コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="9dbbc-129">各コンポーネントは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="9dbbc-130">通常、ミドルウェア コンポーネントは、`Startup.Configure` メソッドのその `Use...` 拡張メソッドを呼び出してパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="9dbbc-131">たとえば、静的ファイルのレンダリングを有効にするには、`UseStaticFiles` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="9dbbc-132">次の例の強調表示されているコードは、要求を処理するパイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="9dbbc-133">ASP.NET Core にはミドルウェアのセットが豊富に組み込まれており、カスタム ミドルウェアをユーザーが記述できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="9dbbc-134">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="9dbbc-135">Host</span><span class="sxs-lookup"><span data-stu-id="9dbbc-135">Host</span></span>

<span data-ttu-id="9dbbc-136">ASP.NET Core アプリは起動時に*ホスト*をビルドします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="9dbbc-137">ホストとは、次などのアプリのすべてのリソースをカプセル化するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="9dbbc-138">HTTP サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="9dbbc-138">An HTTP server implementation</span></span>
* <span data-ttu-id="9dbbc-139">ミドルウェア コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9dbbc-139">Middleware components</span></span>
* <span data-ttu-id="9dbbc-140">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9dbbc-140">Logging</span></span>
* <span data-ttu-id="9dbbc-141">DI</span><span class="sxs-lookup"><span data-stu-id="9dbbc-141">DI</span></span>
* <span data-ttu-id="9dbbc-142">構成</span><span class="sxs-lookup"><span data-stu-id="9dbbc-142">Configuration</span></span>

<span data-ttu-id="9dbbc-143">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9dbbc-144">汎用ホストと Web ホストの 2 つのホストが利用可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="9dbbc-145">汎用ホストが推奨されます。Web ホストは下位互換性のためにのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="9dbbc-146">ホストを作成するコードは `Program.Main` にあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="9dbbc-147">`CreateDefaultBuilder` メソッドと `ConfigureWebHostDefaults` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="9dbbc-148">Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="9dbbc-149">*appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="9dbbc-150">ログ出力をコンソールとデバッグ プロバイダーに送ります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="9dbbc-151">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="9dbbc-152">Web 以外のシナリオ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-152">Non-web scenarios</span></span>

<span data-ttu-id="9dbbc-153">汎用ホストにより、他の種類のアプリで、ログ記録、依存性の注入 (DI)、構成、およびアプリの有効期間管理などの横断的なフレームワーク拡張機能を使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-153">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="9dbbc-154">詳細については、次のトピックを参照してください。 <xref:fundamentals/host/generic-host> および <xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="9dbbc-154">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="9dbbc-155">サーバー</span><span class="sxs-lookup"><span data-stu-id="9dbbc-155">Servers</span></span>

<span data-ttu-id="9dbbc-156">ASP.NET Core アプリは、HTTP 要求をリッスンするために HTTP サーバー実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-156">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="9dbbc-157">サーバーは、`HttpContext` に構成した[要求機能](xref:fundamentals/request-features)のセットとして、アプリへの要求を公開します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-157">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="9dbbc-158">Windows</span><span class="sxs-lookup"><span data-stu-id="9dbbc-158">Windows</span></span>](#tab/windows)

<span data-ttu-id="9dbbc-159">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-159">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="9dbbc-160">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-160">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="9dbbc-161">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-161">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="9dbbc-162">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-162">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="9dbbc-163">*IIS HTTP サーバー*は、IIS を使用する Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-163">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="9dbbc-164">このサーバーでは、ASP.NET Core アプリと IIS が同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-164">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="9dbbc-165">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-165">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="9dbbc-166">macOS</span><span class="sxs-lookup"><span data-stu-id="9dbbc-166">macOS</span></span>](#tab/macos)

<span data-ttu-id="9dbbc-167">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-167">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="9dbbc-168">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-168">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="9dbbc-169">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-169">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="9dbbc-170">Linux</span><span class="sxs-lookup"><span data-stu-id="9dbbc-170">Linux</span></span>](#tab/linux)

<span data-ttu-id="9dbbc-171">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-171">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="9dbbc-172">Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-172">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="9dbbc-173">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-173">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="9dbbc-174">詳細については、「<xref:fundamentals/servers/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-174">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="9dbbc-175">構成</span><span class="sxs-lookup"><span data-stu-id="9dbbc-175">Configuration</span></span>

<span data-ttu-id="9dbbc-176">ASP.NET Core は、構成プロバイダーの順序付けされたセットから、名前と値のペアの設定を取得する構成フレームワークとなります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-176">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="9dbbc-177">*.json* ファイル、*.xml* ファイル、環境変数、コマンドライン引数など、さまざまなソース用に構成プロバイダーが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-177">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="9dbbc-178">独自のカスタム構成プロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-178">You can also write custom configuration providers.</span></span>

<span data-ttu-id="9dbbc-179">たとえば、構成は *appsettings.json* と環境変数から取得したものであると指定できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-179">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="9dbbc-180">このとき *ConnectionString* 値が要求されると、フレームワークはまず *appsettings.json* ファイルを参照します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-180">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="9dbbc-181">値がそこにあり、しかし環境変数にもある場合、環境変数の値が優先されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-181">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="9dbbc-182">ASP.NET Core には、パスワードなどの機密の構成データの管理に[シークレット マネージャー ツール](xref:security/app-secrets)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-182">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9dbbc-183">実稼働の機密情報には、[Azure Key Vault](xref:security/key-vault-configuration) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-183">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="9dbbc-184">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="9dbbc-185">オプション</span><span class="sxs-lookup"><span data-stu-id="9dbbc-185">Options</span></span>

<span data-ttu-id="9dbbc-186">ASP.NET Core では、構成値の格納と取得に、可能な限り*オプション パターン*を使用します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-186">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="9dbbc-187">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-187">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="9dbbc-188">たとえば、以下のコードでは WebSockets のオプションが設定されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-188">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="9dbbc-189">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-189">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="9dbbc-190">環境</span><span class="sxs-lookup"><span data-stu-id="9dbbc-190">Environments</span></span>

<span data-ttu-id="9dbbc-191">*開発*、*ステージング*、および*実稼働*などの実行環境は ASP.NET Core の最上の概念です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-191">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="9dbbc-192">アプリが実行している環境は、`ASPNETCORE_ENVIRONMENT` 環境変数を設定することにより指定できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-192">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="9dbbc-193">ASP.NET Core は、アプリの起動時にその環境変数を読み取り、その値を `IHostingEnvironment` 実装に格納します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-193">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="9dbbc-194">この環境オブジェクトは、DI を介しアプリの任意の場所で使用されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-194">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="9dbbc-195">`Startup` クラスの次のサンプル コードは、それが開発環境で実行された場合のみ、詳細なエラー情報を提供するようアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-195">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="9dbbc-196">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-196">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="9dbbc-197">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9dbbc-197">Logging</span></span>

<span data-ttu-id="9dbbc-198">ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-198">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="9dbbc-199">利用可能なプロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-199">Available providers include the following:</span></span>

* <span data-ttu-id="9dbbc-200">コンソール</span><span class="sxs-lookup"><span data-stu-id="9dbbc-200">Console</span></span>
* <span data-ttu-id="9dbbc-201">デバッグ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-201">Debug</span></span>
* <span data-ttu-id="9dbbc-202">Event Tracing on Windows</span><span class="sxs-lookup"><span data-stu-id="9dbbc-202">Event Tracing on Windows</span></span>
* <span data-ttu-id="9dbbc-203">Windows イベント ログ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-203">Windows Event Log</span></span>
* <span data-ttu-id="9dbbc-204">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9dbbc-204">TraceSource</span></span>
* <span data-ttu-id="9dbbc-205">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9dbbc-205">Azure App Service</span></span>
* <span data-ttu-id="9dbbc-206">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="9dbbc-206">Azure Application Insights</span></span>

<span data-ttu-id="9dbbc-207">DI からの `ILogger` オブジェクトの取得およびログ メソッドの呼び出しによってアプリのコードの任意の場所からログを記述します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-207">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="9dbbc-208">コンストラクターの挿入とログ記録メソッドの呼び出しが強調表示されている `ILogger` オブジェクトを使用するサンプル コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-208">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="9dbbc-209">`ILogger` インターフェイスは、ログ プロバイダーに任意の数のフィールドを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-209">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="9dbbc-210">このフィールドは、一般的にメッセージの文字列を構築するために使用しますが、プロバイダーがデータ ストアに別のフィールドとして送信することも可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-210">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="9dbbc-211">この機能は、[構造化ロギングとも呼ばれるセマンティック ロギング](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をログ プロバイダーが実装するのを可能にします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-211">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9dbbc-212">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="9dbbc-213">ルーティング</span><span class="sxs-lookup"><span data-stu-id="9dbbc-213">Routing</span></span>

<span data-ttu-id="9dbbc-214">*ルート*とは、ハンドラーにマップされている URL のパターンです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-214">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="9dbbc-215">このハンドラーは一般的には Razor Pages、MVC コントローラーのアクション メソッドまたはミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-215">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="9dbbc-216">ASP.NET Core のルーティングでは、アプリで使用する URL を制御できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-216">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="9dbbc-217">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="9dbbc-218">エラー処理</span><span class="sxs-lookup"><span data-stu-id="9dbbc-218">Error handling</span></span>

<span data-ttu-id="9dbbc-219">ASP.NET Core には、次などのエラー処理用の機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-219">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="9dbbc-220">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-220">A developer exception page</span></span>
* <span data-ttu-id="9dbbc-221">カスタム エラー ページ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-221">Custom error pages</span></span>
* <span data-ttu-id="9dbbc-222">静的状態コード ページ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-222">Static status code pages</span></span>
* <span data-ttu-id="9dbbc-223">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="9dbbc-223">Startup exception handling</span></span>

<span data-ttu-id="9dbbc-224">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-224">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="9dbbc-225">HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="9dbbc-225">Make HTTP requests</span></span>

<span data-ttu-id="9dbbc-226">`HttpClient` インスタンスの作成に、`IHttpClientFactory` の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-226">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="9dbbc-227">ファクトリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-227">The factory:</span></span>

* <span data-ttu-id="9dbbc-228">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-228">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="9dbbc-229">たとえば、*github* クライアントを登録して、GitHub にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-229">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="9dbbc-230">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-230">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="9dbbc-231">複数のデリゲート ハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築するのをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-231">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="9dbbc-232">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-232">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="9dbbc-233">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-233">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="9dbbc-234">一時的な障害処理用の人気のサードパーティ製ライブラリ、*Polly* と統合できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-234">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="9dbbc-235">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-235">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="9dbbc-236">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-236">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="9dbbc-237">詳細については、「<xref:fundamentals/http-requests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-237">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="9dbbc-238">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="9dbbc-238">Content root</span></span>

<span data-ttu-id="9dbbc-239">コンテンツ ルートは、以下に対する基本パスです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-239">The content root is the base path to the:</span></span>

* <span data-ttu-id="9dbbc-240">アプリをホストしている実行可能ファイル (*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-240">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="9dbbc-241">アプリを構成するコンパイル済みアセンブリ (*.dll*)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-241">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="9dbbc-242">次のような、アプリで使用される非コード コンテンツ ファイル。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-242">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="9dbbc-243">Razor ファイル (*.cshtml*、*.razor*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-243">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="9dbbc-244">構成ファイル (*.json*、*.xml*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-244">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="9dbbc-245">データ ファイル (*.db*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-245">Data files (*.db*)</span></span>
* <span data-ttu-id="9dbbc-246">[Web ルート](#web-root) (通常、発行された *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-246">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="9dbbc-247">開発中:</span><span class="sxs-lookup"><span data-stu-id="9dbbc-247">During development:</span></span>

* <span data-ttu-id="9dbbc-248">コンテンツ ルートの規定値は、プロジェクトのルート ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-248">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="9dbbc-249">プロジェクトのルート ディレクトリは、次を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-249">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="9dbbc-250">プロジェクトのルート ディレクトリ内にある、アプリの非コード コンテンツ ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-250">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="9dbbc-251">[Web ルート](#web-root) (通常は、プロジェクトのルート ディレクトリ内の *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-251">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="9dbbc-252">[ホストの構築時](#host)には、別のコンテンツ ルート パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-252">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="9dbbc-253">詳細については、「<xref:fundamentals/host/generic-host#contentrootpath>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-253">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

## <a name="web-root"></a><span data-ttu-id="9dbbc-254">Web ルート</span><span class="sxs-lookup"><span data-stu-id="9dbbc-254">Web root</span></span>

<span data-ttu-id="9dbbc-255">Web ルートは、次のような、パブリックで非コードの静的なリソース ファイルへの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-255">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="9dbbc-256">スタイルシート (*.css*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-256">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="9dbbc-257">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-257">JavaScript (*.js*)</span></span>
* <span data-ttu-id="9dbbc-258">画像 (*.png*、*jpg*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-258">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="9dbbc-259">既定で、静的ファイルは Web ルート ディレクトリ (とサブディレクトリ) からのみ提供されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-259">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="9dbbc-260">Web ルートのパスの既定値は、"*{コンテンツ ルート}/wwwroot*" ですが、[ホストの構築](#host)時に別の Web ルートを指定することも可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-260">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="9dbbc-261">詳細については、「<xref:fundamentals/host/generic-host#webroot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-261">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

<span data-ttu-id="9dbbc-262">プロジェクト ファイル内の [\<コンテンツ > プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items#content) を使用して *wwwroot* にファイルを発行できないようにします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-262">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="9dbbc-263">次の例では、*wwwroot/local* ディレクトリおよびサブディレクトリにコンテンツを公開しないようにします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-263">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="9dbbc-264">静的な ID 資産が Web ルートに公開されないようにするには、<xref:security/authentication/identity#prevent-publish-of-static-identity-assets> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-264">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

<span data-ttu-id="9dbbc-265">Razor (*.cshtml*) ファイルの場合、チルダとスラッシュ (`~/`) が Web ルートを指します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-265">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="9dbbc-266">`~/` で始まるパスは、"*仮想パス*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-266">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="9dbbc-267">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-267">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9dbbc-268">この記事では、ASP.NET Core アプリの開発方法を理解するための主要なトピックをまとめています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-268">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="9dbbc-269">Startup クラス</span><span class="sxs-lookup"><span data-stu-id="9dbbc-269">The Startup class</span></span>

<span data-ttu-id="9dbbc-270">`Startup` クラスとは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-270">The `Startup` class is where:</span></span>

* <span data-ttu-id="9dbbc-271">アプリで必要なサービスが構成されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-271">Services required by the app are configured.</span></span>
* <span data-ttu-id="9dbbc-272">要求を処理するパイプラインが定義されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-272">The request handling pipeline is defined.</span></span>

<span data-ttu-id="9dbbc-273">*サービス*とは、アプリが使用するコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-273">*Services* are components that are used by the app.</span></span> <span data-ttu-id="9dbbc-274">たとえば、ログ コンポーネントは、サービスです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-274">For example, a logging component is a service.</span></span> <span data-ttu-id="9dbbc-275">サービスを構成 (または*登録*) するコードが `Startup.ConfigureServices` メソッドに追加されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-275">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="9dbbc-276">要求を処理するパイプラインは、一連の*ミドルウェア* コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-276">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="9dbbc-277">たとえば、ミドルウェアは、静的ファイルに対する要求を処理したり、HTTPS に HTTP 要求をリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-277">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="9dbbc-278">各ミドルウェアは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-278">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="9dbbc-279">`Startup.Configure` メソッドには、要求を処理するパイプラインを構成するコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-279">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="9dbbc-280">`Startup` クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-280">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="9dbbc-281">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="9dbbc-282">依存性の注入 (サービス)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-282">Dependency injection (services)</span></span>

<span data-ttu-id="9dbbc-283">ASP.NET Core には、アプリのクラスが構成済みのサービスを利用できるようにする依存性の注入 (DI) フレームワークが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-283">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="9dbbc-284">クラスのサービスのインスタンスを取得する 1 つの方法は、必要な型のパラメーターを使用したコンストラクターを作成することです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-284">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="9dbbc-285">このパラメーターには、サービスの種類またはインターフェイスが可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-285">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="9dbbc-286">DI システムは、実行時にこのサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-286">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="9dbbc-287">Entity Framework Core コンテキスト オブジェクトを取得するために DI を使用するクラスを次に示します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-287">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="9dbbc-288">強調表示されている行は、コンストラクターの挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-288">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="9dbbc-289">DI が組み込まれており、必要に応じてサードパーティ製の制御の反転 (IoC) コンテナーを組み込むことができるよう設計されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-289">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="9dbbc-290">詳細については、「<xref:fundamentals/dependency-injection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-290">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="9dbbc-291">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="9dbbc-291">Middleware</span></span>

<span data-ttu-id="9dbbc-292">要求を処理するパイプラインは、一連のミドルウェア コンポーネントとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-292">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="9dbbc-293">各コンポーネントは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-293">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="9dbbc-294">通常、ミドルウェア コンポーネントは、`Startup.Configure` メソッドのその `Use...` 拡張メソッドを呼び出してパイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-294">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="9dbbc-295">たとえば、静的ファイルのレンダリングを有効にするには、`UseStaticFiles` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-295">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="9dbbc-296">次の例の強調表示されているコードは、要求を処理するパイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-296">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="9dbbc-297">ASP.NET Core にはミドルウェアのセットが豊富に組み込まれており、カスタム ミドルウェアをユーザーが記述できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-297">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="9dbbc-298">詳細については、「<xref:fundamentals/middleware/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-298">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="9dbbc-299">Host</span><span class="sxs-lookup"><span data-stu-id="9dbbc-299">Host</span></span>

<span data-ttu-id="9dbbc-300">ASP.NET Core アプリは起動時に*ホスト*をビルドします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-300">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="9dbbc-301">ホストとは、次などのアプリのすべてのリソースをカプセル化するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-301">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="9dbbc-302">HTTP サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="9dbbc-302">An HTTP server implementation</span></span>
* <span data-ttu-id="9dbbc-303">ミドルウェア コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9dbbc-303">Middleware components</span></span>
* <span data-ttu-id="9dbbc-304">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9dbbc-304">Logging</span></span>
* <span data-ttu-id="9dbbc-305">DI</span><span class="sxs-lookup"><span data-stu-id="9dbbc-305">DI</span></span>
* <span data-ttu-id="9dbbc-306">構成</span><span class="sxs-lookup"><span data-stu-id="9dbbc-306">Configuration</span></span>

<span data-ttu-id="9dbbc-307">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-307">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="9dbbc-308">Web ホストと汎用ホストの 2 つのホストが利用可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-308">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="9dbbc-309">ASP.NET core 2.x では、汎用ホストは Web 以外のシナリオのみを対象としています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-309">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="9dbbc-310">ホストを作成するコードは `Program.Main` にあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-310">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="9dbbc-311">`CreateDefaultBuilder` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-311">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="9dbbc-312">Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-312">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="9dbbc-313">*appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-313">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="9dbbc-314">ログ出力をコンソールとデバッグ プロバイダーに送ります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-314">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="9dbbc-315">詳細については、「<xref:fundamentals/host/web-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-315">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="9dbbc-316">Web 以外のシナリオ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-316">Non-web scenarios</span></span>

<span data-ttu-id="9dbbc-317">汎用ホストにより、他の種類のアプリで、ログ記録、依存性の注入 (DI)、構成、およびアプリの有効期間管理などの横断的なフレームワーク拡張機能を使えるようになります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-317">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="9dbbc-318">詳細については、次のトピックを参照してください。 <xref:fundamentals/host/generic-host> および <xref:fundamentals/host/hosted-services></span><span class="sxs-lookup"><span data-stu-id="9dbbc-318">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="9dbbc-319">サーバー</span><span class="sxs-lookup"><span data-stu-id="9dbbc-319">Servers</span></span>

<span data-ttu-id="9dbbc-320">ASP.NET Core アプリは、HTTP 要求をリッスンするために HTTP サーバー実装を使用します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-320">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="9dbbc-321">サーバーは、`HttpContext` に構成した[要求機能](xref:fundamentals/request-features)のセットとして、アプリへの要求を公開します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-321">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="9dbbc-322">Windows</span><span class="sxs-lookup"><span data-stu-id="9dbbc-322">Windows</span></span>](#tab/windows)

<span data-ttu-id="9dbbc-323">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-323">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="9dbbc-324">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-324">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="9dbbc-325">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-325">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="9dbbc-326">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-326">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="9dbbc-327">*IIS HTTP サーバー*は、IIS を使用する Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-327">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="9dbbc-328">このサーバーでは、ASP.NET Core アプリと IIS が同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-328">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="9dbbc-329">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-329">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="9dbbc-330">macOS</span><span class="sxs-lookup"><span data-stu-id="9dbbc-330">macOS</span></span>](#tab/macos)

<span data-ttu-id="9dbbc-331">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-331">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="9dbbc-332">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-332">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="9dbbc-333">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-333">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="9dbbc-334">Linux</span><span class="sxs-lookup"><span data-stu-id="9dbbc-334">Linux</span></span>](#tab/linux)

<span data-ttu-id="9dbbc-335">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-335">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="9dbbc-336">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-336">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="9dbbc-337">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-337">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="9dbbc-338">Windows</span><span class="sxs-lookup"><span data-stu-id="9dbbc-338">Windows</span></span>](#tab/windows)

<span data-ttu-id="9dbbc-339">ASP.NET Core では、次のサーバー実装が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-339">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="9dbbc-340">*Kestrel* は、クロスプラットフォームの Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-340">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="9dbbc-341">Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-341">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="9dbbc-342">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-342">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="9dbbc-343">*HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-343">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="9dbbc-344">macOS</span><span class="sxs-lookup"><span data-stu-id="9dbbc-344">macOS</span></span>](#tab/macos)

<span data-ttu-id="9dbbc-345">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-345">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="9dbbc-346">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-346">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="9dbbc-347">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-347">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="9dbbc-348">Linux</span><span class="sxs-lookup"><span data-stu-id="9dbbc-348">Linux</span></span>](#tab/linux)

<span data-ttu-id="9dbbc-349">ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-349">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="9dbbc-350">Kestrel は、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-350">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="9dbbc-351">Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-351">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9dbbc-352">詳細については、「<xref:fundamentals/servers/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-352">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="9dbbc-353">構成</span><span class="sxs-lookup"><span data-stu-id="9dbbc-353">Configuration</span></span>

<span data-ttu-id="9dbbc-354">ASP.NET Core は、構成プロバイダーの順序付けされたセットから、名前と値のペアの設定を取得する構成フレームワークとなります。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-354">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="9dbbc-355">*.json* ファイル、*.xml* ファイル、環境変数、コマンドライン引数など、さまざまなソース用に構成プロバイダーが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-355">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="9dbbc-356">独自のカスタム構成プロバイダーを記述することもできます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-356">You can also write custom configuration providers.</span></span>

<span data-ttu-id="9dbbc-357">たとえば、構成は *appsettings.json* と環境変数から取得したものであると指定できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-357">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="9dbbc-358">このとき *ConnectionString* 値が要求されると、フレームワークはまず *appsettings.json* ファイルを参照します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-358">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="9dbbc-359">値がそこにあり、しかし環境変数にもある場合、環境変数の値が優先されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-359">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="9dbbc-360">ASP.NET Core には、パスワードなどの機密の構成データの管理に[シークレット マネージャー ツール](xref:security/app-secrets)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-360">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9dbbc-361">実稼働の機密情報には、[Azure Key Vault](xref:security/key-vault-configuration) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-361">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="9dbbc-362">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-362">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="9dbbc-363">オプション</span><span class="sxs-lookup"><span data-stu-id="9dbbc-363">Options</span></span>

<span data-ttu-id="9dbbc-364">ASP.NET Core では、構成値の格納と取得に、可能な限り*オプション パターン*を使用します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-364">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="9dbbc-365">オプション パターンではクラスを使用して、関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-365">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="9dbbc-366">たとえば、以下のコードでは WebSockets のオプションが設定されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-366">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="9dbbc-367">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-367">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="9dbbc-368">環境</span><span class="sxs-lookup"><span data-stu-id="9dbbc-368">Environments</span></span>

<span data-ttu-id="9dbbc-369">*開発*、*ステージング*、および*実稼働*などの実行環境は ASP.NET Core の最上の概念です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-369">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="9dbbc-370">アプリが実行している環境は、`ASPNETCORE_ENVIRONMENT` 環境変数を設定することにより指定できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-370">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="9dbbc-371">ASP.NET Core は、アプリの起動時にその環境変数を読み取り、その値を `IHostingEnvironment` 実装に格納します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-371">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="9dbbc-372">この環境オブジェクトは、DI を介しアプリの任意の場所で使用されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-372">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="9dbbc-373">`Startup` クラスの次のサンプル コードは、それが開発環境で実行された場合のみ、詳細なエラー情報を提供するようアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-373">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="9dbbc-374">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-374">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="9dbbc-375">ログの記録</span><span class="sxs-lookup"><span data-stu-id="9dbbc-375">Logging</span></span>

<span data-ttu-id="9dbbc-376">ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-376">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="9dbbc-377">利用可能なプロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-377">Available providers include the following:</span></span>

* <span data-ttu-id="9dbbc-378">コンソール</span><span class="sxs-lookup"><span data-stu-id="9dbbc-378">Console</span></span>
* <span data-ttu-id="9dbbc-379">デバッグ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-379">Debug</span></span>
* <span data-ttu-id="9dbbc-380">Event Tracing on Windows</span><span class="sxs-lookup"><span data-stu-id="9dbbc-380">Event Tracing on Windows</span></span>
* <span data-ttu-id="9dbbc-381">Windows イベント ログ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-381">Windows Event Log</span></span>
* <span data-ttu-id="9dbbc-382">TraceSource</span><span class="sxs-lookup"><span data-stu-id="9dbbc-382">TraceSource</span></span>
* <span data-ttu-id="9dbbc-383">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9dbbc-383">Azure App Service</span></span>
* <span data-ttu-id="9dbbc-384">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="9dbbc-384">Azure Application Insights</span></span>

<span data-ttu-id="9dbbc-385">DI からの `ILogger` オブジェクトの取得およびログ メソッドの呼び出しによってアプリのコードの任意の場所からログを記述します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-385">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="9dbbc-386">コンストラクターの挿入とログ記録メソッドの呼び出しが強調表示されている `ILogger` オブジェクトを使用するサンプル コードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-386">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="9dbbc-387">`ILogger` インターフェイスは、ログ プロバイダーに任意の数のフィールドを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-387">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="9dbbc-388">このフィールドは、一般的にメッセージの文字列を構築するために使用しますが、プロバイダーがデータ ストアに別のフィールドとして送信することも可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-388">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="9dbbc-389">この機能は、[構造化ロギングとも呼ばれるセマンティック ロギング](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をログ プロバイダーが実装するのを可能にします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-389">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="9dbbc-390">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-390">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="9dbbc-391">ルーティング</span><span class="sxs-lookup"><span data-stu-id="9dbbc-391">Routing</span></span>

<span data-ttu-id="9dbbc-392">*ルート*とは、ハンドラーにマップされている URL のパターンです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-392">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="9dbbc-393">このハンドラーは一般的には Razor Pages、MVC コントローラーのアクション メソッドまたはミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-393">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="9dbbc-394">ASP.NET Core のルーティングでは、アプリで使用する URL を制御できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-394">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="9dbbc-395">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-395">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="9dbbc-396">エラー処理</span><span class="sxs-lookup"><span data-stu-id="9dbbc-396">Error handling</span></span>

<span data-ttu-id="9dbbc-397">ASP.NET Core には、次などのエラー処理用の機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-397">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="9dbbc-398">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-398">A developer exception page</span></span>
* <span data-ttu-id="9dbbc-399">カスタム エラー ページ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-399">Custom error pages</span></span>
* <span data-ttu-id="9dbbc-400">静的状態コード ページ</span><span class="sxs-lookup"><span data-stu-id="9dbbc-400">Static status code pages</span></span>
* <span data-ttu-id="9dbbc-401">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="9dbbc-401">Startup exception handling</span></span>

<span data-ttu-id="9dbbc-402">詳細については、「<xref:fundamentals/error-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-402">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="9dbbc-403">HTTP 要求を行う</span><span class="sxs-lookup"><span data-stu-id="9dbbc-403">Make HTTP requests</span></span>

<span data-ttu-id="9dbbc-404">`HttpClient` インスタンスの作成に、`IHttpClientFactory` の実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-404">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="9dbbc-405">ファクトリは次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-405">The factory:</span></span>

* <span data-ttu-id="9dbbc-406">論理 `HttpClient` インスタンスの名前付けと構成を一元化します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-406">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="9dbbc-407">たとえば、*github* クライアントを登録して、GitHub にアクセスするように構成できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-407">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="9dbbc-408">既定のクライアントは、他の目的に登録できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-408">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="9dbbc-409">複数のデリゲート ハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築するのをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-409">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="9dbbc-410">このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-410">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="9dbbc-411">このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-411">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="9dbbc-412">一時的な障害処理用の人気のサードパーティ製ライブラリ、*Polly* と統合できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-412">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="9dbbc-413">基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-413">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="9dbbc-414">ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-414">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="9dbbc-415">詳細については、「<xref:fundamentals/http-requests>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-415">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="9dbbc-416">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="9dbbc-416">Content root</span></span>

<span data-ttu-id="9dbbc-417">コンテンツ ルートは、以下に対する基本パスです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-417">The content root is the base path to the:</span></span>

* <span data-ttu-id="9dbbc-418">アプリをホストしている実行可能ファイル (*.exe*)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-418">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="9dbbc-419">アプリを構成するコンパイル済みアセンブリ (*.dll*)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-419">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="9dbbc-420">次のような、アプリで使用される非コード コンテンツ ファイル。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-420">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="9dbbc-421">Razor ファイル (*.cshtml*、*.razor*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-421">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="9dbbc-422">構成ファイル (*.json*、*.xml*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-422">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="9dbbc-423">データ ファイル (*.db*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-423">Data files (*.db*)</span></span>
* <span data-ttu-id="9dbbc-424">[Web ルート](#web-root) (通常、発行された *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-424">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="9dbbc-425">開発中:</span><span class="sxs-lookup"><span data-stu-id="9dbbc-425">During development:</span></span>

* <span data-ttu-id="9dbbc-426">コンテンツ ルートの規定値は、プロジェクトのルート ディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-426">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="9dbbc-427">プロジェクトのルート ディレクトリは、次を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-427">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="9dbbc-428">プロジェクトのルート ディレクトリ内にある、アプリの非コード コンテンツ ファイルへのパス。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-428">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="9dbbc-429">[Web ルート](#web-root) (通常は、プロジェクトのルート ディレクトリ内の *wwwroot* フォルダー)。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-429">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="9dbbc-430">[ホストの構築時](#host)には、別のコンテンツ ルート パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-430">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="9dbbc-431">詳細については、「<xref:fundamentals/host/web-host#content-root>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-431">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="9dbbc-432">Web ルート</span><span class="sxs-lookup"><span data-stu-id="9dbbc-432">Web root</span></span>

<span data-ttu-id="9dbbc-433">Web ルートは、次のような、パブリックで非コードの静的なリソース ファイルへの基本パスです。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-433">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="9dbbc-434">スタイルシート (*.css*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-434">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="9dbbc-435">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-435">JavaScript (*.js*)</span></span>
* <span data-ttu-id="9dbbc-436">画像 (*.png*、*jpg*)</span><span class="sxs-lookup"><span data-stu-id="9dbbc-436">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="9dbbc-437">既定で、静的ファイルは Web ルート ディレクトリ (とサブディレクトリ) からのみ提供されます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-437">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="9dbbc-438">Web ルートのパスの既定値は、"*{コンテンツ ルート}/wwwroot*" ですが、[ホストの構築](#host)時に別の Web ルートを指定することも可能です。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-438">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="9dbbc-439">詳細については、「[Web ルート](xref:fundamentals/host/web-host#web-root)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-439">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="9dbbc-440">プロジェクト ファイル内の [\<コンテンツ > プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items#content) を使用して *wwwroot* にファイルを発行できないようにします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-440">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="9dbbc-441">次の例では、*wwwroot/local* ディレクトリおよびサブディレクトリにコンテンツを公開しないようにします。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-441">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="9dbbc-442">Razor (*.cshtml*) ファイルの場合、チルダとスラッシュ (`~/`) が Web ルートを指します。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-442">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="9dbbc-443">`~/` で始まるパスは、"*仮想パス*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-443">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="9dbbc-444">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9dbbc-444">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
