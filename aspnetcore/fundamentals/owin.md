---
title: Open Web Interface for .NET (OWIN) と ASP.NET Core
author: ardalis
description: ASP.NET Core が Open Web Interface for .NET (OWIN) をどのようにサポートしているかを確認します。確認することで、Web アプリケーションを Web サーバーから切り離すことができるようになります。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650564"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="ce969-103">Open Web Interface for .NET (OWIN) と ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce969-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="ce969-104">作成者: [Steve Smith](https://ardalis.com/)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ce969-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ce969-105">ASP.NET Core は、Open Web Interface for .NET (OWIN) をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ce969-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="ce969-106">OWIN により、Web アプリを Web サーバーから切り離すことが可能になります。</span><span class="sxs-lookup"><span data-stu-id="ce969-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="ce969-107">ミドルウェアをパイプラインで使用し、要求と関連する応答を処理するための標準的な方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="ce969-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="ce969-108">ASP.NET Core アプリケーションとミドルウェアは、OWIN ベースのアプリケーション、サーバー、およびミドルウェアと相互運用できます。</span><span class="sxs-lookup"><span data-stu-id="ce969-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="ce969-109">OWIN には、さまざまなオブジェクト モデルを使用する 2 つのフレームワークを併用できる分離レイヤーがあります。</span><span class="sxs-lookup"><span data-stu-id="ce969-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="ce969-110">`Microsoft.AspNetCore.Owin` パッケージには 2 つのアダプター実装が用意されています。</span><span class="sxs-lookup"><span data-stu-id="ce969-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="ce969-111">ASP.NET Core から OWIN へ</span><span class="sxs-lookup"><span data-stu-id="ce969-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="ce969-112">OWIN から ASP.NET Core へ</span><span class="sxs-lookup"><span data-stu-id="ce969-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="ce969-113">これにより、ASP.NET Core を OWIN 互換のサーバー/ホスト上でホストするか、他の OWIN 互換コンポーネントを ASP.NET Core 上で実行することができます。</span><span class="sxs-lookup"><span data-stu-id="ce969-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="ce969-114">これらのアダプターを使用すると、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="ce969-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="ce969-115">ASP.NET Core コンポーネントのみを使用するアプリケーションでは、`Microsoft.AspNetCore.Owin` パッケージまたはアダプターを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ce969-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="ce969-116">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ce969-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="ce969-117">ASP.NET Core パイプラインで OWIN ミドルウェアを実行する</span><span class="sxs-lookup"><span data-stu-id="ce969-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="ce969-118">ASP.NET Core の OWIN のサポートは、`Microsoft.AspNetCore.Owin` パッケージの一部として展開されます。</span><span class="sxs-lookup"><span data-stu-id="ce969-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="ce969-119">このパッケージをインストールすることで、OWIN のサポートをプロジェクトにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="ce969-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="ce969-120">OWIN ミドルウェアは、[ インターフェイスと特定のキー (](https://owin.org/spec/spec/owin-1.0.0.html) など) の設定を必須とする `Func<IDictionary<string, object>, Task>`OWIN 仕様`owin.ResponseBody`に準拠しています。</span><span class="sxs-lookup"><span data-stu-id="ce969-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="ce969-121">次の単純な OWIN ミドルウェアを実行すると "Hello World" が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ce969-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="ce969-122">サンプル署名は `Task` を返し、OWIN で必要な場合に `IDictionary<string, object>` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ce969-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="ce969-123">次のコードは、`OwinHello` 拡張メソッドを使用して ASP.NET Core パイプラインに `UseOwin` ミドルウェア (上の図) を追加する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ce969-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="ce969-124">OWIN パイプライン内で実行する他のアクションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="ce969-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="ce969-125">応答ヘッダーは、応答ストリームへの最初の書き込み前にのみ変更してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="ce969-126">パフォーマンス上の理由から、`UseOwin` を複数回、呼び出すことは避けてください。</span><span class="sxs-lookup"><span data-stu-id="ce969-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="ce969-127">OWIN コンポーネントは、グループ化されている場合に最適に動作します。</span><span class="sxs-lookup"><span data-stu-id="ce969-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="ce969-128">OWIN ベースのサーバーで ASP.NET Core ホスティングを使用する</span><span class="sxs-lookup"><span data-stu-id="ce969-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="ce969-129">OWIN ベースのサーバーは、ASP.NET Core アプリをホストできます。</span><span class="sxs-lookup"><span data-stu-id="ce969-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="ce969-130">たとえば、.NET OWIN Web サーバーである [Nowin](https://github.com/Bobris/Nowin) です。</span><span class="sxs-lookup"><span data-stu-id="ce969-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="ce969-131">この記事のサンプルには ​​Nowin を参照するプロジェクトを含めました。また、それを使用して ASP.NET Core を自己ホスティングできる `IServer` を作成しています。</span><span class="sxs-lookup"><span data-stu-id="ce969-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="ce969-132">`IServer` は、`Features` プロパティと `Start` メソッドを必要とするインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="ce969-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="ce969-133">`Start` はサーバーの構成と起動を担当します。この例では、IServerAddressesFeature から解析されたアドレスを設定する一連の fluent API 呼び出しによって行われます。</span><span class="sxs-lookup"><span data-stu-id="ce969-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="ce969-134">`_builder` 変数の fluent 構成によって、メソッドの前半で定義された `appFunc` が要求を処理するように指定されている点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="ce969-135">この `Func` は、受信要求を処理するたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="ce969-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="ce969-136">また、Nowin サーバーの追加と構成を簡単に行うための `IWebHostBuilder` 拡張機能も追加します。</span><span class="sxs-lookup"><span data-stu-id="ce969-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="ce969-137">これが配置された状態で、*Program.cs* で拡張機能を呼び出し、このカスタム サーバーを利用して ASP.NET Core アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ce969-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="ce969-138">ASP.NET Core サーバーの詳細については、[こちら](xref:fundamentals/servers/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="ce969-139">OWIN ベースのサーバー上で ASP.NET Core を実行し、その WebSockets のサポートを利用する</span><span class="sxs-lookup"><span data-stu-id="ce969-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="ce969-140">OWIN ベースのサーバーの機能を ASP.NET Core で利用する方法のもう 1 つの例として、WebSockets などの機能へのアクセスが挙げられます。</span><span class="sxs-lookup"><span data-stu-id="ce969-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="ce969-141">前の例で使用していた .NET OWIN Web サーバーは、ASP.NET Core アプリケーションから利用できる組み込みの Web Sockets をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ce969-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="ce969-142">Web Sockets をサポートし、WebSockets を介してサーバーに送信されたすべてをエコー バックする単純な Web アプリケーションの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ce969-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="ce969-143">この[サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)は、前のサンプルと同じ `NowinServer` を使用して構成されています。唯一の違いは、アプリケーションがその `Configure` メソッドでどのように構成されているかです。</span><span class="sxs-lookup"><span data-stu-id="ce969-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="ce969-144">[単純な websocket クライアント](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en)を使用したテストで、アプリケーションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="ce969-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Web ソケットのテスト クライアント](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="ce969-146">OWIN 環境</span><span class="sxs-lookup"><span data-stu-id="ce969-146">OWIN environment</span></span>

<span data-ttu-id="ce969-147">`HttpContext` を使用して OWIN 環境を構築できます。</span><span class="sxs-lookup"><span data-stu-id="ce969-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="ce969-148">OWIN キー</span><span class="sxs-lookup"><span data-stu-id="ce969-148">OWIN keys</span></span>

<span data-ttu-id="ce969-149">OWIN は、HTTP 要求/応答の交換を通じて情報を伝達するために `IDictionary<string,object>` オブジェクトに依存しています。</span><span class="sxs-lookup"><span data-stu-id="ce969-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="ce969-150">ASP.NET Core は次のキーを実装しています。</span><span class="sxs-lookup"><span data-stu-id="ce969-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="ce969-151">[主な仕様、拡張機能](https://owin.org/#spec)に関するセクションと、「[OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html)」(OWIN キーのガイドラインと共通キー) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="ce969-152">要求データ (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="ce969-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="ce969-153">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-153">Key</span></span>               | <span data-ttu-id="ce969-154">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-154">Value (type)</span></span> | <span data-ttu-id="ce969-155">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="ce969-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="ce969-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="ce969-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="ce969-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="ce969-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="ce969-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="ce969-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="ce969-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="ce969-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="ce969-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="ce969-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="ce969-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="ce969-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="ce969-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="ce969-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="ce969-164">要求データ (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="ce969-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="ce969-165">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-165">Key</span></span>               | <span data-ttu-id="ce969-166">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-166">Value (type)</span></span> | <span data-ttu-id="ce969-167">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="ce969-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="ce969-169">Optional</span><span class="sxs-lookup"><span data-stu-id="ce969-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="ce969-170">応答データ (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="ce969-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="ce969-171">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-171">Key</span></span>               | <span data-ttu-id="ce969-172">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-172">Value (type)</span></span> | <span data-ttu-id="ce969-173">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="ce969-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="ce969-175">Optional</span><span class="sxs-lookup"><span data-stu-id="ce969-175">Optional</span></span> |
| <span data-ttu-id="ce969-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="ce969-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="ce969-177">Optional</span><span class="sxs-lookup"><span data-stu-id="ce969-177">Optional</span></span> |
| <span data-ttu-id="ce969-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="ce969-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="ce969-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="ce969-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="ce969-180">その他のデータ (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="ce969-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="ce969-181">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-181">Key</span></span>               | <span data-ttu-id="ce969-182">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-182">Value (type)</span></span> | <span data-ttu-id="ce969-183">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="ce969-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="ce969-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="ce969-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="ce969-186">共通キー</span><span class="sxs-lookup"><span data-stu-id="ce969-186">Common keys</span></span>

| <span data-ttu-id="ce969-187">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-187">Key</span></span>               | <span data-ttu-id="ce969-188">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-188">Value (type)</span></span> | <span data-ttu-id="ce969-189">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="ce969-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="ce969-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="ce969-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="ce969-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="ce969-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="ce969-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="ce969-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="ce969-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="ce969-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="ce969-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="ce969-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="ce969-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="ce969-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="ce969-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="ce969-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="ce969-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="ce969-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="ce969-199">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-199">Key</span></span>               | <span data-ttu-id="ce969-200">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-200">Value (type)</span></span> | <span data-ttu-id="ce969-201">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="ce969-202">sendfile.SendAsync</span></span> | <span data-ttu-id="ce969-203">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="ce969-204">要求ごと</span><span class="sxs-lookup"><span data-stu-id="ce969-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="ce969-205">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="ce969-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="ce969-206">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-206">Key</span></span>               | <span data-ttu-id="ce969-207">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-207">Value (type)</span></span> | <span data-ttu-id="ce969-208">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="ce969-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="ce969-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="ce969-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="ce969-211">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="ce969-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="ce969-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="ce969-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="ce969-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="ce969-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="ce969-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="ce969-215">キー</span><span class="sxs-lookup"><span data-stu-id="ce969-215">Key</span></span>               | <span data-ttu-id="ce969-216">値 (型)</span><span class="sxs-lookup"><span data-stu-id="ce969-216">Value (type)</span></span> | <span data-ttu-id="ce969-217">説明</span><span class="sxs-lookup"><span data-stu-id="ce969-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="ce969-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="ce969-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="ce969-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="ce969-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="ce969-220">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="ce969-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="ce969-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="ce969-222">記述なし</span><span class="sxs-lookup"><span data-stu-id="ce969-222">Non-spec</span></span> |
| <span data-ttu-id="ce969-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="ce969-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="ce969-224">[RFC6455 のセクション 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) の手順 5.5 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="ce969-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="ce969-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="ce969-226">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="ce969-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="ce969-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="ce969-228">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="ce969-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="ce969-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="ce969-230">「[Delegate Signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)」(デリゲート シグネチャ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ce969-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="ce969-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="ce969-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="ce969-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="ce969-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="ce969-233">Optional</span><span class="sxs-lookup"><span data-stu-id="ce969-233">Optional</span></span> |
| <span data-ttu-id="ce969-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="ce969-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="ce969-235">Optional</span><span class="sxs-lookup"><span data-stu-id="ce969-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="ce969-236">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ce969-236">Additional resources</span></span>

* [<span data-ttu-id="ce969-237">ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="ce969-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ce969-238">サーバー</span><span class="sxs-lookup"><span data-stu-id="ce969-238">Servers</span></span>](xref:fundamentals/servers/index)
