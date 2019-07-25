---
title: ASP.NET Core への Kestrel Web サーバーの実装
author: guardrex
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/24/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 0a2072c3c97faaf51c36df63a5751246d344a971
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856188"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="38788-103">ASP.NET Core への Kestrel Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="38788-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="38788-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="38788-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

<span data-ttu-id="38788-105">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="38788-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="38788-106">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="38788-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="38788-107">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="38788-107">Kestrel supports the following scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="38788-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="38788-108">HTTPS</span></span>
* <span data-ttu-id="38788-109">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="38788-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="38788-110">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="38788-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="38788-111">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="38788-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="38788-112">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="38788-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="38788-113">HTTPS</span><span class="sxs-lookup"><span data-stu-id="38788-113">HTTPS</span></span>
* <span data-ttu-id="38788-114">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="38788-114">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="38788-115">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="38788-115">Unix sockets for high performance behind Nginx</span></span>

::: moniker-end

<span data-ttu-id="38788-116">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="38788-116">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="38788-117">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="38788-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a><span data-ttu-id="38788-118">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="38788-118">HTTP/2 support</span></span>

<span data-ttu-id="38788-119">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="38788-119">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="38788-120">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="38788-120">Operating system&dagger;</span></span>
  * <span data-ttu-id="38788-121">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="38788-121">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="38788-122">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="38788-122">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="38788-123">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="38788-123">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="38788-124">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="38788-124">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="38788-125">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="38788-125">TLS 1.2 or later connection</span></span>

<span data-ttu-id="38788-126">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="38788-126">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="38788-127">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="38788-127">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="38788-128">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="38788-128">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="38788-129">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="38788-129">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="38788-130">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="38788-130">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="38788-131">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="38788-131">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="38788-132">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="38788-132">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="38788-133">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="38788-133">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="38788-134">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="38788-134">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="38788-135">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="38788-135">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="38788-136">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="38788-136">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="38788-138">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="38788-138">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="38788-140">いずれの構成でも、&mdash;リバース プロキシ サーバーの有無に関わらず&mdash;、インターネットから要求を受け取る ASP.NET Core 2.1 またはそれ以降のアプリ用にホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="38788-140">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration for ASP.NET Core 2.1 or later apps that receive requests from the Internet.</span></span>

<span data-ttu-id="38788-141">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="38788-141">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="38788-142">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="38788-142">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="38788-143">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="38788-143">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="38788-144">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="38788-144">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="38788-145">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="38788-145">A reverse proxy:</span></span>

* <span data-ttu-id="38788-146">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="38788-146">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="38788-147">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="38788-147">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="38788-148">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="38788-148">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="38788-149">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="38788-149">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="38788-150">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーは、プレーンな HTTP を使用して内部ネットワーク上のアプリ サーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="38788-150">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with your app servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="38788-151">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="38788-151">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="38788-152">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="38788-152">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="38788-153">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[[Microsoft.AspNetCore.App metapackage]](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 以降) に含まれています。</span><span class="sxs-lookup"><span data-stu-id="38788-153">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="38788-154">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="38788-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="38788-155">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="38788-155">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="38788-156">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="38788-156">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

<span data-ttu-id="38788-157">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す**前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38788-157">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

    host.Run();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="38788-158">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38788-158">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a><span data-ttu-id="38788-159">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="38788-159">Kestrel options</span></span>

<span data-ttu-id="38788-160">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="38788-160">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="38788-161"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="38788-161">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="38788-162">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="38788-162">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="38788-163"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="38788-163">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a><span data-ttu-id="38788-164">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="38788-164">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="38788-165">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="38788-165">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="38788-166">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="38788-166">Defaults to 2 minutes.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

::: moniker-end

### <a name="maximum-client-connections"></a><span data-ttu-id="38788-167">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="38788-167">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="38788-168">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="38788-168">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="38788-169">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="38788-169">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="38788-170">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="38788-170">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="38788-171">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="38788-171">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="38788-172">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="38788-172">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="38788-173">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="38788-173">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="38788-174">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="38788-174">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="38788-175">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="38788-175">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="38788-176">ミドルウェア内の特定の要求に関する設定をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="38788-176">You can override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

