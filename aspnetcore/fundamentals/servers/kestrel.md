---
title: ASP.NET Core への Kestrel Web サーバーの実装
author: guardrex
description: ASP.NET Core 用のクロスプラットフォーム Web サーバーである Kestrel について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5565011f6531ef5e95eb02f310e7107f9ed547b2
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378865"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="d552a-103">ASP.NET Core への Kestrel Web サーバーの実装</span><span class="sxs-lookup"><span data-stu-id="d552a-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="d552a-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher)、[Stephen Halter](https://twitter.com/halter73)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d552a-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d552a-105">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d552a-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d552a-106">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="d552a-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d552a-107">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d552a-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d552a-108">HTTPS</span></span>
* <span data-ttu-id="d552a-109">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="d552a-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d552a-110">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="d552a-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d552a-111">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="d552a-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d552a-112">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="d552a-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d552a-113">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d552a-114">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d552a-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d552a-115">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d552a-115">HTTP/2 support</span></span>

<span data-ttu-id="d552a-116">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d552a-117">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="d552a-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="d552a-118">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d552a-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d552a-119">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="d552a-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d552a-120">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="d552a-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d552a-121">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="d552a-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d552a-122">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d552a-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d552a-123">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="d552a-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d552a-124">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d552a-125">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="d552a-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d552a-126">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d552a-127">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d552a-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d552a-128">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="d552a-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="d552a-129">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d552a-130">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d552a-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d552a-131">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d552a-132">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="d552a-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d552a-133">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d552a-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d552a-135">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d552a-135">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d552a-137">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d552a-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d552a-138">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="d552a-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d552a-139">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="d552a-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d552a-140">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d552a-141">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d552a-142">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="d552a-142">A reverse proxy:</span></span>

* <span data-ttu-id="d552a-143">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d552a-144">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="d552a-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d552a-145">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d552a-146">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="d552a-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d552a-147">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d552a-148">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d552a-149">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="d552a-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d552a-150">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d552a-151">*Program.cs* 内のテンプレート コードは <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="d552a-152">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/generic-host#set-up-a-host> の "*ホストを設定する*" および "*既定の builder 設定*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-152">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="d552a-153">`CreateDefaultBuilder` と `ConfigureWebHostDefaults` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-153">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="d552a-154">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す**前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d552a-154">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new HostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseIISIntegration()
            .UseStartup<Startup>();
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="d552a-155">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="d552a-155">Kestrel options</span></span>

<span data-ttu-id="d552a-156">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-156">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d552a-157"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-157">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d552a-158">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="d552a-158">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d552a-159"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-159">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d552a-160">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-160">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d552a-161">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-161">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="d552a-162">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-162">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d552a-163">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="d552a-163">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d552a-164">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="d552a-164">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d552a-165">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d552a-165">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d552a-166">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-166">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="d552a-167">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="d552a-167">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d552a-168">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-168">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d552a-169">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-169">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d552a-170">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d552a-170">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d552a-171">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-171">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d552a-172">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="d552a-172">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d552a-173">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="d552a-173">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d552a-174">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-174">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d552a-175">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="d552a-175">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d552a-176">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="d552a-176">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d552a-177">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-177">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d552a-178">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-178">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d552a-179">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="d552a-179">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d552a-180">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d552a-180">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d552a-181">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-181">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d552a-182">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-182">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d552a-183">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-183">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d552a-184">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-184">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d552a-185">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="d552a-185">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d552a-186">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="d552a-186">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d552a-187">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="d552a-187">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d552a-188">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="d552a-188">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d552a-189">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-189">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d552a-190">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-190">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d552a-191">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-191">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d552a-192">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="d552a-192">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d552a-193">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-193">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="d552a-194">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-194">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d552a-195">前のサンプルで参照した <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> は、HTTP/2 要求の `HttpContext.Features` には存在しません。これは、プロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が、HTTP/2 で一般的にサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="d552a-195">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d552a-196">ただし、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> は引き続き現在の HTTP/2 要求の `HttpContext.Features` です。これは、HTTP/2 要求に対してであっても、`IHttpMinRequestBodyDataRateFeature.MinDataRate` を `null` に設定すれば、読み取りのレート制限を要求ごとに "*すべて無効*" にできるためです。</span><span class="sxs-lookup"><span data-stu-id="d552a-196">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="d552a-197">`IHttpMinRequestBodyDataRateFeature.MinDataRate` を読み取ろうとしたり、`null` 以外の値に設定しようとしたりすると、HTTP/2 要求を指定した `NotSupportedException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-197">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="d552a-198">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-198">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d552a-199">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d552a-199">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d552a-200">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-200">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d552a-201">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d552a-201">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d552a-202">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="d552a-202">Maximum streams per connection</span></span>

<span data-ttu-id="d552a-203">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-203">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d552a-204">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-204">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="d552a-205">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-205">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d552a-206">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-206">Header table size</span></span>

<span data-ttu-id="d552a-207">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-207">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d552a-208">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-208">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d552a-209">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="d552a-209">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="d552a-210">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-210">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d552a-211">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-211">Maximum frame size</span></span>

<span data-ttu-id="d552a-212">`Http2.MaxFrameSize` は、サーバーによって受信または送信された HTTP/2 接続フレーム ペイロードの最大許容サイズを示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-212">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="d552a-213">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-213">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="d552a-214">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-214">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d552a-215">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-215">Maximum request header size</span></span>

<span data-ttu-id="d552a-216">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-216">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d552a-217">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-217">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d552a-218">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-218">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="d552a-219">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-219">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d552a-220">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-220">Initial connection window size</span></span>

<span data-ttu-id="d552a-221">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-221">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d552a-222">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="d552a-222">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d552a-223">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-223">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="d552a-224">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-224">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d552a-225">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-225">Initial stream window size</span></span>

<span data-ttu-id="d552a-226">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-226">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d552a-227">要求は `Http2.InitialConnectionWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="d552a-227">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="d552a-228">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-228">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="d552a-229">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-229">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d552a-230">同期 IO</span><span class="sxs-lookup"><span data-stu-id="d552a-230">Synchronous IO</span></span>

<span data-ttu-id="d552a-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="d552a-232">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-232">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="d552a-233">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-233">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d552a-234">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="d552a-234">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="d552a-235">同期 IO を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-235">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d552a-236">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-236">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d552a-237">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="d552a-237">Endpoint configuration</span></span>

<span data-ttu-id="d552a-238">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-238">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d552a-239">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="d552a-239">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d552a-240">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-240">Specify URLs using the:</span></span>

* <span data-ttu-id="d552a-241">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="d552a-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d552a-242">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d552a-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d552a-243">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="d552a-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="d552a-244">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="d552a-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="d552a-245">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-245">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d552a-246">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="d552a-246">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d552a-247">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-247">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d552a-248">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-248">A development certificate is created:</span></span>

* <span data-ttu-id="d552a-249">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="d552a-249">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d552a-250">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="d552a-250">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d552a-251">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-251">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d552a-252">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d552a-252">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d552a-253"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-253">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d552a-254">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="d552a-254">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d552a-255">`KestrelServerOptions` 構成: </span><span class="sxs-lookup"><span data-stu-id="d552a-255">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d552a-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="d552a-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d552a-257">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-257">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d552a-258">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d552a-258">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d552a-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="d552a-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d552a-260">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d552a-261">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d552a-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="d552a-262">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="d552a-262">Configure(IConfiguration)</span></span>

<span data-ttu-id="d552a-263">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-263">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d552a-264">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-264">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d552a-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d552a-265">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d552a-266">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-266">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d552a-267">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="d552a-267">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d552a-268">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-268">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d552a-269">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-269">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="d552a-270">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="d552a-270">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d552a-271">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="d552a-271">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d552a-272">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="d552a-272">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d552a-273">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-273">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d552a-274">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="d552a-274">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d552a-275">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="d552a-275">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d552a-276">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="d552a-276">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d552a-277">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-277">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d552a-278">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="d552a-278">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d552a-279">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="d552a-279">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d552a-280">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-280">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d552a-281">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-281">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d552a-282">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="d552a-282">Supported configurations described next:</span></span>

* <span data-ttu-id="d552a-283">構成なし</span><span class="sxs-lookup"><span data-stu-id="d552a-283">No configuration</span></span>
* <span data-ttu-id="d552a-284">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="d552a-284">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d552a-285">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="d552a-285">Change the defaults in code</span></span>

<span data-ttu-id="d552a-286">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="d552a-286">*No configuration*</span></span>

