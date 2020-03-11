---
title: SignalR 構成の ASP.NET Core
author: bradygaster
description: ASP.NET Core SignalR アプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651686"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="18c19-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="18c19-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="18c19-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="18c19-105">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="18c19-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="18c19-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="18c19-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="18c19-108">`AddJsonProtocol` は、`Startup.ConfigureServices`の[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18c19-109">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="18c19-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数と戻り値のシリアル化を構成するために使用できる `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="18c19-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="18c19-111">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="18c19-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="18c19-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="18c19-113">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="18c19-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="18c19-114">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="18c19-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="18c19-116">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="18c19-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="18c19-117">`System.Text.Json`でサポートされていない `Newtonsoft.Json` の機能が必要な場合は、「 [Newtonsoft. Json への切り替え」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="18c19-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-118">MessagePack serialization options</span></span>

<span data-ttu-id="18c19-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="18c19-120">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="18c19-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="18c19-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-122">Configure server options</span></span>

<span data-ttu-id="18c19-123">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="18c19-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="18c19-124">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-124">Option</span></span> | <span data-ttu-id="18c19-125">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-125">Default Value</span></span> | <span data-ttu-id="18c19-126">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="18c19-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-127">30 seconds</span></span> | <span data-ttu-id="18c19-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="18c19-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="18c19-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="18c19-130">推奨値は、`KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="18c19-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="18c19-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-131">15 seconds</span></span> | <span data-ttu-id="18c19-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="18c19-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="18c19-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-134">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="18c19-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-135">15 seconds</span></span> | <span data-ttu-id="18c19-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="18c19-137">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="18c19-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="18c19-138">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="18c19-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="18c19-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="18c19-139">All installed protocols</span></span> | <span data-ttu-id="18c19-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="18c19-140">Protocols supported by this hub.</span></span> <span data-ttu-id="18c19-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="18c19-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="18c19-142">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="18c19-143">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="18c19-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="18c19-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="18c19-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="18c19-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="18c19-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-146">32 KB</span></span> | <span data-ttu-id="18c19-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="18c19-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="18c19-148">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="18c19-149">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="18c19-150">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="18c19-151">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="18c19-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="18c19-152">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="18c19-153">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="18c19-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="18c19-154">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-154">Option</span></span> | <span data-ttu-id="18c19-155">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-155">Default Value</span></span> | <span data-ttu-id="18c19-156">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="18c19-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-157">32 KB</span></span> | <span data-ttu-id="18c19-158">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="18c19-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="18c19-159">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="18c19-160">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="18c19-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="18c19-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="18c19-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="18c19-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-162">32 KB</span></span> | <span data-ttu-id="18c19-163">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="18c19-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="18c19-164">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="18c19-165">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="18c19-165">All Transports are enabled.</span></span> | <span data-ttu-id="18c19-166">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="18c19-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="18c19-167">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-167">See below.</span></span> | <span data-ttu-id="18c19-168">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="18c19-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="18c19-169">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-169">See below.</span></span> | <span data-ttu-id="18c19-170">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="18c19-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="18c19-171">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="18c19-172">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-172">Option</span></span> | <span data-ttu-id="18c19-173">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-173">Default Value</span></span> | <span data-ttu-id="18c19-174">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="18c19-175">90秒</span><span class="sxs-lookup"><span data-stu-id="18c19-175">90 seconds</span></span> | <span data-ttu-id="18c19-176">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="18c19-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="18c19-177">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="18c19-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="18c19-178">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="18c19-179">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-179">Option</span></span> | <span data-ttu-id="18c19-180">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-180">Default Value</span></span> | <span data-ttu-id="18c19-181">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="18c19-182">5 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-182">5 seconds</span></span> | <span data-ttu-id="18c19-183">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="18c19-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="18c19-184">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="18c19-185">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="18c19-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="18c19-186">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-186">Configure client options</span></span>

<span data-ttu-id="18c19-187">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="18c19-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="18c19-188">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="18c19-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="18c19-189">ログの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-189">Configure logging</span></span>

<span data-ttu-id="18c19-190">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="18c19-191">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="18c19-192">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="18c19-193">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="18c19-194">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="18c19-195">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="18c19-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="18c19-196">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18c19-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="18c19-197">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="18c19-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="18c19-198">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="18c19-199">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="18c19-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="18c19-200">`LogLevel` 値の代わりに、ログレベル名を表す `string` の値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="18c19-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="18c19-201">これは、`LogLevel` 定数にアクセスできない環境で SignalR logging を構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="18c19-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="18c19-202">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="18c19-202">The following table lists the available log levels.</span></span> <span data-ttu-id="18c19-203">`configureLogging` に指定する値は、ログに記録される**最小**ログレベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="18c19-204">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="18c19-205">String</span><span class="sxs-lookup"><span data-stu-id="18c19-205">String</span></span>                      | <span data-ttu-id="18c19-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="18c19-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="18c19-207">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="18c19-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="18c19-208">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="18c19-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="18c19-209">完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="18c19-210">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="18c19-211">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="18c19-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="18c19-212">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="18c19-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="18c19-213">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18c19-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="18c19-214">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="18c19-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="18c19-215">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="18c19-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="18c19-216">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-216">Configure allowed transports</span></span>

<span data-ttu-id="18c19-217">SignalR によって使用されるトランスポートは、`WithUrl` の呼び出し (JavaScript の`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="18c19-218">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="18c19-219">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="18c19-219">All transports are enabled by default.</span></span>