<span data-ttu-id="38788-177">アプリが要求の読み取りを開始した後で、要求に対する制限を構成しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="38788-177">An exception is thrown if you attempt to configure the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="38788-178">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="38788-178">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="38788-179">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="38788-179">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="38788-180">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="38788-180">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="38788-181">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="38788-181">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="38788-182">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="38788-182">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="38788-183">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="38788-183">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="38788-184">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="38788-184">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="38788-185">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-185">A minimum rate also applies to the response.</span></span> <span data-ttu-id="38788-186">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="38788-186">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="38788-187">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="38788-187">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

<span data-ttu-id="38788-188">ミドルウェアでは要求ごとに最小レート制限をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="38788-188">You can override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38788-189">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。これは、プロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が、HTTP/2 で一般的にサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="38788-189">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="38788-190">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに "*すべて無効*" にできるためです。</span><span class="sxs-lookup"><span data-stu-id="38788-190">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="38788-191">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="38788-191">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="38788-192">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-192">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="38788-193">前のサンプルで参照したどのレート機能も HTTP/2 要求の `HttpContext.Features` には存在しません。これはプロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が HTTP/2 でサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="38788-193">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="38788-194">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-194">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

### <a name="request-headers-timeout"></a><span data-ttu-id="38788-195">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="38788-195">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="38788-196">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="38788-196">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="38788-197">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="38788-197">Defaults to 30 seconds.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="38788-198">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="38788-198">Maximum streams per connection</span></span>

<span data-ttu-id="38788-199">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="38788-199">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="38788-200">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="38788-200">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="38788-201">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="38788-201">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="38788-202">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="38788-202">Header table size</span></span>

<span data-ttu-id="38788-203">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="38788-203">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="38788-204">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="38788-204">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="38788-205">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="38788-205">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="38788-206">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="38788-206">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="38788-207">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="38788-207">Maximum frame size</span></span>

<span data-ttu-id="38788-208">受信する HTTP/2 接続フレーム ペイロードの最大サイズは、`Http2.MaxFrameSize` によって示されます。</span><span class="sxs-lookup"><span data-stu-id="38788-208">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="38788-209">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-209">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="38788-210">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="38788-210">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="38788-211">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="38788-211">Maximum request header size</span></span>

<span data-ttu-id="38788-212">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="38788-212">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="38788-213">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-213">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="38788-214">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-214">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="38788-215">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="38788-215">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="38788-216">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="38788-216">Initial connection window size</span></span>

<span data-ttu-id="38788-217">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="38788-217">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="38788-218">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="38788-218">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="38788-219">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-219">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="38788-220">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="38788-220">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="38788-221">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="38788-221">Initial stream window size</span></span>

<span data-ttu-id="38788-222">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="38788-222">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="38788-223">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="38788-223">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="38788-224">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-224">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="38788-225">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="38788-225">The default value is 96 KB (98,304).</span></span>

::: moniker-end

### <a name="synchronous-io"></a><span data-ttu-id="38788-226">同期 IO</span><span class="sxs-lookup"><span data-stu-id="38788-226">Synchronous IO</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38788-227"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="38788-227"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="38788-228">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="38788-228">The default value is `false`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="38788-229"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="38788-229"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="38788-230">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="38788-230">The  default value is `true`.</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="38788-231">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="38788-231">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="38788-232">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="38788-232">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="38788-233">同期 IO を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="38788-233">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="38788-234">同期 IO を無効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="38788-234">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.AllowSynchronousIO = false;
        });
```

::: moniker-end

<span data-ttu-id="38788-235">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="38788-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="38788-236">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="38788-236">Endpoint configuration</span></span>

<span data-ttu-id="38788-237">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="38788-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="38788-238">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="38788-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="38788-239">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="38788-239">Specify URLs using the:</span></span>

* <span data-ttu-id="38788-240">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="38788-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="38788-241">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="38788-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="38788-242">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="38788-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="38788-243">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="38788-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="38788-244">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="38788-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="38788-245">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000; http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="38788-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="38788-246">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="38788-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="38788-247">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="38788-247">A development certificate is created:</span></span>

* <span data-ttu-id="38788-248">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="38788-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="38788-249">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="38788-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="38788-250">一部のブラウザーでは、ローカル開発証明書を信頼するには、ブラウザーに明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-250">Some browsers require that you grant explicit permission to the browser to trust the local development certificate.</span></span>

<span data-ttu-id="38788-251">ASP.NET Core 2.1 およびそれより後のプロジェクト テンプレートでは、アプリは HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="38788-251">ASP.NET Core 2.1 and later project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="38788-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="38788-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="38788-253">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="38788-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="38788-254">ASP.NET Core 2.1 以降の `KestrelServerOptions` の構成:</span><span class="sxs-lookup"><span data-stu-id="38788-254">ASP.NET Core 2.1 or later `KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a><span data-ttu-id="38788-255">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="38788-255">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span></span>