<span data-ttu-id="d552a-287">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d552a-287">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d552a-288">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="d552a-288">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d552a-289">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-289">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d552a-290">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-290">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d552a-291">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-291">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d552a-292">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="d552a-292">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d552a-293">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="d552a-293">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d552a-294">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、**[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d552a-294">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="d552a-295">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="d552a-295">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d552a-296">たとえば、**[証明書]** > **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-296">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d552a-297">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="d552a-297">Schema notes:</span></span>

* <span data-ttu-id="d552a-298">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-298">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d552a-299">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-299">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d552a-300">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-300">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d552a-301">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="d552a-301">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d552a-302">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d552a-302">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d552a-303">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-303">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d552a-304">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="d552a-304">The `Certificate` section is optional.</span></span> <span data-ttu-id="d552a-305">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-305">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d552a-306">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="d552a-306">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d552a-307">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d552a-307">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d552a-308">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-308">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d552a-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="d552a-310">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-310">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d552a-311">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-311">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d552a-312">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-312">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d552a-313">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-313">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d552a-314">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="d552a-314">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d552a-315">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-315">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d552a-316">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-316">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d552a-317">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="d552a-317">*Change the defaults in code*</span></span>

<span data-ttu-id="d552a-318">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="d552a-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d552a-319">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="d552a-320">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="d552a-320">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d552a-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d552a-322">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="d552a-322">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d552a-323">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-323">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d552a-324">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d552a-324">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d552a-325">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-325">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d552a-326">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-326">SNI support requires:</span></span>

* <span data-ttu-id="d552a-327">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-327">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d552a-328">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-328">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d552a-329">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-329">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d552a-330">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-330">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d552a-331">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="d552a-331">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d552a-332">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="d552a-332">Bind to a TCP socket</span></span>

<span data-ttu-id="d552a-333"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-333">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="d552a-334">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-334">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d552a-335">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-335">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d552a-336">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="d552a-336">Bind to a Unix socket</span></span>

<span data-ttu-id="d552a-337">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d552a-337">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="d552a-338">ポート 0</span><span class="sxs-lookup"><span data-stu-id="d552a-338">Port 0</span></span>

<span data-ttu-id="d552a-339">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-339">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d552a-340">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-340">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d552a-341">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-341">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d552a-342">制限事項</span><span class="sxs-lookup"><span data-stu-id="d552a-342">Limitations</span></span>

<span data-ttu-id="d552a-343">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-343">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d552a-344">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d552a-344">`--urls` command-line argument</span></span>
* <span data-ttu-id="d552a-345">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="d552a-345">`urls` host configuration key</span></span>
* <span data-ttu-id="d552a-346">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d552a-346">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d552a-347">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="d552a-347">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d552a-348">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-348">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d552a-349">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d552a-349">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d552a-350">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-350">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d552a-351">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="d552a-351">IIS endpoint configuration</span></span>

<span data-ttu-id="d552a-352">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-352">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d552a-353">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-353">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d552a-354">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="d552a-354">ListenOptions.Protocols</span></span>