<span data-ttu-id="18c19-220">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="18c19-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="18c19-221">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="18c19-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="18c19-222">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="18c19-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="18c19-223">Java クライアントでは、トランスポートは、`HttpHubConnectionBuilder`の `withTransport` メソッドを使用して選択されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="18c19-224">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="18c19-225">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="18c19-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="18c19-226">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-226">Configure bearer authentication</span></span>

<span data-ttu-id="18c19-227">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript の`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="18c19-228">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="18c19-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="18c19-229">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="18c19-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="18c19-230">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="18c19-231">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="18c19-232">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="18c19-233">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="18c19-234">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="18c19-235">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="18c19-236">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="18c19-237">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="18c19-238">.NET</span><span class="sxs-lookup"><span data-stu-id="18c19-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="18c19-239">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-239">Option</span></span> | <span data-ttu-id="18c19-240">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-240">Default value</span></span> | <span data-ttu-id="18c19-241">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="18c19-242">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-243">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-243">Timeout for server activity.</span></span> <span data-ttu-id="18c19-244">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="18c19-245">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-246">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="18c19-247">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-247">15 seconds</span></span> | <span data-ttu-id="18c19-248">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="18c19-249">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="18c19-250">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-251">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="18c19-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-252">15 seconds</span></span> | <span data-ttu-id="18c19-253">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="18c19-254">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="18c19-255">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="18c19-256">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="18c19-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="18c19-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="18c19-258">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-258">Option</span></span> | <span data-ttu-id="18c19-259">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-259">Default value</span></span> | <span data-ttu-id="18c19-260">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="18c19-261">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-262">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-262">Timeout for server activity.</span></span> <span data-ttu-id="18c19-263">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="18c19-264">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-265">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="18c19-266">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="18c19-267">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="18c19-268">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="18c19-269">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="18c19-270">Java</span><span class="sxs-lookup"><span data-stu-id="18c19-270">Java</span></span>](#tab/java)

| <span data-ttu-id="18c19-271">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-271">Option</span></span> | <span data-ttu-id="18c19-272">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-272">Default value</span></span> | <span data-ttu-id="18c19-273">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="18c19-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="18c19-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="18c19-275">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-276">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-276">Timeout for server activity.</span></span> <span data-ttu-id="18c19-277">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="18c19-278">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-279">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="18c19-280">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-280">15 seconds</span></span> | <span data-ttu-id="18c19-281">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="18c19-282">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="18c19-283">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-284">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="18c19-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="18c19-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="18c19-286">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="18c19-287">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="18c19-288">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="18c19-289">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="18c19-290">追加のオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-290">Configure additional options</span></span>

<span data-ttu-id="18c19-291">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="18c19-292">.NET</span><span class="sxs-lookup"><span data-stu-id="18c19-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="18c19-293">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-293">.NET Option</span></span> |  <span data-ttu-id="18c19-294">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-294">Default value</span></span> | <span data-ttu-id="18c19-295">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="18c19-296">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="18c19-297">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-298">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-299">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="18c19-300">空</span><span class="sxs-lookup"><span data-stu-id="18c19-300">Empty</span></span> | <span data-ttu-id="18c19-301">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="18c19-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="18c19-302">空</span><span class="sxs-lookup"><span data-stu-id="18c19-302">Empty</span></span> | <span data-ttu-id="18c19-303">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="18c19-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="18c19-304">空</span><span class="sxs-lookup"><span data-stu-id="18c19-304">Empty</span></span> | <span data-ttu-id="18c19-305">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="18c19-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="18c19-306">5 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-306">5 seconds</span></span> | <span data-ttu-id="18c19-307">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="18c19-307">WebSockets only.</span></span> <span data-ttu-id="18c19-308">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="18c19-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="18c19-309">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="18c19-310">空</span><span class="sxs-lookup"><span data-stu-id="18c19-310">Empty</span></span> | <span data-ttu-id="18c19-311">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="18c19-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="18c19-312">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="18c19-313">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="18c19-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="18c19-314">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="18c19-315">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="18c19-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="18c19-316">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="18c19-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="18c19-317">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="18c19-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="18c19-318">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="18c19-319">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="18c19-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="18c19-320">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="18c19-321">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="18c19-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="18c19-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="18c19-323">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-323">JavaScript Option</span></span> | <span data-ttu-id="18c19-324">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-324">Default Value</span></span> | <span data-ttu-id="18c19-325">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="18c19-326">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="18c19-327">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-328">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-329">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="18c19-330">Java</span><span class="sxs-lookup"><span data-stu-id="18c19-330">Java</span></span>](#tab/java)

| <span data-ttu-id="18c19-331">Java オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-331">Java Option</span></span> | <span data-ttu-id="18c19-332">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-332">Default Value</span></span> | <span data-ttu-id="18c19-333">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="18c19-334">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="18c19-335">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-336">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-337">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="18c19-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="18c19-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="18c19-339">空</span><span class="sxs-lookup"><span data-stu-id="18c19-339">Empty</span></span> | <span data-ttu-id="18c19-340">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="18c19-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="18c19-341">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="18c19-342">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="18c19-343">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="18c19-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="18c19-344">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="18c19-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="18c19-345">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="18c19-346">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="18c19-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="18c19-347">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="18c19-348">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、`Startup.ConfigureServices` メソッドで[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="18c19-349">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="18c19-350">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と戻り値のシリアル化を構成するために使用できる JSON.NET `JsonSerializerSettings` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="18c19-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="18c19-351">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="18c19-352">たとえば、"キャメルケース" という既定の名前の代わりに "" という名前のプロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="18c19-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="18c19-353">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="18c19-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="18c19-354">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="18c19-355">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="18c19-356">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-356">MessagePack serialization options</span></span>

<span data-ttu-id="18c19-357">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="18c19-358">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="18c19-359">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="18c19-360">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-360">Configure server options</span></span>

<span data-ttu-id="18c19-361">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="18c19-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="18c19-362">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-362">Option</span></span> | <span data-ttu-id="18c19-363">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-363">Default Value</span></span> | <span data-ttu-id="18c19-364">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="18c19-365">30 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-365">30 seconds</span></span> | <span data-ttu-id="18c19-366">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="18c19-367">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="18c19-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="18c19-368">推奨値は、`KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="18c19-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="18c19-369">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-369">15 seconds</span></span> | <span data-ttu-id="18c19-370">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="18c19-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="18c19-371">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-372">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="18c19-373">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-373">15 seconds</span></span> | <span data-ttu-id="18c19-374">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="18c19-375">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="18c19-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="18c19-376">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="18c19-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="18c19-377">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="18c19-377">All installed protocols</span></span> | <span data-ttu-id="18c19-378">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="18c19-378">Protocols supported by this hub.</span></span> <span data-ttu-id="18c19-379">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="18c19-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="18c19-380">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="18c19-381">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="18c19-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="18c19-382">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="18c19-383">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="18c19-384">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="18c19-385">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="18c19-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="18c19-386">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="18c19-387">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="18c19-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="18c19-388">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-388">Option</span></span> | <span data-ttu-id="18c19-389">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-389">Default Value</span></span> | <span data-ttu-id="18c19-390">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="18c19-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-391">32 KB</span></span> | <span data-ttu-id="18c19-392">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="18c19-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="18c19-393">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="18c19-394">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="18c19-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="18c19-395">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="18c19-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="18c19-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-396">32 KB</span></span> | <span data-ttu-id="18c19-397">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="18c19-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="18c19-398">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="18c19-399">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="18c19-399">All Transports are enabled.</span></span> | <span data-ttu-id="18c19-400">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="18c19-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="18c19-401">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-401">See below.</span></span> | <span data-ttu-id="18c19-402">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="18c19-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="18c19-403">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-403">See below.</span></span> | <span data-ttu-id="18c19-404">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="18c19-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="18c19-405">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="18c19-406">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-406">Option</span></span> | <span data-ttu-id="18c19-407">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-407">Default Value</span></span> | <span data-ttu-id="18c19-408">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="18c19-409">90秒</span><span class="sxs-lookup"><span data-stu-id="18c19-409">90 seconds</span></span> | <span data-ttu-id="18c19-410">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="18c19-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="18c19-411">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="18c19-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="18c19-412">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="18c19-413">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-413">Option</span></span> | <span data-ttu-id="18c19-414">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-414">Default Value</span></span> | <span data-ttu-id="18c19-415">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="18c19-416">5 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-416">5 seconds</span></span> | <span data-ttu-id="18c19-417">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="18c19-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="18c19-418">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="18c19-419">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="18c19-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="18c19-420">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-420">Configure client options</span></span>