<span data-ttu-id="38788-256">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="38788-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="38788-257">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="38788-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
```

::: moniker-end

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a><span data-ttu-id="38788-258">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="38788-258">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span></span>

<span data-ttu-id="38788-259">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="38788-259">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="38788-260">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="38788-260">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureHttpsDefaults(options =>
            {
                // certificate is an X509Certificate2
                options.ServerCertificate = certificate;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureHttpsDefaults(httpsOptions =>
            {
                // certificate is an X509Certificate2
                httpsOptions.ServerCertificate = certificate;
            });
        });
```

::: moniker-end

### <a name="configureiconfiguration"></a><span data-ttu-id="38788-261">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="38788-261">Configure(IConfiguration)</span></span>

<span data-ttu-id="38788-262">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="38788-262">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="38788-263">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-263">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="38788-264">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="38788-264">ListenOptions.UseHttps</span></span>

<span data-ttu-id="38788-265">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="38788-265">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="38788-266">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="38788-266">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="38788-267">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="38788-267">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="38788-268">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="38788-268">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="38788-269">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="38788-269">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="38788-270">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="38788-270">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="38788-271">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="38788-271">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="38788-272">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="38788-272">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="38788-273">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="38788-273">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="38788-274">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="38788-274">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="38788-275">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="38788-275">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="38788-276">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="38788-276">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="38788-277">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="38788-277">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="38788-278">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="38788-278">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="38788-279">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-279">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="38788-280">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-280">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="38788-281">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="38788-281">Supported configurations described next:</span></span>

* <span data-ttu-id="38788-282">構成なし</span><span class="sxs-lookup"><span data-stu-id="38788-282">No configuration</span></span>
* <span data-ttu-id="38788-283">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="38788-283">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="38788-284">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="38788-284">Change the defaults in code</span></span>

<span data-ttu-id="38788-285">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="38788-285">*No configuration*</span></span>

