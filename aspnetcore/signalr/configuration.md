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
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358113"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="a41a1-103">SignalR 構成の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a41a1-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a41a1-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a41a1-105">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a41a1-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a41a1-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a41a1-108">`AddJsonProtocol` は、`Startup.ConfigureServices`の[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a41a1-109">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a41a1-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数と戻り値のシリアル化を構成するために使用できる `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a41a1-111">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a41a1-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a41a1-113">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a41a1-114">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a41a1-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a41a1-116">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="a41a1-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a41a1-117">`System.Text.Json`でサポートされていない `Newtonsoft.Json` の機能が必要な場合は、「 [Newtonsoft. Json への切り替え」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a41a1-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-118">MessagePack serialization options</span></span>

<span data-ttu-id="a41a1-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a41a1-120">詳細については[、「SignalRの Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a1-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a41a1-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-122">Configure server options</span></span>

<span data-ttu-id="a41a1-123">次の表では、SignalR ハブを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a41a1-124">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-124">Option</span></span> | <span data-ttu-id="a41a1-125">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-125">Default Value</span></span> | <span data-ttu-id="a41a1-126">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a41a1-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-127">30 seconds</span></span> | <span data-ttu-id="a41a1-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a41a1-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a41a1-130">推奨値は、`KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a41a1-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-131">15 seconds</span></span> | <span data-ttu-id="a41a1-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a41a1-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-134">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a41a1-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-135">15 seconds</span></span> | <span data-ttu-id="a41a1-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a41a1-137">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a41a1-138">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a41a1-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="a41a1-139">All installed protocols</span></span> | <span data-ttu-id="a41a1-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="a41a1-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a41a1-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a41a1-142">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a41a1-143">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a41a1-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a41a1-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a41a1-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-146">32 KB</span></span> | <span data-ttu-id="a41a1-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a41a1-148">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a41a1-149">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a41a1-150">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a41a1-151">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a41a1-152">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a41a1-153">次の表では、ASP.NET Core SignalRの詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a41a1-154">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-154">Option</span></span> | <span data-ttu-id="a41a1-155">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-155">Default Value</span></span> | <span data-ttu-id="a41a1-156">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a41a1-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-157">32 KB</span></span> | <span data-ttu-id="a41a1-158">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a41a1-159">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a41a1-160">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a41a1-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="a41a1-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a41a1-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-162">32 KB</span></span> | <span data-ttu-id="a41a1-163">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a41a1-164">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a41a1-165">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-165">All Transports are enabled.</span></span> | <span data-ttu-id="a41a1-166">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="a41a1-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a41a1-167">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-167">See below.</span></span> | <span data-ttu-id="a41a1-168">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a41a1-169">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-169">See below.</span></span> | <span data-ttu-id="a41a1-170">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a41a1-171">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a41a1-172">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-172">Option</span></span> | <span data-ttu-id="a41a1-173">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-173">Default Value</span></span> | <span data-ttu-id="a41a1-174">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a41a1-175">90秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-175">90 seconds</span></span> | <span data-ttu-id="a41a1-176">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="a41a1-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a41a1-177">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a41a1-178">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a41a1-179">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-179">Option</span></span> | <span data-ttu-id="a41a1-180">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-180">Default Value</span></span> | <span data-ttu-id="a41a1-181">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a41a1-182">5 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-182">5 seconds</span></span> | <span data-ttu-id="a41a1-183">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a41a1-184">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a41a1-185">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a41a1-186">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-186">Configure client options</span></span>

<span data-ttu-id="a41a1-187">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="a41a1-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a41a1-188">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a41a1-189">ログの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-189">Configure logging</span></span>

