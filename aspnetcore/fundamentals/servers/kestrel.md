---
title: ASP.NET Core への Kestrel Web サーバーの実装
author: guardrex
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 9fbf0ec93634100fccef279fc7cad92cb1420e84
ms.sourcegitcommit: 991442dfb16ef08a0aae05bc79f9e9a2d819c587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492602"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="58188-103">ASP.NET Core への Kestrel Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="58188-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="58188-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Stephen Halter](https://twitter.com/halter73)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="58188-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58188-105">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="58188-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="58188-106">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="58188-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="58188-107">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="58188-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="58188-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="58188-108">HTTPS</span></span>
* <span data-ttu-id="58188-109">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="58188-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="58188-110">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="58188-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="58188-111">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="58188-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="58188-112">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="58188-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="58188-113">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="58188-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="58188-114">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="58188-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="58188-115">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="58188-115">HTTP/2 support</span></span>

<span data-ttu-id="58188-116">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="58188-117">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="58188-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="58188-118">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="58188-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="58188-119">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="58188-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="58188-120">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="58188-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="58188-121">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="58188-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="58188-122">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="58188-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="58188-123">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="58188-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="58188-124">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="58188-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="58188-125">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="58188-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="58188-126">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="58188-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="58188-127">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="58188-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="58188-128">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="58188-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="58188-129">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="58188-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="58188-130">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="58188-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="58188-131">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="58188-132">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="58188-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="58188-133">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58188-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="58188-135">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58188-135">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="58188-137">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="58188-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="58188-138">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="58188-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="58188-139">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="58188-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="58188-140">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="58188-141">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="58188-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="58188-142">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="58188-142">A reverse proxy:</span></span>

* <span data-ttu-id="58188-143">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="58188-144">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="58188-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="58188-145">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="58188-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="58188-146">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="58188-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="58188-147">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="58188-148">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="58188-149">ASP.NET Core アプリでの Kestrel</span><span class="sxs-lookup"><span data-stu-id="58188-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="58188-150">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="58188-151">*Program.cs* では、<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> メソッドにより <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="58188-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="58188-152">ホストのビルトに関する詳細については、「<xref:fundamentals/host/generic-host#set-up-a-host>」の「*ホストを設定する*」と「*既定の builder 設定*」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="58188-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="58188-153">`ConfigureWebHostDefaults` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="58188-154">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="58188-154">Kestrel options</span></span>

<span data-ttu-id="58188-155">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="58188-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="58188-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="58188-157">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="58188-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="58188-158"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58188-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="58188-159">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-159">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="58188-160">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="58188-160">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

<span data-ttu-id="58188-161">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-161">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="58188-162">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="58188-162">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="58188-163">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="58188-163">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="58188-164">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="58188-164">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="58188-165">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-165">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="58188-166">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="58188-166">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="58188-167">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-167">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="58188-168">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-168">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="58188-169">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="58188-169">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="58188-170">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-170">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="58188-171">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="58188-171">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="58188-172">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="58188-172">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="58188-173">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-173">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="58188-174">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="58188-174">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="58188-175">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="58188-175">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="58188-176">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="58188-176">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="58188-177">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-177">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="58188-178">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="58188-178">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="58188-179">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="58188-179">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="58188-180">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-180">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="58188-181">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-181">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="58188-182">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-182">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="58188-183">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="58188-183">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="58188-184">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="58188-184">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="58188-185">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="58188-185">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="58188-186">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="58188-186">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="58188-187">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="58188-187">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="58188-188">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="58188-188">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="58188-189">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="58188-189">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="58188-190">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-190">A minimum rate also applies to the response.</span></span> <span data-ttu-id="58188-191">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="58188-191">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="58188-192">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-192">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="58188-193">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-193">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="58188-194">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。これは、プロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が、HTTP/2 で一般的にサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="58188-194">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="58188-195">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに "*すべて無効*" にできるためです。</span><span class="sxs-lookup"><span data-stu-id="58188-195">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="58188-196">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-196">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="58188-197">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-197">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="58188-198">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="58188-198">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="58188-199">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-199">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="58188-200">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="58188-200">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="58188-201">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="58188-201">Maximum streams per connection</span></span>

<span data-ttu-id="58188-202">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="58188-202">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="58188-203">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="58188-203">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="58188-204">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="58188-204">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="58188-205">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="58188-205">Header table size</span></span>

<span data-ttu-id="58188-206">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="58188-206">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="58188-207">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="58188-207">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="58188-208">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="58188-208">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="58188-209">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="58188-209">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="58188-210">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-210">Maximum frame size</span></span>

<span data-ttu-id="58188-211">`Http2.MaxFrameSize` は、サーバーによって受信または送信された HTTP/2 接続フレーム ペイロードの最大許容サイズを示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-211">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="58188-212">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-212">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="58188-213">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="58188-213">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="58188-214">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-214">Maximum request header size</span></span>

<span data-ttu-id="58188-215">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-215">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="58188-216">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-216">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="58188-217">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-217">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="58188-218">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="58188-218">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="58188-219">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-219">Initial connection window size</span></span>

<span data-ttu-id="58188-220">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-220">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="58188-221">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="58188-221">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="58188-222">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-222">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="58188-223">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="58188-223">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="58188-224">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-224">Initial stream window size</span></span>