<span data-ttu-id="d552a-355">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-355">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d552a-356">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d552a-356">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d552a-357">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="d552a-357">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d552a-358">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="d552a-358">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d552a-359">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="d552a-359">HTTP/1.1 only.</span></span> <span data-ttu-id="d552a-360">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-360">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d552a-361">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="d552a-361">HTTP/2 only.</span></span> <span data-ttu-id="d552a-362">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-362">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d552a-363">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d552a-363">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d552a-364">HTTP/2 では、クライアントが TLS [アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ハンドシェイクで HTTP/2 を選択する必要があります。それ以外の場合、接続は既定で HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="d552a-364">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d552a-365">任意のエンドポイントに対する `ListenOptions.Protocols` の既定値は `HttpProtocols.Http1AndHttp2` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-365">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="d552a-366">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="d552a-366">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d552a-367">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="d552a-367">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d552a-368">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="d552a-368">Renegotiation disabled</span></span>
* <span data-ttu-id="d552a-369">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="d552a-369">Compression disabled</span></span>
* <span data-ttu-id="d552a-370">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="d552a-370">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d552a-371">Elliptic curve Diffie-hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小で 224 ビット</span><span class="sxs-lookup"><span data-stu-id="d552a-371">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="d552a-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小で 2048 ビット</span><span class="sxs-lookup"><span data-stu-id="d552a-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="d552a-373">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="d552a-373">Cipher suite not blacklisted</span></span>

<span data-ttu-id="d552a-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d552a-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d552a-375">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-375">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d552a-376">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-376">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="d552a-377">必要に応じて、接続ミドルウェアを使用し、特定の暗号のために接続ごとに TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="d552a-377">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="d552a-378">次の例では、アプリでサポートされていないすべての暗号アルゴリズムに対して <xref:System.NotSupportedException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-378">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="d552a-379">または、[ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) を定義して、指定できる暗号スイートの一覧と比較します。</span><span class="sxs-lookup"><span data-stu-id="d552a-379">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="d552a-380">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 暗号アルゴリズムでは、暗号化は使用されません。</span><span class="sxs-lookup"><span data-stu-id="d552a-380">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="d552a-381">接続のフィルター処理は、<xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> のラムダを使って構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-381">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="d552a-382">Linux では、<xref:System.Net.Security.CipherSuitesPolicy> を使って、接続ごとに TLS ハンドシェイクをフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-382">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="d552a-383">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="d552a-383">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d552a-384">`CreateDefaultBuilder` は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-384">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d552a-385">次の *appsettings.json* の例では、すべてのエンドポイントにおける既定の接続プロトコルとして HTTP/1.1 を確立しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-385">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="d552a-386">次の *appsettings.json* の例では、特定のエンドポイントにおける HTTP/1.1 接続プロトコルを確立しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-386">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="d552a-387">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-387">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="d552a-388">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="d552a-388">Transport configuration</span></span>

<span data-ttu-id="d552a-389">Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="d552a-389">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="d552a-390">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="d552a-390">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="d552a-391">`IWebHostBuilder` で <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す</span><span class="sxs-lookup"><span data-stu-id="d552a-391">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="d552a-392">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="d552a-392">URL prefixes</span></span>

<span data-ttu-id="d552a-393">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="d552a-393">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d552a-394">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-394">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d552a-395">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="d552a-395">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d552a-396">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-396">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d552a-397">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="d552a-397">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d552a-398">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-398">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d552a-399">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="d552a-399">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d552a-400">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-400">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d552a-401">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="d552a-401">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d552a-402">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-402">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d552a-403">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-403">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d552a-404">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-404">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d552a-405">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-405">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d552a-406">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-406">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d552a-407">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="d552a-407">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d552a-408">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d552a-408">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d552a-409">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="d552a-409">Host filtering</span></span>

<span data-ttu-id="d552a-410">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="d552a-410">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d552a-411">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="d552a-411">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d552a-412">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-412">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d552a-413">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="d552a-413">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d552a-414">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-414">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d552a-415">Host Filtering Middleware は、ASP.NET Core アプリに暗黙的に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-415">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="d552a-416">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-416">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d552a-417">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-417">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d552a-418">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="d552a-418">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d552a-419">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="d552a-419">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d552a-420">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d552a-420">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d552a-421">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-421">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d552a-422">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-422">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d552a-423">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="d552a-423">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d552a-424">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="d552a-424">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d552a-425">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-425">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d552a-426">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d552a-426">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d552a-427">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="d552a-427">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d552a-428">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-428">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d552a-429">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d552a-429">HTTPS</span></span>
* <span data-ttu-id="d552a-430">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="d552a-430">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d552a-431">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="d552a-431">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d552a-432">HTTP/2 (macOS の場合を除く&dagger;)</span><span class="sxs-lookup"><span data-stu-id="d552a-432">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d552a-433">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="d552a-433">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d552a-434">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-434">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d552a-435">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d552a-435">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d552a-436">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="d552a-436">HTTP/2 support</span></span>

<span data-ttu-id="d552a-437">[Http/2](https://httpwg.org/specs/rfc7540.html) は、次の基本要件が満たされている場合に、ASP.NET Core アプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-437">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d552a-438">オペレーティング システム&dagger;</span><span class="sxs-lookup"><span data-stu-id="d552a-438">Operating system&dagger;</span></span>
  * <span data-ttu-id="d552a-439">Windows Server 2016/Windows 10 以降&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d552a-439">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d552a-440">OpenSSL 1.0.2 以降を使用した Linux (Ubuntu 16.04 以降など)</span><span class="sxs-lookup"><span data-stu-id="d552a-440">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d552a-441">ターゲット フレームワーク: .NET Core 2.2 以降</span><span class="sxs-lookup"><span data-stu-id="d552a-441">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d552a-442">[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続</span><span class="sxs-lookup"><span data-stu-id="d552a-442">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d552a-443">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="d552a-443">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d552a-444">&dagger;将来のリリースでは HTTP/2 が macOS 上でサポートされるようになります。</span><span class="sxs-lookup"><span data-stu-id="d552a-444">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d552a-445">&Dagger;Kestrel では、Windows Server 2012 R2 および Windows 8.1 上での HTTP/2 のサポートは制限されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-445">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d552a-446">サポートが制限されている理由は、これらのオペレーティング システムで使用できる TLS 暗号のスイートのリストが制限されているためです。</span><span class="sxs-lookup"><span data-stu-id="d552a-446">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d552a-447">TLS 接続をセキュリティで保護するためには、楕円曲線デジタル署名アルゴリズム (ECDSA) を使用して生成した証明書が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-447">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d552a-448">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/2` を報告します。</span><span class="sxs-lookup"><span data-stu-id="d552a-448">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d552a-449">Http/2 は既定では無効になっています。</span><span class="sxs-lookup"><span data-stu-id="d552a-449">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="d552a-450">構成の詳細については、「[Kestrel オプション](#kestrel-options)」セクションと「[ListenOptions.Protocols](#listenoptionsprotocols)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-450">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d552a-451">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d552a-451">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d552a-452">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-452">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d552a-453">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="d552a-453">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d552a-454">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d552a-454">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d552a-456">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d552a-456">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d552a-458">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d552a-458">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d552a-459">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="d552a-459">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d552a-460">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="d552a-460">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d552a-461">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-461">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d552a-462">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-462">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d552a-463">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="d552a-463">A reverse proxy:</span></span>

* <span data-ttu-id="d552a-464">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-464">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d552a-465">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="d552a-465">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d552a-466">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-466">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d552a-467">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="d552a-467">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d552a-468">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-468">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d552a-469">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-469">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d552a-470">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="d552a-470">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d552a-471">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d552a-471">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d552a-472">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-472">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d552a-473">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-473">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="d552a-474">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-474">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="d552a-475">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、`ConfigureKestrel` を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-475">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d552a-476">アプリでホストを設定するための `CreateDefaultBuilder` を呼び出さない場合は、`ConfigureKestrel` を呼び出す**前に** <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d552a-476">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="d552a-477">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="d552a-477">Kestrel options</span></span>

<span data-ttu-id="d552a-478">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-478">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d552a-479"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-479">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d552a-480">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="d552a-480">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d552a-481"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-481">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d552a-482">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-482">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d552a-483">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-483">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="d552a-484">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-484">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d552a-485">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="d552a-485">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d552a-486">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="d552a-486">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d552a-487">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d552a-487">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d552a-488">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-488">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="d552a-489">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="d552a-489">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d552a-490">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-490">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d552a-491">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-491">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d552a-492">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d552a-492">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d552a-493">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-493">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d552a-494">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="d552a-494">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d552a-495">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="d552a-495">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d552a-496">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-496">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d552a-497">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="d552a-497">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d552a-498">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="d552a-498">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d552a-499">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-499">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d552a-500">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-500">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d552a-501">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="d552a-501">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d552a-502">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d552a-502">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d552a-503">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-503">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d552a-504">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-504">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d552a-505">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-505">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d552a-506">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-506">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d552a-507">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="d552a-507">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d552a-508">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="d552a-508">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d552a-509">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="d552a-509">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d552a-510">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="d552a-510">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d552a-511">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-511">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d552a-512">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-512">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d552a-513">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-513">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d552a-514">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="d552a-514">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d552a-515">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-515">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="d552a-516">ミドルウェアでは要求ごとに最小レート制限をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-516">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d552a-517">前のサンプルで参照したどのレート機能も HTTP/2 要求の `HttpContext.Features` には存在しません。これはプロトコルで要求の多重化に対応するために、要求ごとのレート制限の変更が HTTP/2 でサポートされていないからです。</span><span class="sxs-lookup"><span data-stu-id="d552a-517">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d552a-518">`KestrelServerOptions.Limits` で構成したサーバー全体のレート制限は、引き続き HTTP/1.x と HTTP/2 の両方の接続に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-518">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d552a-519">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d552a-519">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d552a-520">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-520">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d552a-521">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d552a-521">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d552a-522">接続ごとの最大ストリーム</span><span class="sxs-lookup"><span data-stu-id="d552a-522">Maximum streams per connection</span></span>

<span data-ttu-id="d552a-523">HTTP/2 接続ごとの同時要求ストリームの数は、`Http2.MaxStreamsPerConnection` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-523">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d552a-524">余分なストリームは拒否されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-524">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="d552a-525">既定値は 100 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-525">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d552a-526">ヘッダー テーブルのサイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-526">Header table size</span></span>

<span data-ttu-id="d552a-527">HTTP/2 接続では、HTTP ヘッダーは HPACK デコーダーによって圧縮解除されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-527">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d552a-528">HPACK デコーダーが使用するヘッダー圧縮テーブルのサイズは `Http2.HeaderTableSize` によって制限されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-528">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d552a-529">値はオクテット単位で指定し、ゼロ (0) より大きくなければなりません。</span><span class="sxs-lookup"><span data-stu-id="d552a-529">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="d552a-530">既定値は 4096 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-530">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d552a-531">最大フレーム サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-531">Maximum frame size</span></span>

<span data-ttu-id="d552a-532">受信する HTTP/2 接続フレーム ペイロードの最大サイズは、`Http2.MaxFrameSize` によって示されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-532">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="d552a-533">値はオクテット単位で指定し、2^14 (16,384) から 2^24-1 (16,777,215) までの範囲とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-533">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="d552a-534">既定値は 2^14 (16,384) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-534">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d552a-535">最大要求ヘッダー サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-535">Maximum request header size</span></span>

<span data-ttu-id="d552a-536">`Http2.MaxRequestHeaderFieldSize` は、要求ヘッダー値の最大許容サイズをオクテット単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-536">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d552a-537">この制限は、名前と値の圧縮表示と非圧縮表示の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-537">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d552a-538">ゼロ (0) より大きい値である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-538">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="d552a-539">既定値は 8,192 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-539">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d552a-540">初期接続ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-540">Initial connection window size</span></span>

<span data-ttu-id="d552a-541">`Http2.InitialConnectionWindowSize` は、サーバーが接続ごとの要求 (ストリーム) 全体で一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-541">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d552a-542">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="d552a-542">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d552a-543">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-543">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="d552a-544">既定値は 128 KB (131,072) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-544">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d552a-545">初期ストリーム ウィンドウ サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-545">Initial stream window size</span></span>

<span data-ttu-id="d552a-546">`Http2.InitialStreamWindowSize` は、サーバーが要求 (ストリーム) ごとに一度にバッファする最大要求本文データをバイト単位で示しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-546">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d552a-547">要求は `Http2.InitialStreamWindowSize` による制限も受けます。</span><span class="sxs-lookup"><span data-stu-id="d552a-547">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d552a-548">この値は 65,535 より大きく、2^31 (2,147,483,648) 未満である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-548">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="d552a-549">既定値は 96 KB (98,304) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-549">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d552a-550">同期 IO</span><span class="sxs-lookup"><span data-stu-id="d552a-550">Synchronous IO</span></span>

<span data-ttu-id="d552a-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="d552a-552">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-552">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d552a-553">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-553">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d552a-554">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="d552a-554">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="d552a-555">同期 IO を有効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-555">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d552a-556">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-556">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d552a-557">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="d552a-557">Endpoint configuration</span></span>

<span data-ttu-id="d552a-558">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-558">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d552a-559">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="d552a-559">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d552a-560">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-560">Specify URLs using the:</span></span>

* <span data-ttu-id="d552a-561">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="d552a-561">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d552a-562">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d552a-562">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d552a-563">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="d552a-563">`urls` host configuration key.</span></span>
* <span data-ttu-id="d552a-564">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="d552a-564">`UseUrls` extension method.</span></span>

<span data-ttu-id="d552a-565">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-565">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d552a-566">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="d552a-566">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d552a-567">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-567">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d552a-568">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-568">A development certificate is created:</span></span>

* <span data-ttu-id="d552a-569">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="d552a-569">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d552a-570">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="d552a-570">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d552a-571">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-571">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d552a-572">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d552a-572">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d552a-573"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-573">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d552a-574">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="d552a-574">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d552a-575">`KestrelServerOptions` 構成: </span><span class="sxs-lookup"><span data-stu-id="d552a-575">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d552a-576">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="d552a-576">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d552a-577">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-577">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d552a-578">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d552a-578">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d552a-579">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="d552a-579">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d552a-580">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-580">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d552a-581">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d552a-581">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="d552a-582">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="d552a-582">Configure(IConfiguration)</span></span>

<span data-ttu-id="d552a-583">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-583">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d552a-584">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-584">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d552a-585">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d552a-585">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d552a-586">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-586">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d552a-587">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="d552a-587">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d552a-588">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-588">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d552a-589">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-589">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="d552a-590">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="d552a-590">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d552a-591">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="d552a-591">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d552a-592">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="d552a-592">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d552a-593">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-593">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d552a-594">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="d552a-594">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d552a-595">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="d552a-595">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d552a-596">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="d552a-596">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d552a-597">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-597">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d552a-598">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="d552a-598">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d552a-599">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="d552a-599">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d552a-600">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-600">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d552a-601">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-601">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d552a-602">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="d552a-602">Supported configurations described next:</span></span>

* <span data-ttu-id="d552a-603">構成なし</span><span class="sxs-lookup"><span data-stu-id="d552a-603">No configuration</span></span>
* <span data-ttu-id="d552a-604">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="d552a-604">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d552a-605">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="d552a-605">Change the defaults in code</span></span>

<span data-ttu-id="d552a-606">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="d552a-606">*No configuration*</span></span>

<span data-ttu-id="d552a-607">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d552a-607">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d552a-608">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="d552a-608">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d552a-609">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-609">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d552a-610">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-610">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d552a-611">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-611">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d552a-612">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="d552a-612">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d552a-613">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="d552a-613">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d552a-614">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、**[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d552a-614">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="d552a-615">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="d552a-615">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d552a-616">たとえば、**[証明書]** > **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-616">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d552a-617">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="d552a-617">Schema notes:</span></span>

* <span data-ttu-id="d552a-618">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-618">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d552a-619">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-619">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d552a-620">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-620">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d552a-621">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="d552a-621">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d552a-622">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d552a-622">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d552a-623">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-623">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d552a-624">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="d552a-624">The `Certificate` section is optional.</span></span> <span data-ttu-id="d552a-625">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-625">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d552a-626">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="d552a-626">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d552a-627">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d552a-627">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d552a-628">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-628">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d552a-629">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-629">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="d552a-630">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-630">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d552a-631">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-631">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d552a-632">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-632">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d552a-633">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-633">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d552a-634">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="d552a-634">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d552a-635">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-635">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d552a-636">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-636">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d552a-637">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="d552a-637">*Change the defaults in code*</span></span>

<span data-ttu-id="d552a-638">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="d552a-638">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d552a-639">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-639">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="d552a-640">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="d552a-640">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d552a-641">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-641">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d552a-642">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="d552a-642">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d552a-643">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-643">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d552a-644">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d552a-644">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d552a-645">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-645">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d552a-646">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-646">SNI support requires:</span></span>

* <span data-ttu-id="d552a-647">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-647">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d552a-648">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-648">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d552a-649">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-649">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d552a-650">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-650">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d552a-651">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="d552a-651">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d552a-652">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="d552a-652">Bind to a TCP socket</span></span>

<span data-ttu-id="d552a-653"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-653">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="d552a-654">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-654">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d552a-655">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-655">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d552a-656">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="d552a-656">Bind to a Unix socket</span></span>

<span data-ttu-id="d552a-657">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d552a-657">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="d552a-658">ポート 0</span><span class="sxs-lookup"><span data-stu-id="d552a-658">Port 0</span></span>

<span data-ttu-id="d552a-659">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-659">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d552a-660">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-660">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d552a-661">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-661">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d552a-662">制限事項</span><span class="sxs-lookup"><span data-stu-id="d552a-662">Limitations</span></span>

<span data-ttu-id="d552a-663">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-663">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d552a-664">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d552a-664">`--urls` command-line argument</span></span>
* <span data-ttu-id="d552a-665">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="d552a-665">`urls` host configuration key</span></span>
* <span data-ttu-id="d552a-666">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d552a-666">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d552a-667">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="d552a-667">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d552a-668">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-668">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d552a-669">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d552a-669">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d552a-670">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-670">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d552a-671">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="d552a-671">IIS endpoint configuration</span></span>

<span data-ttu-id="d552a-672">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-672">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d552a-673">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-673">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d552a-674">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="d552a-674">ListenOptions.Protocols</span></span>

<span data-ttu-id="d552a-675">`Protocols`プロパティでは、接続エンドポイントまたはサーバーに対して有効にされる HTTP プロトコル (`HttpProtocols`) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-675">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d552a-676">`HttpProtocols` 列挙型から `Protocols` プロパティに値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d552a-676">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d552a-677">`HttpProtocols` 列挙値</span><span class="sxs-lookup"><span data-stu-id="d552a-677">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d552a-678">許可される接続プロトコル</span><span class="sxs-lookup"><span data-stu-id="d552a-678">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d552a-679">HTTP/1.1 のみ。</span><span class="sxs-lookup"><span data-stu-id="d552a-679">HTTP/1.1 only.</span></span> <span data-ttu-id="d552a-680">TLS の有無にかかわらず使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-680">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d552a-681">HTTP/2 のみ。</span><span class="sxs-lookup"><span data-stu-id="d552a-681">HTTP/2 only.</span></span> <span data-ttu-id="d552a-682">[予備知識モード](https://tools.ietf.org/html/rfc7540#section-3.4)がクライアントでサポートされている場合に限り、TLS なしで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-682">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d552a-683">HTTP/1.1 および HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="d552a-683">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d552a-684">HTTP/2 には、TLS と[アプリケーション レイヤー プロトコル ネゴシエーション (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 接続が必要です。それ以外の場合、接続は既定では HTTP/1.1 となります。</span><span class="sxs-lookup"><span data-stu-id="d552a-684">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d552a-685">既定のプロトコルは HTTP/1.1 です。</span><span class="sxs-lookup"><span data-stu-id="d552a-685">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="d552a-686">HTTP/2 に対する TLS 制限事項:</span><span class="sxs-lookup"><span data-stu-id="d552a-686">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d552a-687">TLS バージョン 1.2 以降</span><span class="sxs-lookup"><span data-stu-id="d552a-687">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d552a-688">再ネゴシエーションは無効</span><span class="sxs-lookup"><span data-stu-id="d552a-688">Renegotiation disabled</span></span>
* <span data-ttu-id="d552a-689">圧縮は無効</span><span class="sxs-lookup"><span data-stu-id="d552a-689">Compression disabled</span></span>
* <span data-ttu-id="d552a-690">短期キー交換サイズの上限:</span><span class="sxs-lookup"><span data-stu-id="d552a-690">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d552a-691">Elliptic curve Diffie-hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 最小で 224 ビット</span><span class="sxs-lookup"><span data-stu-id="d552a-691">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="d552a-692">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 最小で 2048 ビット</span><span class="sxs-lookup"><span data-stu-id="d552a-692">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="d552a-693">暗号スイートはブラック リストに登録されない</span><span class="sxs-lookup"><span data-stu-id="d552a-693">Cipher suite not blacklisted</span></span>

<span data-ttu-id="d552a-694">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; と P-256 elliptic curve &lbrack;`FIPS186`&rbrack; の組み合わせは既定ではサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d552a-694">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d552a-695">次の例では、HTTP/1.1 接続と HTTP/2 接続がポート 8000 上で許可されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-695">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d552a-696">接続は、TLS と指定した証明書によってセキュリティで保護されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-696">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="d552a-697">必要に応じて、`IConnectionAdapter` 実装を作成することで、接続ごとに特定の暗号について TLS ハンドシェイクをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="d552a-697">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="d552a-698">*構成からのプロトコルを設定する*</span><span class="sxs-lookup"><span data-stu-id="d552a-698">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d552a-699"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> は既定で `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-699"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d552a-700">次に示す *appsettings.json* の例では、Kestrel のエンドポイントのすべてにおいて、既定の接続プロトコル (HTTP/1.1 および HTTP/2) が確定されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-700">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="d552a-701">次に示す構成ファイルの例では、特定のエンドポイントに対して接続プロトコルが確定されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-701">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="d552a-702">構成で設定された値は、コード内で指定されたプロトコルによってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-702">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="d552a-703">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="d552a-703">Transport configuration</span></span>

<span data-ttu-id="d552a-704">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="d552a-704">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d552a-705">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-705">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d552a-706">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="d552a-706">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d552a-707">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d552a-707">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d552a-708">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="d552a-708">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d552a-709">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="d552a-709">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d552a-710"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d552a-710">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="d552a-711">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="d552a-711">URL prefixes</span></span>

<span data-ttu-id="d552a-712">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="d552a-712">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d552a-713">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-713">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d552a-714">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="d552a-714">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d552a-715">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-715">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d552a-716">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="d552a-716">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d552a-717">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-717">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d552a-718">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="d552a-718">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d552a-719">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-719">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d552a-720">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="d552a-720">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d552a-721">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-721">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d552a-722">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-722">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d552a-723">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-723">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d552a-724">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-724">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d552a-725">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-725">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d552a-726">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="d552a-726">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d552a-727">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d552a-727">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d552a-728">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="d552a-728">Host filtering</span></span>

<span data-ttu-id="d552a-729">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="d552a-729">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d552a-730">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="d552a-730">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d552a-731">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-731">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d552a-732">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="d552a-732">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d552a-733">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-733">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d552a-734">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-734">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d552a-735">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-735">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d552a-736">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-736">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d552a-737">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="d552a-737">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d552a-738">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="d552a-738">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d552a-739">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d552a-739">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d552a-740">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-740">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d552a-741">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-741">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d552a-742">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="d552a-742">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d552a-743">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="d552a-743">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d552a-744">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-744">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d552a-745">Kestrel は、[ASP.NET Core 向けのクロスプラットフォーム Web サーバー](xref:fundamentals/servers/index)です。</span><span class="sxs-lookup"><span data-stu-id="d552a-745">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d552a-746">Kestrel は、ASP.NET Core のプロジェクト テンプレートに既定で含まれる Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="d552a-746">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d552a-747">Kestrel では、次のシナリオがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-747">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d552a-748">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d552a-748">HTTPS</span></span>
* <span data-ttu-id="d552a-749">[Websocket](https://github.com/aspnet/websockets) を有効にするために使用される非透過的なアップグレード</span><span class="sxs-lookup"><span data-stu-id="d552a-749">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d552a-750">Nginx の背後にある高パフォーマンスの UNIX ソケット</span><span class="sxs-lookup"><span data-stu-id="d552a-750">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="d552a-751">kestrel は、.NET Core がサポートするすべてのプラットフォームおよびバージョンでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-751">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d552a-752">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d552a-752">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d552a-753">Kestrel とリバース プロキシを使用するタイミング</span><span class="sxs-lookup"><span data-stu-id="d552a-753">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d552a-754">Kestrel を単独で使用することも、[インターネット インフォメーション サービス (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org)、[Apache](https://httpd.apache.org/) などの*リバース プロキシ サーバー*と併用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-754">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d552a-755">リバース プロキシ サーバーはネットワークから HTTP 要求を受け取り、これを Kestrel に転送します。</span><span class="sxs-lookup"><span data-stu-id="d552a-755">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d552a-756">エッジ (インターネットに接続する) Web サーバーとして使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d552a-756">Kestrel used as an edge (Internet-facing) web server:</span></span>

![リバース プロキシ サーバーなしでインターネットと直接通信する Kestrel](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d552a-758">リバース プロキシ構成で使用される Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d552a-758">Kestrel used in a reverse proxy configuration:</span></span>

![IIS、Nginx、または Apache などのリバース プロキシ サーバーを介してインターネットと間接的に通信する Kestrel](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d552a-760">いずれの構成でも、リバース プロキシ サーバーの有無に関わらず、ホスティング構成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d552a-760">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d552a-761">リバース プロキシ サーバーのないエッジ サーバーとして使用される Kestrel は、複数のプロセス間で同じ IP とポートを共有することをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="d552a-761">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d552a-762">あるポートをリッスンするように Kestrel を構成すると、Kestrel は、要求の `Host` ヘッダーに関係なく、そのポートに対するすべてのトラフィックを処理します。</span><span class="sxs-lookup"><span data-stu-id="d552a-762">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d552a-763">ポートを共有できるリバース プロキシは、一意の IP とポート上の Kestrel に要求を転送することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-763">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d552a-764">リバース プロキシ サーバーが必要ない場合であっても、リバース プロキシ サーバーを使用すると次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-764">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d552a-765">リバース プロキシ:</span><span class="sxs-lookup"><span data-stu-id="d552a-765">A reverse proxy:</span></span>

* <span data-ttu-id="d552a-766">ホストするアプリの公開されるパブリック サーフェス領域を制限することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-766">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d552a-767">構成および防御の層を追加します。</span><span class="sxs-lookup"><span data-stu-id="d552a-767">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d552a-768">既存のインフラストラクチャとより適切に統合できる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-768">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d552a-769">負荷分散とセキュリティで保護された通信 (HTTPS) の構成を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="d552a-769">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d552a-770">リバース プロキシ サーバーのみで X.509 証明書が必要です。このサーバーでは、プレーンな HTTP を使用して内部ネットワーク上のアプリのサーバーと通信することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-770">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d552a-771">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-771">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d552a-772">ASP.NET Core アプリで Kestrel を使用する方法</span><span class="sxs-lookup"><span data-stu-id="d552a-772">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d552a-773">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="d552a-773">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d552a-774">ASP.NET Core プロジェクト テンプレートは既定では Kestrel を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-774">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d552a-775">*Program.cs* 内のテンプレート コードは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出し、これによってバックグラウンドで <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-775">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="d552a-776">`CreateDefaultBuilder` を呼び出した後に追加の構成を指定するには、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d552a-776">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d552a-777">`CreateDefaultBuilder` とホストのビルドについて詳しくは、<xref:fundamentals/host/web-host#set-up-a-host> の "*ホストを設定する*" セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-777">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="d552a-778">Kestrel オプション</span><span class="sxs-lookup"><span data-stu-id="d552a-778">Kestrel options</span></span>

<span data-ttu-id="d552a-779">Kestrel Web サーバーには、インターネットに接続する展開で特に有効な制約構成オプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-779">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d552a-780"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> クラスの <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> プロパティで制約を設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-780">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d552a-781">`Limits` プロパティは、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> クラスのインスタンスを保持します。</span><span class="sxs-lookup"><span data-stu-id="d552a-781">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d552a-782"><xref:Microsoft.AspNetCore.Server.Kestrel.Core> 名前空間を使用する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-782">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d552a-783">次の例の C# コード内で構成されている Kestrel オプションは、[構成プロバイダー](xref:fundamentals/configuration/index)を使って設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-783">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d552a-784">たとえば、ファイル構成プロバイダーによって、*appsettings.json* または "*appsettings.{環境}.json*" ファイルから Kestrel 構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-784">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="d552a-785">次の方法の**いずれか**を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-785">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d552a-786">`Startup.ConfigureServices` で Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="d552a-786">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d552a-787">`IConfiguration` のインスタンスを `Startup` クラスに挿入します。</span><span class="sxs-lookup"><span data-stu-id="d552a-787">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d552a-788">以下の例では、挿入された構成が `Configuration` プロパティに割り当てられることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="d552a-788">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d552a-789">`Startup.ConfigureServices` で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-789">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="d552a-790">ホストのビルド時に Kestrel を構成する。</span><span class="sxs-lookup"><span data-stu-id="d552a-790">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d552a-791">*Program.cs* で、構成の `Kestrel` セクションを Kestrel の構成に読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-791">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d552a-792">上記の方法はいずれも、任意の[構成プロバイダー](xref:fundamentals/configuration/index)で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-792">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d552a-793">Keep-Alive タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d552a-793">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d552a-794">[Keep-Alive タイムアウト](https://tools.ietf.org/html/rfc7230#section-6.5)を取得するか、設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-794">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d552a-795">既定値は 2 分です。</span><span class="sxs-lookup"><span data-stu-id="d552a-795">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="d552a-796">クライアントの最大接続数</span><span class="sxs-lookup"><span data-stu-id="d552a-796">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d552a-797">次のコードを使用することで、アプリ全体に対して同時に開かれる TCP 接続の最大数を設定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-797">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="d552a-798">HTTP または HTTPS から別のプロトコルにアップグレードされた接続については別個の制限があります (WebSockets 要求に関する制限など)。</span><span class="sxs-lookup"><span data-stu-id="d552a-798">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d552a-799">接続がアップグレードされた後、それは `MaxConcurrentConnections` 制限に対してカウントされません。</span><span class="sxs-lookup"><span data-stu-id="d552a-799">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="d552a-800">接続の最大数は既定では無制限 (null) です。</span><span class="sxs-lookup"><span data-stu-id="d552a-800">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d552a-801">要求本文の最大サイズ</span><span class="sxs-lookup"><span data-stu-id="d552a-801">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d552a-802">既定の要求本文の最大サイズは、30,000,000 バイトです。これは約 28.6 MB になります。</span><span class="sxs-lookup"><span data-stu-id="d552a-802">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d552a-803">ASP.NET Core MVC アプリでの制限をオーバーライドする方法としては、アクション メソッドに対して <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d552a-803">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d552a-804">次の例では、すべての要求についての制約をアプリに対して構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-804">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="d552a-805">ミドルウェア内の特定の要求で設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-805">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d552a-806">アプリが要求の読み取りを開始した後で、要求に対する制限をアプリで構成すると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-806">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d552a-807">`MaxRequestBodySize` プロパティが読み取り専用状態にある (制限を構成するには遅すぎる) かどうかを示す `IsReadOnly` プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-807">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d552a-808">[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) の背後でアプリが[アウト プロセス](xref:host-and-deploy/iis/index#out-of-process-hosting-model)で実行されるとき、Kestrel の要求本文サイズ上限が無効になります。IIS により既に上限が設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="d552a-808">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d552a-809">要求本文の最小レート</span><span class="sxs-lookup"><span data-stu-id="d552a-809">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d552a-810">kestrel はデータが指定のレート (バイト数/秒) で到着しているかどうかを毎秒チェックします。</span><span class="sxs-lookup"><span data-stu-id="d552a-810">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d552a-811">レートが最小値を下回った場合は接続がタイムアウトになります。猶予期間とは、クライアントによって送信速度を最低ラインまで引き上げられるのを、Kestrel が待機する時間のことです。この期間中、レートはチェックされません。</span><span class="sxs-lookup"><span data-stu-id="d552a-811">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d552a-812">猶予期間により、TCP のスロースタートのため最初にデータを低速で送信する接続がドロップされるのを回避できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-812">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d552a-813">既定の最小レートは 240 バイト/秒であり、5 秒の猶予時間が設定されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-813">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d552a-814">最小レートは応答にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-814">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d552a-815">要求制限と応答制限を設定するコードは、プロパティ名およびインターフェイス名に `RequestBody` または `Response` が使用されることを除けば同じです。</span><span class="sxs-lookup"><span data-stu-id="d552a-815">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d552a-816">次の例では、*Program.cs* で最小データ レートを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-816">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="d552a-817">要求ヘッダー タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d552a-817">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d552a-818">サーバーで要求ヘッダーの受信にかかる時間の最大値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-818">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d552a-819">既定値は 30 秒です。</span><span class="sxs-lookup"><span data-stu-id="d552a-819">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="d552a-820">同期 IO</span><span class="sxs-lookup"><span data-stu-id="d552a-820">Synchronous IO</span></span>

<span data-ttu-id="d552a-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> を使うと、要求と応答に対して同期 IO を許可するかどうかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="d552a-822">既定値は `true` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-822">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d552a-823">ブロッキング同期 IO 操作の回数が多いと、スレッド プールの不足を招き、アプリが応答しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-823">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d552a-824">非同期 IO をサポートしていないライブラリを使用する場合にのみ `AllowSynchronousIO` を有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="d552a-824">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="d552a-825">同期 IO を無効にする例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-825">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="d552a-826">Kestrel のその他のオプションと制限については、以下をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-826">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d552a-827">エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="d552a-827">Endpoint configuration</span></span>

<span data-ttu-id="d552a-828">既定では、ASP.NET Core は以下にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-828">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d552a-829">`https://localhost:5001` (ローカル開発証明書が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="d552a-829">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d552a-830">以下を使用して URL を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-830">Specify URLs using the:</span></span>

* <span data-ttu-id="d552a-831">`ASPNETCORE_URLS` 環境変数。</span><span class="sxs-lookup"><span data-stu-id="d552a-831">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d552a-832">`--urls` コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d552a-832">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d552a-833">`urls` ホスト構成キー。</span><span class="sxs-lookup"><span data-stu-id="d552a-833">`urls` host configuration key.</span></span>
* <span data-ttu-id="d552a-834">`UseUrls` 拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="d552a-834">`UseUrls` extension method.</span></span>

<span data-ttu-id="d552a-835">これらの方法を使うと、1 つまたは複数の HTTP エンドポイントおよび HTTPS エンドポイント (既定の証明書が使用可能な場合は HTTPS) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-835">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d552a-836">セミコロン区切りのリストとして値を構成します (例: `"Urls": "http://localhost:8000;http://localhost:8001"`)。</span><span class="sxs-lookup"><span data-stu-id="d552a-836">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d552a-837">これらの方法について詳しくは、「[サーバーの URL](xref:fundamentals/host/web-host#server-urls)」および「[構成のオーバーライド](xref:fundamentals/host/web-host#override-configuration)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-837">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d552a-838">開発証明書が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-838">A development certificate is created:</span></span>

* <span data-ttu-id="d552a-839">[.NET Core SDK](/dotnet/core/sdk) がインストールされるとき。</span><span class="sxs-lookup"><span data-stu-id="d552a-839">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d552a-840">証明書を作成するには [dev-certs ツール](xref:aspnetcore-2.1#https)を使います。</span><span class="sxs-lookup"><span data-stu-id="d552a-840">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d552a-841">一部のブラウザーでは、ローカル開発証明書を信頼するには、明示的にアクセス許可を付与する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-841">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d552a-842">プロジェクト テンプレートでは、アプリを HTTPS で実行するように既定で構成され、[HTTPS のリダイレクトと HSTS のサポート](xref:security/enforcing-ssl)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d552a-842">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d552a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> で <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> または <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> メソッドを呼び出して、Kestrel 用に URL プレフィックスおよびポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-843">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d552a-844">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、`ASPNETCORE_URLS` 環境変数も機能しますが、このセクションで後述する制限があります (既定の証明書が、HTTPS エンドポイントの構成に使用できる必要があります)。</span><span class="sxs-lookup"><span data-stu-id="d552a-844">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d552a-845">`KestrelServerOptions` 構成: </span><span class="sxs-lookup"><span data-stu-id="d552a-845">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d552a-846">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="d552a-846">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d552a-847">指定した各エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-847">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d552a-848">`ConfigureEndpointDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d552a-848">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d552a-849">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="d552a-849">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d552a-850">各 HTTPS エンドポイントに対して実行するように、構成の `Action` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-850">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d552a-851">`ConfigureHttpsDefaults` を複数回呼び出すと、前の `Action` が最後に指定した `Action` で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d552a-851">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="d552a-852">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="d552a-852">Configure(IConfiguration)</span></span>

<span data-ttu-id="d552a-853">入力として <xref:Microsoft.Extensions.Configuration.IConfiguration> を受け取る Kestrel を設定するための構成ローダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-853">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d552a-854">構成のスコープは、Kestrel の構成セクションにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-854">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d552a-855">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d552a-855">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d552a-856">HTTPS を使用するように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-856">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d552a-857">`ListenOptions.UseHttps` 拡張機能:</span><span class="sxs-lookup"><span data-stu-id="d552a-857">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d552a-858">`UseHttps` &ndash; 既定の証明書で HTTPS を使うように Kestrel を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-858">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d552a-859">既定の証明書が構成されていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d552a-859">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="d552a-860">`ListenOptions.UseHttps` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="d552a-860">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d552a-861">`filename` は、アプリのコンテンツ ファイルが格納されているディレクトリを基準とする、証明書ファイルのパスとファイル名です。</span><span class="sxs-lookup"><span data-stu-id="d552a-861">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d552a-862">`password` は、X.509 証明書データにアクセスするために必要なパスワードです。</span><span class="sxs-lookup"><span data-stu-id="d552a-862">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d552a-863">`configureOptions` は、`HttpsConnectionAdapterOptions` を構成するための `Action` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-863">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d552a-864">`ListenOptions` を返します。</span><span class="sxs-lookup"><span data-stu-id="d552a-864">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d552a-865">`storeName` は、証明書の読み込み元の証明書ストアです。</span><span class="sxs-lookup"><span data-stu-id="d552a-865">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d552a-866">`subject` は、証明書のサブジェクト名です。</span><span class="sxs-lookup"><span data-stu-id="d552a-866">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d552a-867">`allowInvalid` は、無効な証明書 (自己署名証明書など) を考慮する必要があるかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-867">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d552a-868">`location` は、証明書を読み込むストアの場所です。</span><span class="sxs-lookup"><span data-stu-id="d552a-868">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d552a-869">`serverCertificate` は、X.509 証明書です。</span><span class="sxs-lookup"><span data-stu-id="d552a-869">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d552a-870">運用環境では、HTTPS を明示的に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-870">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d552a-871">少なくとも、既定の証明書を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-871">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d552a-872">サポートされている構成を次に説明します。</span><span class="sxs-lookup"><span data-stu-id="d552a-872">Supported configurations described next:</span></span>

* <span data-ttu-id="d552a-873">構成なし</span><span class="sxs-lookup"><span data-stu-id="d552a-873">No configuration</span></span>
* <span data-ttu-id="d552a-874">構成から既定の証明書を置き換える</span><span class="sxs-lookup"><span data-stu-id="d552a-874">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d552a-875">コードで既定値を変更する</span><span class="sxs-lookup"><span data-stu-id="d552a-875">Change the defaults in code</span></span>

<span data-ttu-id="d552a-876">*構成なし*</span><span class="sxs-lookup"><span data-stu-id="d552a-876">*No configuration*</span></span>

<span data-ttu-id="d552a-877">Kestrel は、`http://localhost:5000` と `https://localhost:5001` (既定の証明書が使用可能な場合) でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d552a-877">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d552a-878">*構成から既定の証明書を置き換える*</span><span class="sxs-lookup"><span data-stu-id="d552a-878">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d552a-879">`CreateDefaultBuilder` は既定で `Configure(context.Configuration.GetSection("Kestrel"))` を呼び出して Kestrel の構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-879">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d552a-880">Kestrel は、既定の HTTPS アプリ設定構成スキーマを使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-880">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d552a-881">ディスク上のファイルまたは証明書ストアから、URL や使用する証明書など、複数のエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-881">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d552a-882">以下の *appsettings.json* の例では、次のことが行われています。</span><span class="sxs-lookup"><span data-stu-id="d552a-882">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d552a-883">**AllowInvalid** を `true` に設定し、の無効な証明書 (自己署名証明書など) の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="d552a-883">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d552a-884">証明書 (後の例では **HttpsDefaultCert**) が指定されていないすべての HTTPS エンドポイントは、**[証明書]** > **[既定]** または開発証明書で定義されている証明書にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="d552a-884">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="d552a-885">証明書ノードの **Path** と **Password** を使用する代わりの方法は、証明書ストアのフィールドを使って証明書を指定することです。</span><span class="sxs-lookup"><span data-stu-id="d552a-885">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d552a-886">たとえば、**[証明書]** > **[既定]** の証明書は次のように指定できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-886">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d552a-887">スキーマに関する注意事項:</span><span class="sxs-lookup"><span data-stu-id="d552a-887">Schema notes:</span></span>

* <span data-ttu-id="d552a-888">エンドポイント名は大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-888">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d552a-889">たとえば、`HTTPS` と `Https` は有効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-889">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d552a-890">`Url` パラメーターは、エンドポイントごとに必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-890">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d552a-891">このパラメーターの形式は、1 つの値に制限されることを除き、最上位レベルの `Urls` 構成パラメーターと同じです。</span><span class="sxs-lookup"><span data-stu-id="d552a-891">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d552a-892">これらのエンドポイントは、最上位レベルの `Urls` 構成での定義に追加されるのではなく、それを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d552a-892">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d552a-893">コードで `Listen` を使用して定義されているエンドポイントは、構成セクションで定義されているエンドポイントに累積されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-893">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d552a-894">`Certificate` セクションは省略可能です。</span><span class="sxs-lookup"><span data-stu-id="d552a-894">The `Certificate` section is optional.</span></span> <span data-ttu-id="d552a-895">`Certificate` セクションを指定しないと、前述のシナリオで定義した既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-895">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d552a-896">既定値を使用できない場合、サーバーにより例外がスローされ、開始できません。</span><span class="sxs-lookup"><span data-stu-id="d552a-896">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d552a-897">`Certificate` セクションは、**Path**&ndash;**Password** 証明書と **Subject**&ndash;**Store** 証明書の両方をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d552a-897">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d552a-898">ポートが競合しない限り、この方法で任意の数のエンドポイントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-898">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d552a-899">`options.Configure(context.Configuration.GetSection("{SECTION}"))` が `.Endpoint(string name, listenOptions => { })` メソッドで返す `KestrelConfigurationLoader` を使用して、構成されているエンドポイントの設定を補足できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-899">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="d552a-900">`KestrelServerOptions.ConfigurationLoader` に直接アクセスして、既存のローダー (<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって提供されるものなど) の反復を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-900">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d552a-901">カスタム設定を読み取ることができるように、各エンドポイントの構成セクションを `Endpoint` メソッドのオプションで使用できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-901">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d552a-902">別のセクションで `options.Configure(context.Configuration.GetSection("{SECTION}"))` を再び呼び出すことにより、複数の構成を読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d552a-902">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d552a-903">前のインスタンスで `Load` を明示的に呼び出していない限り、最後の構成のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-903">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d552a-904">既定の構成セクションを置き換えることができるように、メタパッケージは `Load` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="d552a-904">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d552a-905">`KestrelConfigurationLoader` はミラー、`KestrelServerOptions` から API の `Listen` ファミリを `Endpoint` オーバーロードとしてミラーリングするので、コードと構成のエンドポイントを同じ場所で構成できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-905">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d552a-906">これらのオーバーロードでは名前は使用されず、構成からの既定の設定のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-906">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d552a-907">*コードで既定値を変更する*</span><span class="sxs-lookup"><span data-stu-id="d552a-907">*Change the defaults in code*</span></span>

<span data-ttu-id="d552a-908">`ConfigureEndpointDefaults` および`ConfigureHttpsDefaults` を使用して、`ListenOptions` および `HttpsConnectionAdapterOptions` の既定の設定を変更できます (前のシナリオで指定した既定の証明書のオーバーライドなど)。</span><span class="sxs-lookup"><span data-stu-id="d552a-908">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d552a-909">エンドポイントを構成する前に、`ConfigureEndpointDefaults` および `ConfigureHttpsDefaults` を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-909">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="d552a-910">*Kestrel による SNI のサポート*</span><span class="sxs-lookup"><span data-stu-id="d552a-910">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d552a-911">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) を使用すると、同じ IP アドレスとポートで複数のドメインをホストできます。</span><span class="sxs-lookup"><span data-stu-id="d552a-911">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d552a-912">SNI が機能するためには、サーバーが正しい証明書を提供できるように、クライアントは TLS ハンドシェイクの間にセキュリティで保護されたセッションのホスト名をサーバーに送信します。</span><span class="sxs-lookup"><span data-stu-id="d552a-912">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d552a-913">クライアントは、TLS ハンドシェイクに続くセキュリティで保護されたセッション中に、提供された証明書をサーバーとの暗号化された通信に使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-913">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d552a-914">Kestrel は、`ServerCertificateSelector` コールバックによって SNI をサポートします。</span><span class="sxs-lookup"><span data-stu-id="d552a-914">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d552a-915">コールバックは接続ごとに 1 回呼び出されるので、アプリはそれを使って、ホスト名を検査し、適切な証明書を選択できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-915">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d552a-916">SNI のサポートには、次が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-916">SNI support requires:</span></span>

* <span data-ttu-id="d552a-917">ターゲット フレームワーク `netcoreapp2.1` 以降で実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-917">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d552a-918">`net461` 以降、コールバックは呼び出されますが、`name` は常に `null` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-918">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d552a-919">クライアントが TLS ハンドシェイクでホスト名パラメーターを提供しない場合も、`name` は `null` です。</span><span class="sxs-lookup"><span data-stu-id="d552a-919">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d552a-920">すべての Web サイトは、同じ Kestrel インスタンスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-920">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d552a-921">Kestrel では、リバース プロキシは使用しない、複数のインスタンスでの IP アドレスとポートの共有はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="d552a-921">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d552a-922">TCP ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="d552a-922">Bind to a TCP socket</span></span>

<span data-ttu-id="d552a-923"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> メソッドは TCP ソケットにバインドし、オプションのラムダにより X.509 証明書を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d552a-923">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="d552a-924">例では、<xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> を使用して、エンドポイントに対する HTTPS が構成されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-924">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d552a-925">同じ API を使用して、特定のエンドポイントに対する他の Kestrel 設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-925">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d552a-926">UNIX ソケットにバインドする</span><span class="sxs-lookup"><span data-stu-id="d552a-926">Bind to a Unix socket</span></span>

<span data-ttu-id="d552a-927">次の例に示すように、Nginx でのパフォーマンスの向上のために <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> を使って UNIX ソケットをリッスンします。</span><span class="sxs-lookup"><span data-stu-id="d552a-927">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="d552a-928">ポート 0</span><span class="sxs-lookup"><span data-stu-id="d552a-928">Port 0</span></span>

<span data-ttu-id="d552a-929">ポート番号 `0` を指定すると、Kestrel は使用可能なポートに動的にバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-929">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d552a-930">次の例では、Kestrel が実行時に実際にバインドするポートを特定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d552a-930">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d552a-931">アプリを実行すると、コンソール ウィンドウの出力で、アプリがアクセスできる動的なポートが示されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-931">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d552a-932">制限事項</span><span class="sxs-lookup"><span data-stu-id="d552a-932">Limitations</span></span>

<span data-ttu-id="d552a-933">次の方法でエンドポイントを構成します。</span><span class="sxs-lookup"><span data-stu-id="d552a-933">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d552a-934">`--urls` コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="d552a-934">`--urls` command-line argument</span></span>
* <span data-ttu-id="d552a-935">`urls` ホスト構成キー</span><span class="sxs-lookup"><span data-stu-id="d552a-935">`urls` host configuration key</span></span>
* <span data-ttu-id="d552a-936">`ASPNETCORE_URLS` 環境変数</span><span class="sxs-lookup"><span data-stu-id="d552a-936">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d552a-937">コードを Kestrel 以外のサーバーで機能させるには、これらのメソッドが有用です。</span><span class="sxs-lookup"><span data-stu-id="d552a-937">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d552a-938">ただし、次の使用制限があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-938">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d552a-939">HTTPS エンドポイントの構成で (たとえば、前に説明したように `KestrelServerOptions` 構成または構成ファイルを使用して) 既定の証明書が提供されていない場合、これらの方法で HTTPS を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="d552a-939">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d552a-940">`Listen` と `UseUrls` 両方の方法を同時に使用すると、`Listen` エンドポイントが `UseUrls` エンドポイントをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-940">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d552a-941">IIS エンドポイントの構成</span><span class="sxs-lookup"><span data-stu-id="d552a-941">IIS endpoint configuration</span></span>

<span data-ttu-id="d552a-942">IIS を使用するときは、IIS オーバーライド バインドに対する URL バインドを、`Listen` または `UseUrls` によって設定します。</span><span class="sxs-lookup"><span data-stu-id="d552a-942">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d552a-943">詳しくは、「[ASP.NET Core モジュールの概要](xref:host-and-deploy/aspnet-core-module)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d552a-943">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="d552a-944">トランスポート構成</span><span class="sxs-lookup"><span data-stu-id="d552a-944">Transport configuration</span></span>

<span data-ttu-id="d552a-945">ASP.NET Core 2.1 のリリースにより、Kestrel の既定のトランスポートは、Libuv に基づかなくなり、代わりにマネージド ソケットに基づくようになりました。</span><span class="sxs-lookup"><span data-stu-id="d552a-945">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d552a-946">これは、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出す 2.1 にアップグレードする ASP.NET Core 2.0 アプリにとっては破壊的変更であり、以下のパッケージのいずれかに依存しています。</span><span class="sxs-lookup"><span data-stu-id="d552a-946">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d552a-947">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (パッケージの直接の参照)</span><span class="sxs-lookup"><span data-stu-id="d552a-947">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d552a-948">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d552a-948">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d552a-949">Libuv を使用する必要のあるプロジェクトの場合</span><span class="sxs-lookup"><span data-stu-id="d552a-949">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d552a-950">アプリのプロジェクト ファイルに [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) パッケージの依存関係を追加します。</span><span class="sxs-lookup"><span data-stu-id="d552a-950">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d552a-951"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d552a-951">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="d552a-952">URL プレフィックス</span><span class="sxs-lookup"><span data-stu-id="d552a-952">URL prefixes</span></span>

<span data-ttu-id="d552a-953">`UseUrls`、`--urls` コマンドライン引数、`urls` ホスト構成キー、または `ASPNETCORE_URLS` 環境変数を使用する場合、URL プレフィックスは次のいずれかの形式となります。</span><span class="sxs-lookup"><span data-stu-id="d552a-953">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d552a-954">HTTP URL プレフィックスのみが有効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-954">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d552a-955">`UseUrls` を使用して URL バインドを構成する場合、kestrel は HTTPS をサポートしません。</span><span class="sxs-lookup"><span data-stu-id="d552a-955">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d552a-956">IPv4 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-956">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d552a-957">`0.0.0.0` は、すべての IPv4 アドレスにバインドする特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="d552a-957">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d552a-958">IPv6 アドレスとポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-958">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d552a-959">IPv6 の `[::]` は IPv4 の `0.0.0.0` に相当します。</span><span class="sxs-lookup"><span data-stu-id="d552a-959">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d552a-960">ホスト名とポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-960">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d552a-961">ホスト名、`*`、および `+` は特別ではありません。</span><span class="sxs-lookup"><span data-stu-id="d552a-961">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d552a-962">有効な IP アドレスまたは `localhost` と認識されないものはすべて、IPv4 および IPv6 のすべての IP にバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-962">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d552a-963">異なるホスト名を同じポート上の異なる ASP.NET Core アプリにバインドするには、[HTTP.sys](xref:fundamentals/servers/httpsys) を使用するか、または IIS、Nginx、Apache などのリバース プロキシ サーバーを使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-963">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d552a-964">リバース プロキシ構成でのホストには[ホストのフィルター処理](#host-filtering)が必要です。</span><span class="sxs-lookup"><span data-stu-id="d552a-964">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d552a-965">`localhost` 名とポート番号、またはループバック IP とポート番号</span><span class="sxs-lookup"><span data-stu-id="d552a-965">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d552a-966">`localhost` が指定された場合、Kestrel は IPv4 ループバック インターフェイスと IPv6 ループバック インターフェイスの両方へのバインドを試みます。</span><span class="sxs-lookup"><span data-stu-id="d552a-966">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d552a-967">要求されたポートがいずれかのループバック インターフェイス上の別のサービスで使用中である場合、Kestrel は開始できません。</span><span class="sxs-lookup"><span data-stu-id="d552a-967">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d552a-968">他の何らかの理由でいずれかのループバック インターフェイスが使用できない場合 (ほとんどの場合は IPv6 がサポートされていないことが理由)、Kestrel は警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d552a-968">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d552a-969">ホスト フィルタリング</span><span class="sxs-lookup"><span data-stu-id="d552a-969">Host filtering</span></span>

<span data-ttu-id="d552a-970">Kestrel は `http://example.com:5000` などのプレフィックスに基づく構成をサポートしますが、Kestrel はほとんどのホスト名を無視します。</span><span class="sxs-lookup"><span data-stu-id="d552a-970">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d552a-971">ホスト `localhost` は、ループバック アドレスへのバインドに使用される特殊なケースです。</span><span class="sxs-lookup"><span data-stu-id="d552a-971">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d552a-972">明示的な IP アドレス以外のすべてのホストは、すべてのパブリック IP アドレスにバインドします。</span><span class="sxs-lookup"><span data-stu-id="d552a-972">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d552a-973">`Host` ヘッダーは検証されません。</span><span class="sxs-lookup"><span data-stu-id="d552a-973">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d552a-974">これを解決するには、Host Filtering Middleware を使用します。</span><span class="sxs-lookup"><span data-stu-id="d552a-974">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d552a-975">Host Filtering Middleware は、[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 または 2.2) に含まれる、[Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) パッケージによって提供されています。</span><span class="sxs-lookup"><span data-stu-id="d552a-975">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d552a-976">ミドルウェアは <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって追加され、そこでは <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d552a-976">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d552a-977">Host Filtering Middleware は既定では無効です。</span><span class="sxs-lookup"><span data-stu-id="d552a-977">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d552a-978">このミドルウェアを有効にするには、*appsettings.json*/*appsettings.\<>.json* に、`AllowedHosts` キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="d552a-978">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d552a-979">この値は、ポート番号を含まないホスト名のセミコロン区切りリストです。</span><span class="sxs-lookup"><span data-stu-id="d552a-979">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d552a-980">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d552a-980">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d552a-981">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) にも <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d552a-981">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d552a-982">Forwarded Headers Middleware および Host Filtering Middleware には、異なるシナリオ用に類似した機能があります。</span><span class="sxs-lookup"><span data-stu-id="d552a-982">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d552a-983">リバース プロキシ サーバーまたはロード バランサーを使用して要求を転送するとき、`Host` ヘッダーが保存されていない場合、Forwarded Headers Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="d552a-983">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d552a-984">Kestrel が一般向けエッジ サーバーとして使用されていたり、`Host` ヘッダーが直接転送されたりしている場合、Host Filtering Middleware に `AllowedHosts` を設定するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="d552a-984">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d552a-985">Forwarded Headers Middleware の詳細については、<xref:host-and-deploy/proxy-load-balancer> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d552a-985">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d552a-986">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d552a-986">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d552a-987">RFC 7230: メッセージの構文と経路制御 (セクション 5.4: ホスト)</span><span class="sxs-lookup"><span data-stu-id="d552a-987">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