<span data-ttu-id="38788-286">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="38788-286">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="38788-287">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="38788-287">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="38788-288"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="38788-288"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="38788-289">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="38788-289">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="38788-290">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="38788-290">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="38788-291">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="38788-291">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="38788-292">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="38788-292">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="38788-293">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]**  >  **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="38788-293">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
"Kestrel": {
  "Endpoints": {
    "Http": {
      "Url": "http://localhost:5000"
    },

    "HttpsInlineCertFile": {
      "Url": "https://localhost:5001",
      "Certificate": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    },

    "HttpsInlineCertStore": {
      "Url": "https://localhost:5002",
      "Certificate": {
        "Subject": "<subject; required>",
        "Store": "<certificate store; required>",
        "Location": "<location; defaults to CurrentUser>",
        "AllowInvalid": "<true or false; defaults to false>"
      }
    },

    "HttpsDefaultCert": {
      "Url": "https://localhost:5003"
    },

    "Https": {
      "Url": "https://*:5004",
      "Certificate": {
      "Path": "<path to .pfx file>",
      "Password": "<certificate password>"
      }
    }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="38788-294">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="38788-294">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="38788-295">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="38788-295">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="38788-296">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="38788-296">Schema notes:</span></span>

* <span data-ttu-id="38788-297">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="38788-297">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="38788-298">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="38788-298">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="38788-299">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="38788-299">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="38788-300">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="38788-300">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="38788-301">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="38788-301">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="38788-302">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="38788-302">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="38788-303">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="38788-303">The `Certificate` section is optional.</span></span> <span data-ttu-id="38788-304">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-304">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="38788-305">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="38788-305">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="38788-306">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="38788-306">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="38788-307">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="38788-307">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="38788-308">`options.Configure(context.Configuration.GetSection("Kestrel"))` が `.Endpoint(string name, options => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="38788-308">`options.Configure(context.Configuration.GetSection("Kestrel"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, options => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  <span data-ttu-id="38788-309">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することもできます。</span><span class="sxs-lookup"><span data-stu-id="38788-309">You can also directly access `KestrelServerOptions.ConfigurationLoader` to keep iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="38788-310">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="38788-310">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="38788-311">別のセクションで `options.Configure(context.Configuration.GetSection("Kestrel"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="38788-311">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("Kestrel"))` again with another section.</span></span> <span data-ttu-id="38788-312">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-312">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="38788-313">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="38788-313">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="38788-314">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="38788-314">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="38788-315">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-315">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="38788-316">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="38788-316">*Change the defaults in code*</span></span>

<span data-ttu-id="38788-317">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="38788-317">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="38788-318">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

<span data-ttu-id="38788-319">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="38788-319">*Kestrel support for SNI*</span></span>

<span data-ttu-id="38788-320">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="38788-320">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="38788-321">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="38788-321">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="38788-322">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="38788-322">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="38788-323">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="38788-323">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="38788-324">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="38788-324">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="38788-325">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="38788-325">SNI support requires:</span></span>

* <span data-ttu-id="38788-326">ターゲット フレームワーク `netcoreapp2.1` で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="38788-326">Running on target framework `netcoreapp2.1`.</span></span> <span data-ttu-id="38788-327">`netcoreapp2.0` および `net461` の場合は、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="38788-327">On `netcoreapp2.0` and `net461`, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="38788-328">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="38788-328">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="38788-329">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="38788-329">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="38788-330">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="38788-330">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="38788-331">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="38788-331">Bind to a TCP socket</span></span>

<span data-ttu-id="38788-332"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="38788-332">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

<span data-ttu-id="38788-333">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="38788-333">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="38788-334">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="38788-334">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="38788-335">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="38788-335">Bind to a Unix socket</span></span>

<span data-ttu-id="38788-336">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="38788-336">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a><span data-ttu-id="38788-337">ポート 0</span><span class="sxs-lookup"><span data-stu-id="38788-337">Port 0</span></span>

<span data-ttu-id="38788-338">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="38788-338">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="38788-339">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="38788-339">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="38788-340">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="38788-340">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="38788-341">制限事項</span><span class="sxs-lookup"><span data-stu-id="38788-341">Limitations</span></span>

<span data-ttu-id="38788-342">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="38788-342">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="38788-343">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="38788-343">`--urls` command-line argument</span></span>
* <span data-ttu-id="38788-344">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="38788-344">`urls` host configuration key</span></span>
* <span data-ttu-id="38788-345">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="38788-345">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="38788-346">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="38788-346">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="38788-347">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="38788-347">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="38788-348">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="38788-348">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="38788-349">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="38788-349">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="38788-350">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="38788-350">IIS endpoint configuration</span></span>

<span data-ttu-id="38788-351">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="38788-351">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="38788-352">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="38788-352">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a><span data-ttu-id="38788-353">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="38788-353">ListenOptions.Protocols</span></span>

<span data-ttu-id="38788-354">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="38788-354">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="38788-355">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="38788-355">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="38788-356">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="38788-356">`HttpProtocols` enum value</span></span> | <span data-ttu-id="38788-357">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="38788-357">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="38788-358">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="38788-358">HTTP/1.1 only.</span></span> <span data-ttu-id="38788-359">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="38788-359">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="38788-360">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="38788-360">HTTP/2 only.</span></span> <span data-ttu-id="38788-361">主に TLS と一緒に使用されます。</span><span class="sxs-lookup"><span data-stu-id="38788-361">Primarily used with TLS.</span></span> <span data-ttu-id="38788-362">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="38788-362">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="38788-363">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="38788-363">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="38788-364">HTTP/2 をネゴシエートするには TLS と[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) が必要です。それ以外の場合、接続は既定では HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="38788-364">Requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection to negotiate HTTP/2; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="38788-365">既定のプロトコルは HTTP/1.1 です。</span><span class="sxs-lookup"><span data-stu-id="38788-365">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="38788-366">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="38788-366">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="38788-367">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="38788-367">TLS version 1.2 or later</span></span>
* <span data-ttu-id="38788-368">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="38788-368">Renegotiation disabled</span></span>
* <span data-ttu-id="38788-369">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="38788-369">Compression disabled</span></span>
* <span data-ttu-id="38788-370">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="38788-370">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="38788-371">Elliptic curve Diffie-hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小で 224 ビット</span><span class="sxs-lookup"><span data-stu-id="38788-371">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="38788-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小で 2048 ビット</span><span class="sxs-lookup"><span data-stu-id="38788-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="38788-373">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="38788-373">Cipher suite not blacklisted</span></span>

<span data-ttu-id="38788-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="38788-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="38788-375">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="38788-375">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="38788-376">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="38788-376">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="38788-377">必要に応じて、`IConnectionAdapter` 実装を作成することで、接続ごとに特定の暗号について TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="38788-377">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
                listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
            });
        });
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that you don't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null
        // indicates that no cipher algorithm supported by Kestrel matches the
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="38788-378">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="38788-378">*Set the protocol from configuration*</span></span>