<span data-ttu-id="58188-225">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-225">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="58188-226">要求は `Http2.InitialConnectionWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="58188-226">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="58188-227">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-227">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="58188-228">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="58188-228">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="58188-229">同期 IO</span><span class="sxs-lookup"><span data-stu-id="58188-229">Synchronous IO</span></span>

<span data-ttu-id="58188-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="58188-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="58188-231">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="58188-231">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="58188-232">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="58188-232">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="58188-233">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="58188-233">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="58188-234">同期 IO を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58188-234">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="58188-235">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="58188-236">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="58188-236">Endpoint configuration</span></span>

<span data-ttu-id="58188-237">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="58188-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="58188-238">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="58188-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="58188-239">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-239">Specify URLs using the:</span></span>

* <span data-ttu-id="58188-240">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="58188-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="58188-241">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="58188-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="58188-242">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="58188-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="58188-243">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="58188-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="58188-244">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="58188-245">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000; http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="58188-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="58188-246">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="58188-247">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="58188-247">A development certificate is created:</span></span>

* <span data-ttu-id="58188-248">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="58188-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="58188-249">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="58188-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="58188-250">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-250">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="58188-251">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="58188-251">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="58188-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="58188-253">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="58188-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="58188-254">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="58188-254">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="58188-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="58188-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="58188-256">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="58188-257">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="58188-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="58188-258"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-258">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="58188-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="58188-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="58188-260">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="58188-261">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="58188-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="58188-262"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-262">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="58188-263">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="58188-263">Configure(IConfiguration)</span></span>

<span data-ttu-id="58188-264">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="58188-264">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="58188-265">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-265">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="58188-266">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="58188-266">ListenOptions.UseHttps</span></span>

<span data-ttu-id="58188-267">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-267">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="58188-268">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="58188-268">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="58188-269">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-269">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="58188-270">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-270">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="58188-271">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="58188-271">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="58188-272">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="58188-272">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="58188-273">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="58188-273">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="58188-274">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="58188-274">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="58188-275">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="58188-275">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="58188-276">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="58188-276">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="58188-277">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="58188-277">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="58188-278">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="58188-278">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="58188-279">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="58188-279">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="58188-280">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="58188-280">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="58188-281">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-281">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="58188-282">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-282">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="58188-283">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="58188-283">Supported configurations described next:</span></span>

* <span data-ttu-id="58188-284">構成なし</span><span class="sxs-lookup"><span data-stu-id="58188-284">No configuration</span></span>
* <span data-ttu-id="58188-285">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="58188-285">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="58188-286">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="58188-286">Change the defaults in code</span></span>

<span data-ttu-id="58188-287">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="58188-287">*No configuration*</span></span>

<span data-ttu-id="58188-288">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="58188-288">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="58188-289">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="58188-289">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="58188-290">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-290">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="58188-291">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-291">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="58188-292">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-292">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="58188-293">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="58188-293">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="58188-294">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="58188-294">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="58188-295">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="58188-295">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="58188-296">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="58188-296">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="58188-297">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-297">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="58188-298">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="58188-298">Schema notes:</span></span>

* <span data-ttu-id="58188-299">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="58188-299">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="58188-300">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="58188-300">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="58188-301">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-301">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="58188-302">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="58188-302">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="58188-303">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="58188-303">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="58188-304">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="58188-304">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="58188-305">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="58188-305">The `Certificate` section is optional.</span></span> <span data-ttu-id="58188-306">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-306">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="58188-307">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="58188-307">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="58188-308">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="58188-308">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="58188-309">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="58188-309">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="58188-310">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="58188-310">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="58188-311">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-311">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="58188-312">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-312">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="58188-313">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="58188-313">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="58188-314">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-314">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="58188-315">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="58188-315">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="58188-316">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="58188-316">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="58188-317">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-317">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="58188-318">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="58188-318">*Change the defaults in code*</span></span>

<span data-ttu-id="58188-319">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="58188-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="58188-320">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-320">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="58188-321">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="58188-321">*Kestrel support for SNI*</span></span>

<span data-ttu-id="58188-322">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="58188-322">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="58188-323">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="58188-323">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="58188-324">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-324">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="58188-325">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="58188-325">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="58188-326">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="58188-326">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="58188-327">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-327">SNI support requires:</span></span>

* <span data-ttu-id="58188-328">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-328">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="58188-329">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="58188-329">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="58188-330">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="58188-330">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="58188-331">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="58188-331">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="58188-332">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="58188-332">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
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

### <a name="connection-logging"></a><span data-ttu-id="58188-333">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="58188-333">Connection logging</span></span>

<span data-ttu-id="58188-334">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-334">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="58188-335">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="58188-335">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="58188-336">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="58188-336">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="58188-337">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="58188-337">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="58188-338">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="58188-338">Bind to a TCP socket</span></span>

<span data-ttu-id="58188-339"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="58188-339">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="58188-340">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="58188-340">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="58188-341">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-341">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="58188-342">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="58188-342">Bind to a Unix socket</span></span>

<span data-ttu-id="58188-343">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="58188-343">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="58188-344">ポート 0</span><span class="sxs-lookup"><span data-stu-id="58188-344">Port 0</span></span>

<span data-ttu-id="58188-345">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-345">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="58188-346">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-346">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="58188-347">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="58188-347">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="58188-348">制限事項</span><span class="sxs-lookup"><span data-stu-id="58188-348">Limitations</span></span>