<span data-ttu-id="a41a1-190">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a41a1-191">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a41a1-192">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a1-193">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a41a1-194">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a41a1-195">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a41a1-196">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a41a1-197">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a41a1-198">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a41a1-199">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a41a1-200">`LogLevel` 値の代わりに、ログレベル名を表す `string` の値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a41a1-201">これは、`LogLevel` 定数にアクセスできない環境で SignalR ログを構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a41a1-202">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-202">The following table lists the available log levels.</span></span> <span data-ttu-id="a41a1-203">`configureLogging` に指定する値は、ログに記録される**最小**ログレベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a41a1-204">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a41a1-205">文字列型</span><span class="sxs-lookup"><span data-stu-id="a41a1-205">String</span></span>                      | <span data-ttu-id="a41a1-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a41a1-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a41a1-207">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="a41a1-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a41a1-208">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="a41a1-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a41a1-209">ログ記録を完全に無効にするには、`configureLogging`メソッドで`signalR.LogLevel.None`を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a41a1-210">ログ記録の詳細については、 [SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a41a1-211">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a41a1-212">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a41a1-213">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a41a1-214">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a41a1-215">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a41a1-216">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-216">Configure allowed transports</span></span>

<span data-ttu-id="a41a1-217">SignalR によって使用されるトランスポートは、`WithUrl` 呼び出し (JavaScript では`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a41a1-218">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a41a1-219">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-219">All transports are enabled by default.</span></span>

<span data-ttu-id="a41a1-220">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a41a1-221">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a41a1-222">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a41a1-223">Java クライアントでは、トランスポートは、`HttpHubConnectionBuilder`の `withTransport` メソッドを使用して選択されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a41a1-224">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a41a1-225">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a41a1-226">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-226">Configure bearer authentication</span></span>

<span data-ttu-id="a41a1-227">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript では`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a41a1-228">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="a41a1-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a41a1-229">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a41a1-230">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a41a1-231">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a41a1-232">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a41a1-233">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a41a1-234">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a41a1-235">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a41a1-236">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a41a1-237">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a41a1-238">.NET</span><span class="sxs-lookup"><span data-stu-id="a41a1-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a41a1-239">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-239">Option</span></span> | <span data-ttu-id="a41a1-240">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-240">Default value</span></span> | <span data-ttu-id="a41a1-241">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a41a1-242">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-243">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-243">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-244">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a41a1-245">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-246">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a41a1-247">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-247">15 seconds</span></span> | <span data-ttu-id="a41a1-248">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="a41a1-249">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a41a1-250">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-251">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a41a1-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-252">15 seconds</span></span> | <span data-ttu-id="a41a1-253">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a41a1-254">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a41a1-255">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a41a1-256">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a41a1-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a41a1-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a41a1-258">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-258">Option</span></span> | <span data-ttu-id="a41a1-259">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-259">Default value</span></span> | <span data-ttu-id="a41a1-260">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a41a1-261">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-262">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-262">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-263">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a41a1-264">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-265">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a41a1-266">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-267">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a41a1-268">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a41a1-269">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a41a1-270">Java</span><span class="sxs-lookup"><span data-stu-id="a41a1-270">Java</span></span>](#tab/java)

| <span data-ttu-id="a41a1-271">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-271">Option</span></span> | <span data-ttu-id="a41a1-272">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-272">Default value</span></span> | <span data-ttu-id="a41a1-273">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a41a1-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a41a1-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a41a1-275">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-276">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-276">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-277">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a41a1-278">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-279">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a41a1-280">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-280">15 seconds</span></span> | <span data-ttu-id="a41a1-281">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="a41a1-282">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a41a1-283">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-284">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a41a1-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a41a1-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a41a1-286">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-287">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a41a1-288">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a41a1-289">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a41a1-290">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-290">Configure additional options</span></span>

<span data-ttu-id="a41a1-291">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a41a1-292">.NET</span><span class="sxs-lookup"><span data-stu-id="a41a1-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a41a1-293">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-293">.NET Option</span></span> |  <span data-ttu-id="a41a1-294">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-294">Default value</span></span> | <span data-ttu-id="a41a1-295">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a41a1-296">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a41a1-297">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-298">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-299">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a41a1-300">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-300">Empty</span></span> | <span data-ttu-id="a41a1-301">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a41a1-302">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-302">Empty</span></span> | <span data-ttu-id="a41a1-303">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a41a1-304">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-304">Empty</span></span> | <span data-ttu-id="a41a1-305">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="a41a1-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a41a1-306">5 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-306">5 seconds</span></span> | <span data-ttu-id="a41a1-307">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-307">WebSockets only.</span></span> <span data-ttu-id="a41a1-308">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="a41a1-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a41a1-309">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a41a1-310">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-310">Empty</span></span> | <span data-ttu-id="a41a1-311">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a41a1-312">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a41a1-313">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="a41a1-314">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a41a1-315">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a41a1-316">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="a41a1-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a41a1-317">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a41a1-318">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a41a1-319">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a41a1-320">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a41a1-321">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a41a1-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a41a1-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a41a1-323">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-323">JavaScript Option</span></span> | <span data-ttu-id="a41a1-324">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-324">Default Value</span></span> | <span data-ttu-id="a41a1-325">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a41a1-326">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a41a1-327">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-328">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-329">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a41a1-330">Java</span><span class="sxs-lookup"><span data-stu-id="a41a1-330">Java</span></span>](#tab/java)

| <span data-ttu-id="a41a1-331">Java オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-331">Java Option</span></span> | <span data-ttu-id="a41a1-332">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-332">Default Value</span></span> | <span data-ttu-id="a41a1-333">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a41a1-334">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a41a1-335">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-336">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-337">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a41a1-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a41a1-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a41a1-339">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-339">Empty</span></span> | <span data-ttu-id="a41a1-340">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a41a1-341">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a41a1-342">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a41a1-343">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a41a1-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a41a1-344">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a41a1-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a41a1-345">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a41a1-346">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a41a1-347">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a41a1-348">JSON のシリアル化は、 [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用してサーバー上で構成できます。これは `Startup.ConfigureServices` メソッドの [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a41a1-349">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a41a1-350">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と戻り値のシリアル化を構成するために使用できる JSON.NET `JsonSerializerSettings` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a41a1-351">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a41a1-352">たとえば、"キャメルケース" という既定の名前の代わりに "" という名前のプロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a41a1-353">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a41a1-354">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a41a1-355">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a41a1-356">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-356">MessagePack serialization options</span></span>

<span data-ttu-id="a41a1-357">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a41a1-358">詳細については[、「SignalRの Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a1-359">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a41a1-360">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-360">Configure server options</span></span>

<span data-ttu-id="a41a1-361">次の表では、SignalR ハブを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a41a1-362">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-362">Option</span></span> | <span data-ttu-id="a41a1-363">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-363">Default Value</span></span> | <span data-ttu-id="a41a1-364">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a41a1-365">30 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-365">30 seconds</span></span> | <span data-ttu-id="a41a1-366">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a41a1-367">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a41a1-368">推奨値は、`KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a41a1-369">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-369">15 seconds</span></span> | <span data-ttu-id="a41a1-370">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a41a1-371">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-372">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a41a1-373">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-373">15 seconds</span></span> | <span data-ttu-id="a41a1-374">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a41a1-375">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a41a1-376">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a41a1-377">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="a41a1-377">All installed protocols</span></span> | <span data-ttu-id="a41a1-378">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="a41a1-378">Protocols supported by this hub.</span></span> <span data-ttu-id="a41a1-379">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a41a1-380">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a41a1-381">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a41a1-382">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a41a1-383">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a41a1-384">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a41a1-385">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a41a1-386">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a41a1-387">次の表では、ASP.NET Core SignalRの詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a41a1-388">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-388">Option</span></span> | <span data-ttu-id="a41a1-389">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-389">Default Value</span></span> | <span data-ttu-id="a41a1-390">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a41a1-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-391">32 KB</span></span> | <span data-ttu-id="a41a1-392">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a41a1-393">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a41a1-394">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a41a1-395">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="a41a1-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a41a1-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-396">32 KB</span></span> | <span data-ttu-id="a41a1-397">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a41a1-398">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a41a1-399">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-399">All Transports are enabled.</span></span> | <span data-ttu-id="a41a1-400">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="a41a1-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a41a1-401">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-401">See below.</span></span> | <span data-ttu-id="a41a1-402">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a41a1-403">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-403">See below.</span></span> | <span data-ttu-id="a41a1-404">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a41a1-405">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a41a1-406">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-406">Option</span></span> | <span data-ttu-id="a41a1-407">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-407">Default Value</span></span> | <span data-ttu-id="a41a1-408">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a41a1-409">90秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-409">90 seconds</span></span> | <span data-ttu-id="a41a1-410">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="a41a1-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a41a1-411">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a41a1-412">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a41a1-413">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-413">Option</span></span> | <span data-ttu-id="a41a1-414">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-414">Default Value</span></span> | <span data-ttu-id="a41a1-415">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a41a1-416">5 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-416">5 seconds</span></span> | <span data-ttu-id="a41a1-417">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a41a1-418">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a41a1-419">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a41a1-420">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-420">Configure client options</span></span>

<span data-ttu-id="a41a1-421">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="a41a1-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a41a1-422">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a41a1-423">ログの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-423">Configure logging</span></span>

<span data-ttu-id="a41a1-424">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a41a1-425">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a41a1-426">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a1-427">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a41a1-428">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a41a1-429">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a41a1-430">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a41a1-431">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a41a1-432">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a41a1-433">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a41a1-434">ログ記録を完全に無効にするには、`configureLogging`メソッドで`signalR.LogLevel.None`を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a41a1-435">ログ記録の詳細については、 [SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a41a1-436">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a41a1-437">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a41a1-438">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a41a1-439">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a41a1-440">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a41a1-441">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-441">Configure allowed transports</span></span>

<span data-ttu-id="a41a1-442">SignalR によって使用されるトランスポートは、`WithUrl` 呼び出し (JavaScript では`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a41a1-443">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a41a1-444">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-444">All transports are enabled by default.</span></span>

<span data-ttu-id="a41a1-445">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a41a1-446">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a41a1-447">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a41a1-448">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-448">Configure bearer authentication</span></span>

<span data-ttu-id="a41a1-449">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript では`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a41a1-450">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="a41a1-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a41a1-451">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a41a1-452">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a41a1-453">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a41a1-454">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a41a1-455">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a41a1-456">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a41a1-457">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a41a1-458">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a41a1-459">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a41a1-460">.NET</span><span class="sxs-lookup"><span data-stu-id="a41a1-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a41a1-461">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-461">Option</span></span> | <span data-ttu-id="a41a1-462">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-462">Default value</span></span> | <span data-ttu-id="a41a1-463">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a41a1-464">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-465">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-465">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-466">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a41a1-467">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-468">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a41a1-469">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-469">15 seconds</span></span> | <span data-ttu-id="a41a1-470">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="a41a1-471">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a41a1-472">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-473">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a41a1-474">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-474">15 seconds</span></span> | <span data-ttu-id="a41a1-475">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a41a1-476">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a41a1-477">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a41a1-478">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a41a1-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a41a1-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a41a1-480">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-480">Option</span></span> | <span data-ttu-id="a41a1-481">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-481">Default value</span></span> | <span data-ttu-id="a41a1-482">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a41a1-483">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-484">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-484">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-485">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a41a1-486">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-487">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a41a1-488">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-489">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a41a1-490">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a41a1-491">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a41a1-492">Java</span><span class="sxs-lookup"><span data-stu-id="a41a1-492">Java</span></span>](#tab/java)

| <span data-ttu-id="a41a1-493">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-493">Option</span></span> | <span data-ttu-id="a41a1-494">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-494">Default value</span></span> | <span data-ttu-id="a41a1-495">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a41a1-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a41a1-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a41a1-497">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-498">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-498">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-499">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a41a1-500">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-501">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a41a1-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-502">15 seconds</span></span> | <span data-ttu-id="a41a1-503">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="a41a1-504">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a41a1-505">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-506">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a41a1-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a41a1-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a41a1-508">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-509">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a41a1-510">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a41a1-511">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a41a1-512">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-512">Configure additional options</span></span>

<span data-ttu-id="a41a1-513">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a41a1-514">.NET</span><span class="sxs-lookup"><span data-stu-id="a41a1-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a41a1-515">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-515">.NET Option</span></span> |  <span data-ttu-id="a41a1-516">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-516">Default value</span></span> | <span data-ttu-id="a41a1-517">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a41a1-518">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a41a1-519">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-520">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-521">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a41a1-522">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-522">Empty</span></span> | <span data-ttu-id="a41a1-523">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a41a1-524">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-524">Empty</span></span> | <span data-ttu-id="a41a1-525">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a41a1-526">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-526">Empty</span></span> | <span data-ttu-id="a41a1-527">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="a41a1-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a41a1-528">5 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-528">5 seconds</span></span> | <span data-ttu-id="a41a1-529">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-529">WebSockets only.</span></span> <span data-ttu-id="a41a1-530">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="a41a1-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a41a1-531">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a41a1-532">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-532">Empty</span></span> | <span data-ttu-id="a41a1-533">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a41a1-534">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a41a1-535">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="a41a1-536">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a41a1-537">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a41a1-538">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="a41a1-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a41a1-539">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a41a1-540">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a41a1-541">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a41a1-542">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a41a1-543">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a41a1-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a41a1-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a41a1-545">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-545">JavaScript Option</span></span> | <span data-ttu-id="a41a1-546">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-546">Default Value</span></span> | <span data-ttu-id="a41a1-547">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a41a1-548">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a41a1-549">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-550">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-551">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a41a1-552">Java</span><span class="sxs-lookup"><span data-stu-id="a41a1-552">Java</span></span>](#tab/java)

| <span data-ttu-id="a41a1-553">Java オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-553">Java Option</span></span> | <span data-ttu-id="a41a1-554">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-554">Default Value</span></span> | <span data-ttu-id="a41a1-555">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a41a1-556">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a41a1-557">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-558">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-559">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a41a1-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a41a1-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a41a1-561">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-561">Empty</span></span> | <span data-ttu-id="a41a1-562">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a41a1-563">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a41a1-564">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a41a1-565">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a41a1-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a41a1-566">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a41a1-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a41a1-567">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a41a1-568">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a41a1-569">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a41a1-570">JSON のシリアル化は、 [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用してサーバー上で構成できます。これは `Startup.ConfigureServices` メソッドの [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a41a1-571">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a41a1-572">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と戻り値のシリアル化を構成するために使用できる JSON.NET `JsonSerializerSettings` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a41a1-573">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a41a1-574">たとえば、"キャメルケース" という既定の名前の代わりに "" という名前のプロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a41a1-575">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a41a1-576">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a41a1-577">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a41a1-578">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-578">MessagePack serialization options</span></span>

<span data-ttu-id="a41a1-579">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a41a1-580">詳細については[、「SignalRの Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a1-581">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a41a1-582">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-582">Configure server options</span></span>

<span data-ttu-id="a41a1-583">次の表では、SignalR ハブを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a41a1-584">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-584">Option</span></span> | <span data-ttu-id="a41a1-585">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-585">Default Value</span></span> | <span data-ttu-id="a41a1-586">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a41a1-587">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-587">15 seconds</span></span> | <span data-ttu-id="a41a1-588">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a41a1-589">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-590">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a41a1-591">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-591">15 seconds</span></span> | <span data-ttu-id="a41a1-592">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a41a1-593">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a41a1-594">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a41a1-595">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="a41a1-595">All installed protocols</span></span> | <span data-ttu-id="a41a1-596">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="a41a1-596">Protocols supported by this hub.</span></span> <span data-ttu-id="a41a1-597">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a41a1-598">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a41a1-599">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="a41a1-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a41a1-600">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a41a1-601">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a41a1-602">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a41a1-603">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a41a1-604">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a41a1-605">次の表では、ASP.NET Core SignalRの詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a41a1-606">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-606">Option</span></span> | <span data-ttu-id="a41a1-607">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-607">Default Value</span></span> | <span data-ttu-id="a41a1-608">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a41a1-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-609">32 KB</span></span> | <span data-ttu-id="a41a1-610">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a41a1-611">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a41a1-612">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a41a1-613">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="a41a1-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a41a1-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="a41a1-614">32 KB</span></span> | <span data-ttu-id="a41a1-615">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a41a1-616">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a41a1-617">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-617">All Transports are enabled.</span></span> | <span data-ttu-id="a41a1-618">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="a41a1-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a41a1-619">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-619">See below.</span></span> | <span data-ttu-id="a41a1-620">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a41a1-621">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-621">See below.</span></span> | <span data-ttu-id="a41a1-622">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a41a1-623">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a41a1-624">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-624">Option</span></span> | <span data-ttu-id="a41a1-625">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-625">Default Value</span></span> | <span data-ttu-id="a41a1-626">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a41a1-627">90秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-627">90 seconds</span></span> | <span data-ttu-id="a41a1-628">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="a41a1-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a41a1-629">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a41a1-630">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a41a1-631">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-631">Option</span></span> | <span data-ttu-id="a41a1-632">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-632">Default Value</span></span> | <span data-ttu-id="a41a1-633">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a41a1-634">5 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-634">5 seconds</span></span> | <span data-ttu-id="a41a1-635">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a41a1-636">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a41a1-637">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a41a1-638">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-638">Configure client options</span></span>

<span data-ttu-id="a41a1-639">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="a41a1-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a41a1-640">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a41a1-641">ログの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-641">Configure logging</span></span>

<span data-ttu-id="a41a1-642">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a41a1-643">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a41a1-644">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a41a1-645">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a41a1-646">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a41a1-647">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a41a1-648">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a41a1-649">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a41a1-650">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a41a1-651">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a41a1-652">ログ記録を完全に無効にするには、`configureLogging`メソッドで`signalR.LogLevel.None`を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a41a1-653">ログ記録の詳細については、 [SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a41a1-654">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a41a1-655">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a41a1-656">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a41a1-657">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a41a1-658">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a41a1-659">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-659">Configure allowed transports</span></span>

<span data-ttu-id="a41a1-660">SignalR によって使用されるトランスポートは、`WithUrl` 呼び出し (JavaScript では`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a41a1-661">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a41a1-662">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a41a1-662">All transports are enabled by default.</span></span>

<span data-ttu-id="a41a1-663">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a41a1-664">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a41a1-665">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-665">Configure bearer authentication</span></span>

<span data-ttu-id="a41a1-666">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript では`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a41a1-667">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="a41a1-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a41a1-668">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a41a1-669">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a41a1-670">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a41a1-671">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a41a1-672">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a41a1-673">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a41a1-674">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a41a1-675">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="a41a1-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a41a1-676">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a41a1-677">.NET</span><span class="sxs-lookup"><span data-stu-id="a41a1-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a41a1-678">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-678">Option</span></span> | <span data-ttu-id="a41a1-679">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-679">Default value</span></span> | <span data-ttu-id="a41a1-680">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a41a1-681">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-682">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-682">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-683">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a41a1-684">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-685">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a41a1-686">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-686">15 seconds</span></span> | <span data-ttu-id="a41a1-687">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="a41a1-688">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a41a1-689">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-690">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a41a1-691">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a41a1-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a41a1-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a41a1-693">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-693">Option</span></span> | <span data-ttu-id="a41a1-694">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-694">Default value</span></span> | <span data-ttu-id="a41a1-695">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a41a1-696">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-697">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-697">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-698">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a41a1-699">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-700">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a41a1-701">Java</span><span class="sxs-lookup"><span data-stu-id="a41a1-701">Java</span></span>](#tab/java)

| <span data-ttu-id="a41a1-702">オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-702">Option</span></span> | <span data-ttu-id="a41a1-703">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-703">Default value</span></span> | <span data-ttu-id="a41a1-704">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a41a1-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a41a1-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a41a1-706">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a41a1-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a41a1-707">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-707">Timeout for server activity.</span></span> <span data-ttu-id="a41a1-708">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a41a1-709">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a41a1-710">推奨値は、サーバーの `KeepAliveInterval` 値の少なくとも2倍の数で、ping が到着するまでの時間を考慮します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a41a1-711">15 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-711">15 seconds</span></span> | <span data-ttu-id="a41a1-712">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="a41a1-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="a41a1-713">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a41a1-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a41a1-714">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="a41a1-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a41a1-715">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a41a1-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a41a1-716">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="a41a1-716">Configure additional options</span></span>

<span data-ttu-id="a41a1-717">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="a41a1-718">.NET</span><span class="sxs-lookup"><span data-stu-id="a41a1-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a41a1-719">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-719">.NET Option</span></span> |  <span data-ttu-id="a41a1-720">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-720">Default value</span></span> | <span data-ttu-id="a41a1-721">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a41a1-722">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a41a1-723">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-724">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-725">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a41a1-726">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-726">Empty</span></span> | <span data-ttu-id="a41a1-727">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a41a1-728">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-728">Empty</span></span> | <span data-ttu-id="a41a1-729">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="a41a1-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a41a1-730">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-730">Empty</span></span> | <span data-ttu-id="a41a1-731">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="a41a1-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a41a1-732">5 秒</span><span class="sxs-lookup"><span data-stu-id="a41a1-732">5 seconds</span></span> | <span data-ttu-id="a41a1-733">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-733">WebSockets only.</span></span> <span data-ttu-id="a41a1-734">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="a41a1-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a41a1-735">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a41a1-736">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-736">Empty</span></span> | <span data-ttu-id="a41a1-737">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a41a1-738">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a41a1-739">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="a41a1-740">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a41a1-741">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a41a1-742">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="a41a1-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a41a1-743">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a41a1-744">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a41a1-745">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a41a1-746">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="a41a1-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a41a1-747">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="a41a1-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="a41a1-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a41a1-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a41a1-749">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-749">JavaScript Option</span></span> | <span data-ttu-id="a41a1-750">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-750">Default Value</span></span> | <span data-ttu-id="a41a1-751">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a41a1-752">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a41a1-753">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-754">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-755">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="a41a1-756">Java</span><span class="sxs-lookup"><span data-stu-id="a41a1-756">Java</span></span>](#tab/java)

| <span data-ttu-id="a41a1-757">Java オプション</span><span class="sxs-lookup"><span data-stu-id="a41a1-757">Java Option</span></span> | <span data-ttu-id="a41a1-758">既定値</span><span class="sxs-lookup"><span data-stu-id="a41a1-758">Default Value</span></span> | <span data-ttu-id="a41a1-759">説明</span><span class="sxs-lookup"><span data-stu-id="a41a1-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a41a1-760">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="a41a1-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a41a1-761">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="a41a1-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a41a1-762">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a41a1-763">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a41a1-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a41a1-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a41a1-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a41a1-765">空</span><span class="sxs-lookup"><span data-stu-id="a41a1-765">Empty</span></span> | <span data-ttu-id="a41a1-766">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="a41a1-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a41a1-767">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a41a1-768">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="a41a1-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a41a1-769">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a41a1-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a41a1-770">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a41a1-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end