<span data-ttu-id="18c19-421">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="18c19-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="18c19-422">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="18c19-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="18c19-423">ログの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-423">Configure logging</span></span>

<span data-ttu-id="18c19-424">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="18c19-425">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="18c19-426">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="18c19-427">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="18c19-428">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="18c19-429">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="18c19-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="18c19-430">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18c19-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="18c19-431">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="18c19-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="18c19-432">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="18c19-433">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="18c19-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="18c19-434">完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="18c19-435">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="18c19-436">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="18c19-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="18c19-437">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="18c19-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="18c19-438">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18c19-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="18c19-439">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="18c19-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="18c19-440">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="18c19-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="18c19-441">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-441">Configure allowed transports</span></span>

<span data-ttu-id="18c19-442">SignalR によって使用されるトランスポートは、`WithUrl` の呼び出し (JavaScript の`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="18c19-443">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="18c19-444">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="18c19-444">All transports are enabled by default.</span></span>

<span data-ttu-id="18c19-445">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="18c19-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="18c19-446">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="18c19-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="18c19-447">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="18c19-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="18c19-448">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-448">Configure bearer authentication</span></span>

<span data-ttu-id="18c19-449">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript の`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="18c19-450">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="18c19-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="18c19-451">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="18c19-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="18c19-452">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="18c19-453">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="18c19-454">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="18c19-455">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="18c19-456">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="18c19-457">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="18c19-458">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="18c19-459">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="18c19-460">.NET</span><span class="sxs-lookup"><span data-stu-id="18c19-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="18c19-461">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-461">Option</span></span> | <span data-ttu-id="18c19-462">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-462">Default value</span></span> | <span data-ttu-id="18c19-463">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="18c19-464">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-465">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-465">Timeout for server activity.</span></span> <span data-ttu-id="18c19-466">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="18c19-467">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-468">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="18c19-469">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-469">15 seconds</span></span> | <span data-ttu-id="18c19-470">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="18c19-471">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="18c19-472">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-473">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="18c19-474">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-474">15 seconds</span></span> | <span data-ttu-id="18c19-475">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="18c19-476">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="18c19-477">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="18c19-478">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="18c19-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="18c19-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="18c19-480">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-480">Option</span></span> | <span data-ttu-id="18c19-481">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-481">Default value</span></span> | <span data-ttu-id="18c19-482">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="18c19-483">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-484">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-484">Timeout for server activity.</span></span> <span data-ttu-id="18c19-485">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="18c19-486">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-487">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="18c19-488">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="18c19-489">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="18c19-490">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="18c19-491">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="18c19-492">Java</span><span class="sxs-lookup"><span data-stu-id="18c19-492">Java</span></span>](#tab/java)

| <span data-ttu-id="18c19-493">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-493">Option</span></span> | <span data-ttu-id="18c19-494">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-494">Default value</span></span> | <span data-ttu-id="18c19-495">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="18c19-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="18c19-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="18c19-497">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-498">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-498">Timeout for server activity.</span></span> <span data-ttu-id="18c19-499">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="18c19-500">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-501">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="18c19-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-502">15 seconds</span></span> | <span data-ttu-id="18c19-503">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="18c19-504">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="18c19-505">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-506">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="18c19-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="18c19-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="18c19-508">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="18c19-509">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="18c19-510">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="18c19-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="18c19-511">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="18c19-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="18c19-512">追加のオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-512">Configure additional options</span></span>

<span data-ttu-id="18c19-513">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="18c19-514">.NET</span><span class="sxs-lookup"><span data-stu-id="18c19-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="18c19-515">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-515">.NET Option</span></span> |  <span data-ttu-id="18c19-516">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-516">Default value</span></span> | <span data-ttu-id="18c19-517">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="18c19-518">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="18c19-519">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-520">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-521">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="18c19-522">空</span><span class="sxs-lookup"><span data-stu-id="18c19-522">Empty</span></span> | <span data-ttu-id="18c19-523">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="18c19-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="18c19-524">空</span><span class="sxs-lookup"><span data-stu-id="18c19-524">Empty</span></span> | <span data-ttu-id="18c19-525">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="18c19-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="18c19-526">空</span><span class="sxs-lookup"><span data-stu-id="18c19-526">Empty</span></span> | <span data-ttu-id="18c19-527">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="18c19-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="18c19-528">5 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-528">5 seconds</span></span> | <span data-ttu-id="18c19-529">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="18c19-529">WebSockets only.</span></span> <span data-ttu-id="18c19-530">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="18c19-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="18c19-531">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="18c19-532">空</span><span class="sxs-lookup"><span data-stu-id="18c19-532">Empty</span></span> | <span data-ttu-id="18c19-533">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="18c19-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="18c19-534">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="18c19-535">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="18c19-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="18c19-536">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="18c19-537">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="18c19-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="18c19-538">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="18c19-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="18c19-539">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="18c19-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="18c19-540">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="18c19-541">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="18c19-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="18c19-542">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="18c19-543">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="18c19-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="18c19-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="18c19-545">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-545">JavaScript Option</span></span> | <span data-ttu-id="18c19-546">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-546">Default Value</span></span> | <span data-ttu-id="18c19-547">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="18c19-548">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="18c19-549">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-550">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-551">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="18c19-552">Java</span><span class="sxs-lookup"><span data-stu-id="18c19-552">Java</span></span>](#tab/java)

| <span data-ttu-id="18c19-553">Java オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-553">Java Option</span></span> | <span data-ttu-id="18c19-554">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-554">Default Value</span></span> | <span data-ttu-id="18c19-555">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="18c19-556">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="18c19-557">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-558">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-559">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="18c19-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="18c19-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="18c19-561">空</span><span class="sxs-lookup"><span data-stu-id="18c19-561">Empty</span></span> | <span data-ttu-id="18c19-562">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="18c19-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="18c19-563">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="18c19-564">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="18c19-565">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="18c19-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="18c19-566">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="18c19-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="18c19-567">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="18c19-568">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="18c19-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="18c19-569">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="18c19-570">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、`Startup.ConfigureServices` メソッドで[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="18c19-571">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="18c19-572">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と戻り値のシリアル化を構成するために使用できる JSON.NET `JsonSerializerSettings` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="18c19-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="18c19-573">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="18c19-574">たとえば、"キャメルケース" という既定の名前の代わりに "" という名前のプロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="18c19-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="18c19-575">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="18c19-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="18c19-576">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="18c19-577">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="18c19-578">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-578">MessagePack serialization options</span></span>

<span data-ttu-id="18c19-579">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="18c19-580">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="18c19-581">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="18c19-582">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-582">Configure server options</span></span>

<span data-ttu-id="18c19-583">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="18c19-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="18c19-584">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-584">Option</span></span> | <span data-ttu-id="18c19-585">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-585">Default Value</span></span> | <span data-ttu-id="18c19-586">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="18c19-587">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-587">15 seconds</span></span> | <span data-ttu-id="18c19-588">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="18c19-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="18c19-589">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-590">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="18c19-591">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-591">15 seconds</span></span> | <span data-ttu-id="18c19-592">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="18c19-593">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="18c19-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="18c19-594">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="18c19-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="18c19-595">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="18c19-595">All installed protocols</span></span> | <span data-ttu-id="18c19-596">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="18c19-596">Protocols supported by this hub.</span></span> <span data-ttu-id="18c19-597">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="18c19-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="18c19-598">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="18c19-599">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="18c19-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="18c19-600">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="18c19-601">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="18c19-602">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="18c19-603">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="18c19-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="18c19-604">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="18c19-605">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="18c19-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="18c19-606">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-606">Option</span></span> | <span data-ttu-id="18c19-607">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-607">Default Value</span></span> | <span data-ttu-id="18c19-608">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="18c19-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-609">32 KB</span></span> | <span data-ttu-id="18c19-610">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="18c19-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="18c19-611">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="18c19-612">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="18c19-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="18c19-613">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="18c19-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="18c19-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="18c19-614">32 KB</span></span> | <span data-ttu-id="18c19-615">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="18c19-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="18c19-616">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="18c19-617">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="18c19-617">All Transports are enabled.</span></span> | <span data-ttu-id="18c19-618">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="18c19-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="18c19-619">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-619">See below.</span></span> | <span data-ttu-id="18c19-620">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="18c19-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="18c19-621">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-621">See below.</span></span> | <span data-ttu-id="18c19-622">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="18c19-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="18c19-623">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="18c19-624">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-624">Option</span></span> | <span data-ttu-id="18c19-625">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-625">Default Value</span></span> | <span data-ttu-id="18c19-626">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="18c19-627">90秒</span><span class="sxs-lookup"><span data-stu-id="18c19-627">90 seconds</span></span> | <span data-ttu-id="18c19-628">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="18c19-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="18c19-629">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="18c19-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="18c19-630">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="18c19-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="18c19-631">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-631">Option</span></span> | <span data-ttu-id="18c19-632">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-632">Default Value</span></span> | <span data-ttu-id="18c19-633">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="18c19-634">5 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-634">5 seconds</span></span> | <span data-ttu-id="18c19-635">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="18c19-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="18c19-636">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="18c19-637">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="18c19-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="18c19-638">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-638">Configure client options</span></span>

<span data-ttu-id="18c19-639">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="18c19-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="18c19-640">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="18c19-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="18c19-641">ログの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-641">Configure logging</span></span>

<span data-ttu-id="18c19-642">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="18c19-643">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="18c19-644">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="18c19-645">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="18c19-646">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="18c19-647">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="18c19-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="18c19-648">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="18c19-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="18c19-649">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="18c19-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="18c19-650">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="18c19-651">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="18c19-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="18c19-652">完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="18c19-653">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="18c19-654">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="18c19-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="18c19-655">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="18c19-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="18c19-656">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="18c19-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="18c19-657">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="18c19-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="18c19-658">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="18c19-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="18c19-659">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="18c19-659">Configure allowed transports</span></span>

<span data-ttu-id="18c19-660">SignalR によって使用されるトランスポートは、`WithUrl` の呼び出し (JavaScript の`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="18c19-661">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="18c19-662">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="18c19-662">All transports are enabled by default.</span></span>

<span data-ttu-id="18c19-663">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="18c19-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="18c19-664">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="18c19-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="18c19-665">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-665">Configure bearer authentication</span></span>

<span data-ttu-id="18c19-666">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript の`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="18c19-667">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="18c19-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="18c19-668">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="18c19-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="18c19-669">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="18c19-670">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="18c19-671">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="18c19-672">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="18c19-673">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="18c19-674">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="18c19-675">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="18c19-676">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="18c19-677">.NET</span><span class="sxs-lookup"><span data-stu-id="18c19-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="18c19-678">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-678">Option</span></span> | <span data-ttu-id="18c19-679">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-679">Default value</span></span> | <span data-ttu-id="18c19-680">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="18c19-681">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-682">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-682">Timeout for server activity.</span></span> <span data-ttu-id="18c19-683">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="18c19-684">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-685">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="18c19-686">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-686">15 seconds</span></span> | <span data-ttu-id="18c19-687">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="18c19-688">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="18c19-689">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-690">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="18c19-691">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="18c19-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="18c19-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="18c19-693">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-693">Option</span></span> | <span data-ttu-id="18c19-694">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-694">Default value</span></span> | <span data-ttu-id="18c19-695">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="18c19-696">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-697">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-697">Timeout for server activity.</span></span> <span data-ttu-id="18c19-698">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="18c19-699">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-700">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="18c19-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="18c19-701">Java</span><span class="sxs-lookup"><span data-stu-id="18c19-701">Java</span></span>](#tab/java)

| <span data-ttu-id="18c19-702">オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-702">Option</span></span> | <span data-ttu-id="18c19-703">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-703">Default value</span></span> | <span data-ttu-id="18c19-704">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="18c19-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="18c19-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="18c19-706">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="18c19-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="18c19-707">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-707">Timeout for server activity.</span></span> <span data-ttu-id="18c19-708">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="18c19-709">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="18c19-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="18c19-710">推奨値は、サーバーの `KeepAliveInterval` 値の少なくとも2倍の数で、ping が到着するまでの時間を考慮します。</span><span class="sxs-lookup"><span data-stu-id="18c19-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="18c19-711">15 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-711">15 seconds</span></span> | <span data-ttu-id="18c19-712">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="18c19-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="18c19-713">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18c19-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="18c19-714">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="18c19-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="18c19-715">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18c19-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="18c19-716">追加のオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="18c19-716">Configure additional options</span></span>

<span data-ttu-id="18c19-717">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="18c19-718">.NET</span><span class="sxs-lookup"><span data-stu-id="18c19-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="18c19-719">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-719">.NET Option</span></span> |  <span data-ttu-id="18c19-720">既定値</span><span class="sxs-lookup"><span data-stu-id="18c19-720">Default value</span></span> | <span data-ttu-id="18c19-721">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="18c19-722">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="18c19-723">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-724">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-725">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="18c19-726">空</span><span class="sxs-lookup"><span data-stu-id="18c19-726">Empty</span></span> | <span data-ttu-id="18c19-727">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="18c19-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="18c19-728">空</span><span class="sxs-lookup"><span data-stu-id="18c19-728">Empty</span></span> | <span data-ttu-id="18c19-729">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="18c19-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="18c19-730">空</span><span class="sxs-lookup"><span data-stu-id="18c19-730">Empty</span></span> | <span data-ttu-id="18c19-731">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="18c19-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="18c19-732">5 秒</span><span class="sxs-lookup"><span data-stu-id="18c19-732">5 seconds</span></span> | <span data-ttu-id="18c19-733">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="18c19-733">WebSockets only.</span></span> <span data-ttu-id="18c19-734">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="18c19-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="18c19-735">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="18c19-736">空</span><span class="sxs-lookup"><span data-stu-id="18c19-736">Empty</span></span> | <span data-ttu-id="18c19-737">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="18c19-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="18c19-738">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="18c19-739">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="18c19-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="18c19-740">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="18c19-741">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="18c19-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="18c19-742">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="18c19-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="18c19-743">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="18c19-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="18c19-744">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="18c19-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="18c19-745">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="18c19-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="18c19-746">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="18c19-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="18c19-747">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18c19-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="18c19-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="18c19-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="18c19-749">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-749">JavaScript Option</span></span> | <span data-ttu-id="18c19-750">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-750">Default Value</span></span> | <span data-ttu-id="18c19-751">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="18c19-752">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="18c19-753">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-754">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-755">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="18c19-756">Java</span><span class="sxs-lookup"><span data-stu-id="18c19-756">Java</span></span>](#tab/java)

| <span data-ttu-id="18c19-757">Java オプション</span><span class="sxs-lookup"><span data-stu-id="18c19-757">Java Option</span></span> | <span data-ttu-id="18c19-758">Default value</span><span class="sxs-lookup"><span data-stu-id="18c19-758">Default Value</span></span> | <span data-ttu-id="18c19-759">説明</span><span class="sxs-lookup"><span data-stu-id="18c19-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="18c19-760">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="18c19-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="18c19-761">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="18c19-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="18c19-762">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="18c19-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="18c19-763">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18c19-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="18c19-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="18c19-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="18c19-765">空</span><span class="sxs-lookup"><span data-stu-id="18c19-765">Empty</span></span> | <span data-ttu-id="18c19-766">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="18c19-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="18c19-767">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="18c19-768">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="18c19-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="18c19-769">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="18c19-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="18c19-770">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="18c19-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end