<span data-ttu-id="58188-349">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-349">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="58188-350">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="58188-350">`--urls` command-line argument</span></span>
* <span data-ttu-id="58188-351">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="58188-351">`urls` host configuration key</span></span>
* <span data-ttu-id="58188-352">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="58188-352">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="58188-353">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="58188-353">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="58188-354">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="58188-354">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="58188-355">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="58188-355">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="58188-356">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-356">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="58188-357">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="58188-357">IIS endpoint configuration</span></span>

<span data-ttu-id="58188-358">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-358">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="58188-359">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-359">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="58188-360">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="58188-360">ListenOptions.Protocols</span></span>

<span data-ttu-id="58188-361">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="58188-361">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="58188-362">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="58188-362">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="58188-363">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="58188-363">`HttpProtocols` enum value</span></span> | <span data-ttu-id="58188-364">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="58188-364">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="58188-365">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="58188-365">HTTP/1.1 only.</span></span> <span data-ttu-id="58188-366">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-366">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="58188-367">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="58188-367">HTTP/2 only.</span></span> <span data-ttu-id="58188-368">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-368">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="58188-369">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="58188-369">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="58188-370">HTTP/2 では、クライアントが TLS [アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクで HTTP/2 を選択する必要があります。それ以外の場合、接続は既定で HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="58188-370">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="58188-371">任意のエンドポイントに対する `ListenOptions.Protocols` の既定値は `HttpProtocols.Http1AndHttp2` です。</span><span class="sxs-lookup"><span data-stu-id="58188-371">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="58188-372">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="58188-372">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="58188-373">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="58188-373">TLS version 1.2 or later</span></span>
* <span data-ttu-id="58188-374">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="58188-374">Renegotiation disabled</span></span>
* <span data-ttu-id="58188-375">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="58188-375">Compression disabled</span></span>
* <span data-ttu-id="58188-376">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="58188-376">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="58188-377">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小で 224 ビット</span><span class="sxs-lookup"><span data-stu-id="58188-377">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="58188-378">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小で 2048 ビット</span><span class="sxs-lookup"><span data-stu-id="58188-378">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="58188-379">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="58188-379">Cipher suite not blacklisted</span></span>

<span data-ttu-id="58188-380">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="58188-380">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="58188-381">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="58188-381">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="58188-382">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="58188-382">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="58188-383">必要に応じて、接続ミドルウェアを使用し、特定の暗号のために接続ごとに TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="58188-383">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="58188-384">次の例では、アプリでサポートされていないすべての暗号アルゴリズムに対して <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-384">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="58188-385">または、[ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) を定義して、指定できる暗号スイートの一覧と比較します。</span><span class="sxs-lookup"><span data-stu-id="58188-385">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="58188-386">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 暗号アルゴリズムでは、暗号化は使用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-386">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="58188-387">接続のフィルター処理は、<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> のラムダを使って構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-387">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="58188-388">Linux では、<xref:System.Net.Security.CipherSuitesPolicy> を使って、接続ごとに TLS ハンドシェイクをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="58188-388">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="58188-389">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="58188-389">*Set the protocol from configuration*</span></span>

<span data-ttu-id="58188-390">`CreateDefaultBuilder` は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-390">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="58188-391">次の *appsettings.json* の例では、すべてのエンドポイントにおける既定の接続プロトコルとして HTTP/1.1 を確立しています。</span><span class="sxs-lookup"><span data-stu-id="58188-391">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="58188-392">次の *appsettings.json* の例では、特定のエンドポイントにおける HTTP/1.1 接続プロトコルを確立しています。</span><span class="sxs-lookup"><span data-stu-id="58188-392">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="58188-393">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="58188-393">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="58188-394">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="58188-394">Transport configuration</span></span>

<span data-ttu-id="58188-395">Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="58188-395">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="58188-396">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="58188-396">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="58188-397">`IWebHostBuilder` で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す</span><span class="sxs-lookup"><span data-stu-id="58188-397">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="58188-398">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="58188-398">URL prefixes</span></span>

<span data-ttu-id="58188-399">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="58188-399">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="58188-400">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="58188-400">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="58188-401">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="58188-401">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="58188-402">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-402">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="58188-403">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="58188-403">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="58188-404">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-404">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="58188-405">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="58188-405">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="58188-406">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-406">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="58188-407">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="58188-407">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="58188-408">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-408">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="58188-409">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-409">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="58188-410">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-410">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="58188-411">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-411">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="58188-412">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="58188-412">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="58188-413">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="58188-413">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="58188-414">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="58188-414">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="58188-415">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="58188-415">Host filtering</span></span>

<span data-ttu-id="58188-416">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="58188-416">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="58188-417">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="58188-417">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="58188-418">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-418">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="58188-419">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="58188-419">`Host` headers aren't validated.</span></span>

<span data-ttu-id="58188-420">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-420">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="58188-421">Host Filtering Middleware は、ASP.NET Core アプリに暗黙的に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="58188-421">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="58188-422">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="58188-422">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="58188-423">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="58188-423">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="58188-424">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="58188-424">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="58188-425">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="58188-425">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="58188-426">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="58188-426">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="58188-427">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="58188-427">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="58188-428">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="58188-428">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="58188-429">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="58188-429">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="58188-430">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="58188-430">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="58188-431">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58188-431">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="58188-432">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="58188-432">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="58188-433">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="58188-433">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="58188-434">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="58188-434">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="58188-435">HTTPS</span><span class="sxs-lookup"><span data-stu-id="58188-435">HTTPS</span></span>
* <span data-ttu-id="58188-436">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="58188-436">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="58188-437">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="58188-437">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="58188-438">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="58188-438">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="58188-439">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="58188-439">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="58188-440">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="58188-440">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="58188-441">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="58188-441">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="58188-442">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="58188-442">HTTP/2 support</span></span>

<span data-ttu-id="58188-443">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-443">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="58188-444">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="58188-444">Operating system&dagger;</span></span>
  * <span data-ttu-id="58188-445">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="58188-445">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="58188-446">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="58188-446">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="58188-447">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="58188-447">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="58188-448">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="58188-448">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="58188-449">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="58188-449">TLS 1.2 or later connection</span></span>

<span data-ttu-id="58188-450">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="58188-450">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="58188-451">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="58188-451">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="58188-452">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="58188-452">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="58188-453">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="58188-453">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="58188-454">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="58188-454">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="58188-455">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="58188-455">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="58188-456">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="58188-456">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="58188-457">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="58188-457">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="58188-458">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-458">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="58188-459">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="58188-459">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="58188-460">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58188-460">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="58188-462">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58188-462">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="58188-464">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="58188-464">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="58188-465">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="58188-465">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="58188-466">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="58188-466">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="58188-467">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-467">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="58188-468">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="58188-468">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="58188-469">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="58188-469">A reverse proxy:</span></span>

* <span data-ttu-id="58188-470">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-470">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="58188-471">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="58188-471">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="58188-472">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="58188-472">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="58188-473">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="58188-473">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="58188-474">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-474">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="58188-475">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-475">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="58188-476">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="58188-476">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="58188-477">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="58188-477">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="58188-478">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-478">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="58188-479">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="58188-479">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="58188-480">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-480">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="58188-481">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-481">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="58188-482">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す**前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-482">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="58188-483">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="58188-483">Kestrel options</span></span>

<span data-ttu-id="58188-484">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="58188-484">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="58188-485"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-485">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="58188-486">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="58188-486">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="58188-487"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58188-487">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="58188-488">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-488">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="58188-489">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="58188-489">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="58188-490">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-490">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="58188-491">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="58188-491">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="58188-492">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="58188-492">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="58188-493">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="58188-493">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="58188-494">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-494">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="58188-495">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="58188-495">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="58188-496">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-496">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="58188-497">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-497">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="58188-498">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="58188-498">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="58188-499">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-499">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="58188-500">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="58188-500">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="58188-501">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="58188-501">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="58188-502">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-502">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="58188-503">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="58188-503">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="58188-504">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="58188-504">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="58188-505">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="58188-505">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="58188-506">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-506">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="58188-507">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="58188-507">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="58188-508">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="58188-508">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="58188-509">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-509">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="58188-510">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-510">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="58188-511">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-511">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="58188-512">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="58188-512">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="58188-513">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="58188-513">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="58188-514">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="58188-514">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="58188-515">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="58188-515">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="58188-516">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="58188-516">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="58188-517">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="58188-517">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="58188-518">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="58188-518">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="58188-519">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-519">A minimum rate also applies to the response.</span></span> <span data-ttu-id="58188-520">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="58188-520">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="58188-521">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-521">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="58188-522">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-522">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="58188-523">前のサンプルで参照したどのレート機能も HTTP/2 要求の `HttpContext.Features` には存在しません。これはプロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が HTTP/2 でサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="58188-523">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="58188-524">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-524">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="58188-525">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="58188-525">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="58188-526">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-526">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="58188-527">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="58188-527">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="58188-528">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="58188-528">Maximum streams per connection</span></span>

<span data-ttu-id="58188-529">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="58188-529">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="58188-530">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="58188-530">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="58188-531">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="58188-531">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="58188-532">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="58188-532">Header table size</span></span>

<span data-ttu-id="58188-533">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="58188-533">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="58188-534">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="58188-534">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="58188-535">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="58188-535">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="58188-536">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="58188-536">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="58188-537">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-537">Maximum frame size</span></span>

<span data-ttu-id="58188-538">受信する HTTP/2 接続フレーム ペイロードの最大サイズは、`Http2.MaxFrameSize` によって示されます。</span><span class="sxs-lookup"><span data-stu-id="58188-538">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="58188-539">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-539">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="58188-540">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="58188-540">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="58188-541">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-541">Maximum request header size</span></span>

<span data-ttu-id="58188-542">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-542">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="58188-543">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-543">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="58188-544">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-544">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="58188-545">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="58188-545">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="58188-546">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-546">Initial connection window size</span></span>

<span data-ttu-id="58188-547">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-547">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="58188-548">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="58188-548">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="58188-549">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-549">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="58188-550">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="58188-550">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="58188-551">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-551">Initial stream window size</span></span>

<span data-ttu-id="58188-552">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="58188-552">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="58188-553">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="58188-553">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="58188-554">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-554">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="58188-555">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="58188-555">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="58188-556">同期 IO</span><span class="sxs-lookup"><span data-stu-id="58188-556">Synchronous IO</span></span>

<span data-ttu-id="58188-557"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="58188-557"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="58188-558">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="58188-558">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="58188-559">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="58188-559">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="58188-560">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="58188-560">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="58188-561">同期 IO を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58188-561">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="58188-562">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-562">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="58188-563">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="58188-563">Endpoint configuration</span></span>

<span data-ttu-id="58188-564">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="58188-564">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="58188-565">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="58188-565">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="58188-566">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-566">Specify URLs using the:</span></span>

* <span data-ttu-id="58188-567">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="58188-567">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="58188-568">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="58188-568">`--urls` command-line argument.</span></span>
* <span data-ttu-id="58188-569">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="58188-569">`urls` host configuration key.</span></span>
* <span data-ttu-id="58188-570">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="58188-570">`UseUrls` extension method.</span></span>

<span data-ttu-id="58188-571">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-571">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="58188-572">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000; http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="58188-572">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="58188-573">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-573">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="58188-574">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="58188-574">A development certificate is created:</span></span>

* <span data-ttu-id="58188-575">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="58188-575">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="58188-576">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="58188-576">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="58188-577">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-577">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="58188-578">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="58188-578">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="58188-579"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-579">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="58188-580">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="58188-580">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="58188-581">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="58188-581">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="58188-582">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="58188-582">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="58188-583">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-583">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="58188-584">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="58188-584">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="58188-585"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-585">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="58188-586">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="58188-586">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="58188-587">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-587">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="58188-588">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="58188-588">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="58188-589"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-589">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="58188-590">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="58188-590">Configure(IConfiguration)</span></span>

<span data-ttu-id="58188-591">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="58188-591">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="58188-592">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-592">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="58188-593">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="58188-593">ListenOptions.UseHttps</span></span>

<span data-ttu-id="58188-594">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-594">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="58188-595">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="58188-595">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="58188-596">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-596">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="58188-597">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-597">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="58188-598">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="58188-598">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="58188-599">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="58188-599">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="58188-600">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="58188-600">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="58188-601">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="58188-601">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="58188-602">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="58188-602">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="58188-603">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="58188-603">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="58188-604">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="58188-604">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="58188-605">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="58188-605">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="58188-606">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="58188-606">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="58188-607">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="58188-607">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="58188-608">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-608">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="58188-609">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-609">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="58188-610">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="58188-610">Supported configurations described next:</span></span>

* <span data-ttu-id="58188-611">構成なし</span><span class="sxs-lookup"><span data-stu-id="58188-611">No configuration</span></span>
* <span data-ttu-id="58188-612">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="58188-612">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="58188-613">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="58188-613">Change the defaults in code</span></span>

<span data-ttu-id="58188-614">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="58188-614">*No configuration*</span></span>

<span data-ttu-id="58188-615">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="58188-615">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="58188-616">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="58188-616">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="58188-617">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-617">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="58188-618">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-618">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="58188-619">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-619">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="58188-620">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="58188-620">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="58188-621">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="58188-621">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="58188-622">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="58188-622">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="58188-623">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="58188-623">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="58188-624">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-624">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="58188-625">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="58188-625">Schema notes:</span></span>

* <span data-ttu-id="58188-626">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="58188-626">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="58188-627">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="58188-627">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="58188-628">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-628">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="58188-629">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="58188-629">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="58188-630">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="58188-630">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="58188-631">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="58188-631">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="58188-632">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="58188-632">The `Certificate` section is optional.</span></span> <span data-ttu-id="58188-633">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-633">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="58188-634">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="58188-634">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="58188-635">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="58188-635">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="58188-636">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="58188-636">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="58188-637">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="58188-637">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="58188-638">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-638">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="58188-639">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-639">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="58188-640">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="58188-640">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="58188-641">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-641">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="58188-642">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="58188-642">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="58188-643">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="58188-643">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="58188-644">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-644">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="58188-645">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="58188-645">*Change the defaults in code*</span></span>

<span data-ttu-id="58188-646">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="58188-646">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="58188-647">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-647">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="58188-648">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="58188-648">*Kestrel support for SNI*</span></span>

<span data-ttu-id="58188-649">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="58188-649">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="58188-650">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="58188-650">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="58188-651">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-651">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="58188-652">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="58188-652">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="58188-653">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="58188-653">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="58188-654">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-654">SNI support requires:</span></span>

* <span data-ttu-id="58188-655">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-655">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="58188-656">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="58188-656">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="58188-657">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="58188-657">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="58188-658">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="58188-658">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="58188-659">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="58188-659">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
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

### <a name="connection-logging"></a><span data-ttu-id="58188-660">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="58188-660">Connection logging</span></span>

<span data-ttu-id="58188-661">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-661">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="58188-662">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="58188-662">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="58188-663">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="58188-663">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="58188-664">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="58188-664">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="58188-665">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="58188-665">Bind to a TCP socket</span></span>

<span data-ttu-id="58188-666"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="58188-666">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="58188-667">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="58188-667">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="58188-668">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-668">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="58188-669">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="58188-669">Bind to a Unix socket</span></span>

<span data-ttu-id="58188-670">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="58188-670">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="58188-671">ポート 0</span><span class="sxs-lookup"><span data-stu-id="58188-671">Port 0</span></span>

<span data-ttu-id="58188-672">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-672">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="58188-673">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-673">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="58188-674">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="58188-674">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="58188-675">制限事項</span><span class="sxs-lookup"><span data-stu-id="58188-675">Limitations</span></span>

<span data-ttu-id="58188-676">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-676">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="58188-677">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="58188-677">`--urls` command-line argument</span></span>
* <span data-ttu-id="58188-678">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="58188-678">`urls` host configuration key</span></span>
* <span data-ttu-id="58188-679">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="58188-679">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="58188-680">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="58188-680">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="58188-681">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="58188-681">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="58188-682">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="58188-682">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="58188-683">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-683">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="58188-684">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="58188-684">IIS endpoint configuration</span></span>

<span data-ttu-id="58188-685">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-685">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="58188-686">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-686">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="58188-687">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="58188-687">ListenOptions.Protocols</span></span>

<span data-ttu-id="58188-688">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="58188-688">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="58188-689">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="58188-689">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="58188-690">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="58188-690">`HttpProtocols` enum value</span></span> | <span data-ttu-id="58188-691">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="58188-691">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="58188-692">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="58188-692">HTTP/1.1 only.</span></span> <span data-ttu-id="58188-693">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-693">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="58188-694">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="58188-694">HTTP/2 only.</span></span> <span data-ttu-id="58188-695">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-695">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="58188-696">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="58188-696">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="58188-697">HTTP/2 には、TLS と[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続が必要です。それ以外の場合、接続は既定では HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="58188-697">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="58188-698">既定のプロトコルは HTTP/1.1 です。</span><span class="sxs-lookup"><span data-stu-id="58188-698">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="58188-699">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="58188-699">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="58188-700">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="58188-700">TLS version 1.2 or later</span></span>
* <span data-ttu-id="58188-701">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="58188-701">Renegotiation disabled</span></span>
* <span data-ttu-id="58188-702">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="58188-702">Compression disabled</span></span>
* <span data-ttu-id="58188-703">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="58188-703">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="58188-704">Elliptic curve Diffie-hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小で 224 ビット</span><span class="sxs-lookup"><span data-stu-id="58188-704">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="58188-705">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小で 2048 ビット</span><span class="sxs-lookup"><span data-stu-id="58188-705">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="58188-706">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="58188-706">Cipher suite not blacklisted</span></span>

<span data-ttu-id="58188-707">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="58188-707">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="58188-708">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="58188-708">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="58188-709">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="58188-709">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="58188-710">必要に応じて、`IConnectionAdapter` 実装を作成することで、接続ごとに特定の暗号について TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="58188-710">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
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

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
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

<span data-ttu-id="58188-711">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="58188-711">*Set the protocol from configuration*</span></span>

<span data-ttu-id="58188-712"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-712"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="58188-713">次に示す *appsettings.json* の例では、Kestrel のエンドポイントのすべてにおいて、既定の接続プロトコル (HTTP/1.1 および HTTP/2) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="58188-713">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="58188-714">次に示す構成ファイルの例では、特定のエンドポイントに対して接続プロトコルが確定されます。</span><span class="sxs-lookup"><span data-stu-id="58188-714">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="58188-715">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="58188-715">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="58188-716">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="58188-716">Transport configuration</span></span>

<span data-ttu-id="58188-717">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="58188-717">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="58188-718">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="58188-718">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="58188-719">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="58188-719">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="58188-720">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="58188-720">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="58188-721">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="58188-721">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="58188-722">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="58188-722">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="58188-723"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-723">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="58188-724">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="58188-724">URL prefixes</span></span>

<span data-ttu-id="58188-725">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="58188-725">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="58188-726">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="58188-726">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="58188-727">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="58188-727">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="58188-728">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-728">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="58188-729">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="58188-729">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="58188-730">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-730">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="58188-731">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="58188-731">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="58188-732">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-732">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="58188-733">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="58188-733">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="58188-734">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-734">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="58188-735">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-735">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="58188-736">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-736">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="58188-737">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-737">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="58188-738">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="58188-738">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="58188-739">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="58188-739">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="58188-740">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="58188-740">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="58188-741">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="58188-741">Host filtering</span></span>

<span data-ttu-id="58188-742">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="58188-742">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="58188-743">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="58188-743">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="58188-744">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-744">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="58188-745">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="58188-745">`Host` headers aren't validated.</span></span>

<span data-ttu-id="58188-746">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-746">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="58188-747">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="58188-747">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="58188-748">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="58188-748">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="58188-749">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="58188-749">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="58188-750">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="58188-750">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="58188-751">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="58188-751">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="58188-752">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="58188-752">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="58188-753">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="58188-753">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="58188-754">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="58188-754">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="58188-755">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="58188-755">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="58188-756">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="58188-756">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="58188-757">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58188-757">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="58188-758">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="58188-758">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="58188-759">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="58188-759">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="58188-760">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="58188-760">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="58188-761">HTTPS</span><span class="sxs-lookup"><span data-stu-id="58188-761">HTTPS</span></span>
* <span data-ttu-id="58188-762">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="58188-762">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="58188-763">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="58188-763">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="58188-764">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="58188-764">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="58188-765">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="58188-765">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="58188-766">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="58188-766">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="58188-767">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-767">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="58188-768">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="58188-768">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="58188-769">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58188-769">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="58188-771">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58188-771">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="58188-773">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="58188-773">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="58188-774">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="58188-774">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="58188-775">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="58188-775">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="58188-776">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-776">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="58188-777">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="58188-777">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="58188-778">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="58188-778">A reverse proxy:</span></span>

* <span data-ttu-id="58188-779">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-779">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="58188-780">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="58188-780">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="58188-781">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="58188-781">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="58188-782">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="58188-782">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="58188-783">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-783">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="58188-784">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-784">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="58188-785">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="58188-785">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="58188-786">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="58188-786">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="58188-787">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-787">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="58188-788">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="58188-788">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="58188-789">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-789">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="58188-790">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-790">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="58188-791">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="58188-791">Kestrel options</span></span>

<span data-ttu-id="58188-792">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="58188-792">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="58188-793"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-793">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="58188-794">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="58188-794">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="58188-795"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58188-795">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="58188-796">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="58188-796">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="58188-797">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="58188-797">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="58188-798">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-798">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="58188-799">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="58188-799">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="58188-800">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="58188-800">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="58188-801">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="58188-801">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="58188-802">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-802">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="58188-803">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="58188-803">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="58188-804">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-804">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="58188-805">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-805">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="58188-806">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="58188-806">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="58188-807">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-807">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="58188-808">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="58188-808">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="58188-809">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="58188-809">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="58188-810">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-810">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="58188-811">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="58188-811">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="58188-812">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="58188-812">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="58188-813">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="58188-813">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="58188-814">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="58188-814">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="58188-815">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="58188-815">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="58188-816">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="58188-816">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="58188-817">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-817">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="58188-818">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-818">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="58188-819">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-819">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="58188-820">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="58188-820">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="58188-821">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="58188-821">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="58188-822">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="58188-822">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="58188-823">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="58188-823">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="58188-824">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="58188-824">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="58188-825">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="58188-825">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="58188-826">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="58188-826">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="58188-827">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-827">A minimum rate also applies to the response.</span></span> <span data-ttu-id="58188-828">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="58188-828">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="58188-829">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-829">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="58188-830">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="58188-830">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="58188-831">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-831">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="58188-832">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="58188-832">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="58188-833">同期 IO</span><span class="sxs-lookup"><span data-stu-id="58188-833">Synchronous IO</span></span>

<span data-ttu-id="58188-834"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="58188-834"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="58188-835">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="58188-835">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="58188-836">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="58188-836">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="58188-837">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="58188-837">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="58188-838">同期 IO を無効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58188-838">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="58188-839">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-839">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="58188-840">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="58188-840">Endpoint configuration</span></span>

<span data-ttu-id="58188-841">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="58188-841">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="58188-842">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="58188-842">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="58188-843">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-843">Specify URLs using the:</span></span>

* <span data-ttu-id="58188-844">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="58188-844">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="58188-845">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="58188-845">`--urls` command-line argument.</span></span>
* <span data-ttu-id="58188-846">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="58188-846">`urls` host configuration key.</span></span>
* <span data-ttu-id="58188-847">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="58188-847">`UseUrls` extension method.</span></span>

<span data-ttu-id="58188-848">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-848">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="58188-849">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000; http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="58188-849">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="58188-850">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-850">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="58188-851">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="58188-851">A development certificate is created:</span></span>

* <span data-ttu-id="58188-852">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="58188-852">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="58188-853">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="58188-853">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="58188-854">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-854">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="58188-855">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="58188-855">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="58188-856"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-856">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="58188-857">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="58188-857">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="58188-858">`KestrelServerOptions` 構成:</span><span class="sxs-lookup"><span data-stu-id="58188-858">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="58188-859">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="58188-859">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="58188-860">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-860">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="58188-861">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="58188-861">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="58188-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-862">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="58188-863">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="58188-863">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="58188-864">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="58188-864">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="58188-865">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="58188-865">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="58188-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> を呼び出す**前に** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> を呼び出すことで作成されるエンドポイントには既定値が適用されません。</span><span class="sxs-lookup"><span data-stu-id="58188-866">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="58188-867">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="58188-867">Configure(IConfiguration)</span></span>

<span data-ttu-id="58188-868">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="58188-868">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="58188-869">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-869">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="58188-870">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="58188-870">ListenOptions.UseHttps</span></span>

<span data-ttu-id="58188-871">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-871">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="58188-872">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="58188-872">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="58188-873">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-873">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="58188-874">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="58188-874">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="58188-875">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="58188-875">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="58188-876">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="58188-876">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="58188-877">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="58188-877">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="58188-878">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="58188-878">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="58188-879">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="58188-879">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="58188-880">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="58188-880">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="58188-881">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="58188-881">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="58188-882">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="58188-882">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="58188-883">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="58188-883">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="58188-884">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="58188-884">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="58188-885">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-885">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="58188-886">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-886">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="58188-887">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="58188-887">Supported configurations described next:</span></span>

* <span data-ttu-id="58188-888">構成なし</span><span class="sxs-lookup"><span data-stu-id="58188-888">No configuration</span></span>
* <span data-ttu-id="58188-889">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="58188-889">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="58188-890">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="58188-890">Change the defaults in code</span></span>

<span data-ttu-id="58188-891">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="58188-891">*No configuration*</span></span>

<span data-ttu-id="58188-892">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="58188-892">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="58188-893">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="58188-893">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="58188-894">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="58188-894">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="58188-895">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-895">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="58188-896">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-896">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="58188-897">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="58188-897">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="58188-898">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="58188-898">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="58188-899">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、 **[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="58188-899">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="58188-900">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="58188-900">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="58188-901">たとえば、 **[証明書]**  >  **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="58188-901">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="58188-902">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="58188-902">Schema notes:</span></span>

* <span data-ttu-id="58188-903">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="58188-903">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="58188-904">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="58188-904">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="58188-905">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-905">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="58188-906">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="58188-906">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="58188-907">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="58188-907">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="58188-908">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="58188-908">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="58188-909">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="58188-909">The `Certificate` section is optional.</span></span> <span data-ttu-id="58188-910">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-910">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="58188-911">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="58188-911">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="58188-912">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="58188-912">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="58188-913">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="58188-913">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="58188-914">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="58188-914">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="58188-915">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="58188-915">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="58188-916">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="58188-916">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="58188-917">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="58188-917">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="58188-918">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-918">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="58188-919">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="58188-919">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="58188-920">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="58188-920">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="58188-921">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58188-921">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="58188-922">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="58188-922">*Change the defaults in code*</span></span>

<span data-ttu-id="58188-923">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="58188-923">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="58188-924">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-924">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="58188-925">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="58188-925">*Kestrel support for SNI*</span></span>

<span data-ttu-id="58188-926">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="58188-926">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="58188-927">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="58188-927">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="58188-928">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-928">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="58188-929">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="58188-929">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="58188-930">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="58188-930">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="58188-931">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-931">SNI support requires:</span></span>

* <span data-ttu-id="58188-932">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="58188-932">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="58188-933">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="58188-933">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="58188-934">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="58188-934">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="58188-935">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="58188-935">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="58188-936">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="58188-936">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
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

### <a name="connection-logging"></a><span data-ttu-id="58188-937">接続のログ記録</span><span class="sxs-lookup"><span data-stu-id="58188-937">Connection logging</span></span>

<span data-ttu-id="58188-938">接続でのバイト レベルの通信に対してデバッグ レベルのログを出力するには、<xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-938">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="58188-939">接続のログ記録は、TLS 暗号化の間やプロキシの内側など、低レベルの通信での問題のトラブルシューティングに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="58188-939">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="58188-940">`UseConnectionLogging` が `UseHttps` の前に配置されている場合は、暗号化されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="58188-940">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="58188-941">`UseConnectionLogging` が `UseHttps` の後に配置されている場合は、暗号化解除されたトラフィックがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="58188-941">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="58188-942">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="58188-942">Bind to a TCP socket</span></span>

<span data-ttu-id="58188-943"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="58188-943">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
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
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="58188-944">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="58188-944">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="58188-945">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-945">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="58188-946">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="58188-946">Bind to a Unix socket</span></span>

<span data-ttu-id="58188-947">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="58188-947">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

### <a name="port-0"></a><span data-ttu-id="58188-948">ポート 0</span><span class="sxs-lookup"><span data-stu-id="58188-948">Port 0</span></span>

<span data-ttu-id="58188-949">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-949">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="58188-950">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="58188-950">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="58188-951">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="58188-951">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="58188-952">制限事項</span><span class="sxs-lookup"><span data-stu-id="58188-952">Limitations</span></span>

<span data-ttu-id="58188-953">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="58188-953">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="58188-954">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="58188-954">`--urls` command-line argument</span></span>
* <span data-ttu-id="58188-955">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="58188-955">`urls` host configuration key</span></span>
* <span data-ttu-id="58188-956">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="58188-956">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="58188-957">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="58188-957">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="58188-958">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="58188-958">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="58188-959">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="58188-959">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="58188-960">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="58188-960">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="58188-961">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="58188-961">IIS endpoint configuration</span></span>

<span data-ttu-id="58188-962">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="58188-962">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="58188-963">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="58188-963">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="58188-964">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="58188-964">Transport configuration</span></span>

<span data-ttu-id="58188-965">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="58188-965">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="58188-966">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="58188-966">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="58188-967">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="58188-967">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="58188-968">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="58188-968">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="58188-969">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="58188-969">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="58188-970">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="58188-970">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="58188-971"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="58188-971">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="58188-972">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="58188-972">URL prefixes</span></span>

<span data-ttu-id="58188-973">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="58188-973">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="58188-974">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="58188-974">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="58188-975">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="58188-975">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="58188-976">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-976">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="58188-977">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="58188-977">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="58188-978">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-978">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="58188-979">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="58188-979">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="58188-980">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-980">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="58188-981">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="58188-981">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="58188-982">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-982">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="58188-983">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-983">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="58188-984">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="58188-984">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="58188-985">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="58188-985">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="58188-986">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="58188-986">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="58188-987">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="58188-987">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="58188-988">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="58188-988">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="58188-989">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="58188-989">Host filtering</span></span>

<span data-ttu-id="58188-990">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="58188-990">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="58188-991">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="58188-991">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="58188-992">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="58188-992">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="58188-993">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="58188-993">`Host` headers aren't validated.</span></span>

<span data-ttu-id="58188-994">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="58188-994">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="58188-995">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="58188-995">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="58188-996">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="58188-996">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="58188-997">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="58188-997">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="58188-998">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="58188-998">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="58188-999">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="58188-999">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="58188-1000">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="58188-1000">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="58188-1001">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="58188-1001">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="58188-1002">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="58188-1002">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="58188-1003">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="58188-1003">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="58188-1004">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="58188-1004">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="58188-1005">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58188-1005">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="58188-1006">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="58188-1006">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="58188-1007">RFC 7230: メッセージの構文と経路制御 (セクション 5.4: ホスト)</span><span class="sxs-lookup"><span data-stu-id="58188-1007">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