<span data-ttu-id="38788-379"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="38788-379"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="38788-380">次に示す *appsettings.json* の例では、Kestrel のエンドポイントのすべてにおいて、既定の接続プロトコル (HTTP/1.1 および HTTP/2) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="38788-380">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="38788-381">次に示す構成ファイルの例では、特定のエンドポイントに対して接続プロトコルが確定されます。</span><span class="sxs-lookup"><span data-stu-id="38788-381">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="38788-382">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="38788-382">Protocols specified in code override values set by configuration.</span></span>

::: moniker-end

## <a name="transport-configuration"></a><span data-ttu-id="38788-383">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="38788-383">Transport configuration</span></span>

<span data-ttu-id="38788-384">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="38788-384">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="38788-385">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="38788-385">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="38788-386">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="38788-386">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="38788-387">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="38788-387">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="38788-388">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) メタパッケージを使用し Libuv を使用する必要のある ASP.NET Core 2.1 以降のプロジェクトでは、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="38788-388">For ASP.NET Core 2.1 or later projects that use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and require the use of Libuv:</span></span>

* <span data-ttu-id="38788-389">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="38788-389">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                      Version="<LATEST_VERSION>" />
    ```

* <span data-ttu-id="38788-390"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38788-390">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

    ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseLibuv()
                .UseStartup<Startup>();
    }
    ```

### <a name="url-prefixes"></a><span data-ttu-id="38788-391">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="38788-391">URL prefixes</span></span>

<span data-ttu-id="38788-392">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="38788-392">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="38788-393">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="38788-393">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="38788-394">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="38788-394">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="38788-395">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="38788-395">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="38788-396">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="38788-396">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="38788-397">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="38788-397">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="38788-398">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="38788-398">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="38788-399">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="38788-399">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="38788-400">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="38788-400">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="38788-401">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="38788-401">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="38788-402">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="38788-402">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="38788-403">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="38788-403">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="38788-404">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="38788-404">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="38788-405">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="38788-405">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="38788-406">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="38788-406">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="38788-407">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="38788-407">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="38788-408">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="38788-408">Host filtering</span></span>

<span data-ttu-id="38788-409">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="38788-409">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="38788-410">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="38788-410">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="38788-411">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="38788-411">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="38788-412">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="38788-412">`Host` headers aren't validated.</span></span>

<span data-ttu-id="38788-413">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="38788-413">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="38788-414">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 以降) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="38788-414">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="38788-415">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="38788-415">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="38788-416">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="38788-416">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="38788-417">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="38788-417">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="38788-418">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="38788-418">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="38788-419">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="38788-419">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="38788-420">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="38788-420">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="38788-421">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="38788-421">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="38788-422">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="38788-422">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="38788-423">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="38788-423">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="38788-424">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="38788-424">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38788-425">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="38788-425">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="38788-426">Kestrel ソース コード</span><span class="sxs-lookup"><span data-stu-id="38788-426">Kestrel source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Servers/Kestrel)
* [<span data-ttu-id="38788-427">RFC 7230: メッセージの構文と経路制御 (セクション 5.4: ホスト)</span><span class="sxs-lookup"><span data-stu-id="38788-427">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
