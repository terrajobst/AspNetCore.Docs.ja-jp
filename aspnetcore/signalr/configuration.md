---
title: SignalR 構成の ASP.NET Core
author: bradygaster
description: ASP.NET Core SignalR アプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 682cc36216a4dc9b38c87b4f67100ab565a70e5c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963980"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="563c9-103">SignalR 構成の ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="563c9-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="563c9-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="563c9-105">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="563c9-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="563c9-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="563c9-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="563c9-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="563c9-108">`AddJsonProtocol` は、`Startup.ConfigureServices`の[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="563c9-109">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="563c9-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="563c9-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数と戻り値のシリアル化を構成するために使用できる `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="563c9-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="563c9-111">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="563c9-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="563c9-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="563c9-113">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="563c9-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="563c9-114">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="563c9-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="563c9-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="563c9-116">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="563c9-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="563c9-117">`System.Text.Json`でサポートされていない `Newtonsoft.Json` の機能が必要な場合は、「 [Newtonsoft. Json への切り替え」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="563c9-118">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、`Startup.ConfigureServices` メソッドで[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-118">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="563c9-119">`AddJsonProtocol` メソッドは、`options` オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="563c9-119">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="563c9-120">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と戻り値のシリアル化を構成するために使用できる JSON.NET `JsonSerializerSettings` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="563c9-120">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="563c9-121">詳細については、 [JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-121">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="563c9-122">たとえば、"キャメルケース" という既定の名前の代わりに "" という名前のプロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="563c9-122">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="563c9-123">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ `AddJsonProtocol` 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="563c9-123">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="563c9-124">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-124">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="563c9-125">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="563c9-125">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

::: moniker-end

### <a name="messagepack-serialization-options"></a><span data-ttu-id="563c9-126">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-126">MessagePack serialization options</span></span>

<span data-ttu-id="563c9-127">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-127">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="563c9-128">詳細については[、「SignalRの Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-128">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="563c9-129">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="563c9-129">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="563c9-130">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="563c9-130">Configure server options</span></span>

<span data-ttu-id="563c9-131">次の表では、SignalR ハブを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="563c9-131">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="563c9-132">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-132">Option</span></span> | <span data-ttu-id="563c9-133">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-133">Default Value</span></span> | <span data-ttu-id="563c9-134">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="563c9-135">30 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-135">30 seconds</span></span> | <span data-ttu-id="563c9-136">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="563c9-136">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="563c9-137">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="563c9-137">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="563c9-138">推奨値は、`KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="563c9-138">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="563c9-139">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-139">15 seconds</span></span> | <span data-ttu-id="563c9-140">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="563c9-140">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="563c9-141">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-141">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-142">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-142">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="563c9-143">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-143">15 seconds</span></span> | <span data-ttu-id="563c9-144">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-144">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="563c9-145">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="563c9-145">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="563c9-146">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="563c9-146">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="563c9-147">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="563c9-147">All installed protocols</span></span> | <span data-ttu-id="563c9-148">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="563c9-148">Protocols supported by this hub.</span></span> <span data-ttu-id="563c9-149">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="563c9-149">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="563c9-150">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-150">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="563c9-151">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="563c9-151">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="563c9-152">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="563c9-152">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="563c9-153">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="563c9-153">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="563c9-154">32 KB</span><span class="sxs-lookup"><span data-stu-id="563c9-154">32 KB</span></span> | <span data-ttu-id="563c9-155">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="563c9-155">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="563c9-156">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-156">Option</span></span> | <span data-ttu-id="563c9-157">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-157">Default Value</span></span> | <span data-ttu-id="563c9-158">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="563c9-159">30 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-159">30 seconds</span></span> | <span data-ttu-id="563c9-160">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="563c9-160">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="563c9-161">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="563c9-161">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="563c9-162">推奨値は、`KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="563c9-162">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="563c9-163">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-163">15 seconds</span></span> | <span data-ttu-id="563c9-164">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="563c9-164">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="563c9-165">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-165">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-166">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-166">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="563c9-167">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-167">15 seconds</span></span> | <span data-ttu-id="563c9-168">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-168">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="563c9-169">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="563c9-169">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="563c9-170">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="563c9-170">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="563c9-171">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="563c9-171">All installed protocols</span></span> | <span data-ttu-id="563c9-172">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="563c9-172">Protocols supported by this hub.</span></span> <span data-ttu-id="563c9-173">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="563c9-173">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="563c9-174">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-174">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="563c9-175">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="563c9-175">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="563c9-176">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-176">Option</span></span> | <span data-ttu-id="563c9-177">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-177">Default Value</span></span> | <span data-ttu-id="563c9-178">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-178">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="563c9-179">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-179">15 seconds</span></span> | <span data-ttu-id="563c9-180">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="563c9-180">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="563c9-181">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-181">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-182">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-182">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="563c9-183">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-183">15 seconds</span></span> | <span data-ttu-id="563c9-184">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-184">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="563c9-185">`KeepAliveInterval`を変更する場合は、クライアントの `ServerTimeout`/`serverTimeoutInMilliseconds` 設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="563c9-185">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="563c9-186">推奨される `ServerTimeout`/`serverTimeoutInMilliseconds` 値は `KeepAliveInterval` 値の倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="563c9-186">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="563c9-187">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="563c9-187">All installed protocols</span></span> | <span data-ttu-id="563c9-188">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="563c9-188">Protocols supported by this hub.</span></span> <span data-ttu-id="563c9-189">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="563c9-189">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="563c9-190">`true`した場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-190">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="563c9-191">既定値は `false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="563c9-191">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="563c9-192">オプションは、`Startup.ConfigureServices`の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-192">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="563c9-193">1つのハブのオプションは `AddSignalR` で提供されるグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-193">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="563c9-194">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-194">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="563c9-195">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="563c9-195">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="563c9-196">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-196">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="563c9-197">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="563c9-197">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="563c9-198">これらのオプションは、`Startup.Configure`で[Maphub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-198">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

::: moniker-end

<span data-ttu-id="563c9-199">次の表では、ASP.NET Core SignalRの詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="563c9-199">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="563c9-200">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-200">Option</span></span> | <span data-ttu-id="563c9-201">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-201">Default Value</span></span> | <span data-ttu-id="563c9-202">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-202">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="563c9-203">32 KB</span><span class="sxs-lookup"><span data-stu-id="563c9-203">32 KB</span></span> | <span data-ttu-id="563c9-204">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="563c9-204">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="563c9-205">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-205">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="563c9-206">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="563c9-206">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="563c9-207">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="563c9-207">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="563c9-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="563c9-208">32 KB</span></span> | <span data-ttu-id="563c9-209">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="563c9-209">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="563c9-210">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-210">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="563c9-211">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="563c9-211">All Transports are enabled.</span></span> | <span data-ttu-id="563c9-212">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="563c9-212">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="563c9-213">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-213">See below.</span></span> | <span data-ttu-id="563c9-214">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="563c9-214">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="563c9-215">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-215">See below.</span></span> | <span data-ttu-id="563c9-216">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="563c9-216">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="563c9-217">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-217">Option</span></span> | <span data-ttu-id="563c9-218">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-218">Default Value</span></span> | <span data-ttu-id="563c9-219">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-219">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="563c9-220">32 KB</span><span class="sxs-lookup"><span data-stu-id="563c9-220">32 KB</span></span> | <span data-ttu-id="563c9-221">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="563c9-221">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="563c9-222">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-222">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="563c9-223">ハブクラスに適用された `Authorize` の属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="563c9-223">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="563c9-224">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="563c9-224">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="563c9-225">32 KB</span><span class="sxs-lookup"><span data-stu-id="563c9-225">32 KB</span></span> | <span data-ttu-id="563c9-226">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="563c9-226">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="563c9-227">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-227">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="563c9-228">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="563c9-228">All Transports are enabled.</span></span> | <span data-ttu-id="563c9-229">クライアントが接続に使用できるトランスポートを制限できる、`HttpTransportType` 値のビットフラグ列挙型。</span><span class="sxs-lookup"><span data-stu-id="563c9-229">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="563c9-230">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-230">See below.</span></span> | <span data-ttu-id="563c9-231">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="563c9-231">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="563c9-232">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-232">See below.</span></span> | <span data-ttu-id="563c9-233">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="563c9-233">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="563c9-234">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="563c9-234">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="563c9-235">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-235">Option</span></span> | <span data-ttu-id="563c9-236">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-236">Default Value</span></span> | <span data-ttu-id="563c9-237">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-237">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="563c9-238">90秒</span><span class="sxs-lookup"><span data-stu-id="563c9-238">90 seconds</span></span> | <span data-ttu-id="563c9-239">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="563c9-239">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="563c9-240">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="563c9-240">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="563c9-241">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="563c9-241">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="563c9-242">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-242">Option</span></span> | <span data-ttu-id="563c9-243">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-243">Default Value</span></span> | <span data-ttu-id="563c9-244">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="563c9-245">5 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-245">5 seconds</span></span> | <span data-ttu-id="563c9-246">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="563c9-246">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="563c9-247">`Sec-WebSocket-Protocol` ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="563c9-247">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="563c9-248">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="563c9-248">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="563c9-249">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="563c9-249">Configure client options</span></span>

<span data-ttu-id="563c9-250">クライアントオプションは `HubConnectionBuilder` の種類で構成できます (.NET および JavaScript クライアントで使用できます)。</span><span class="sxs-lookup"><span data-stu-id="563c9-250">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="563c9-251">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="563c9-251">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="563c9-252">ログの構成</span><span class="sxs-lookup"><span data-stu-id="563c9-252">Configure logging</span></span>

<span data-ttu-id="563c9-253">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-253">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="563c9-254">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-254">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="563c9-255">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-255">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="563c9-256">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-256">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="563c9-257">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-257">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="563c9-258">たとえば、コンソールのログ記録を有効にするには、`Microsoft.Extensions.Logging.Console` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="563c9-258">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="563c9-259">`AddConsole` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="563c9-259">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="563c9-260">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="563c9-260">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="563c9-261">生成するログメッセージの最小レベルを示す `LogLevel` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-261">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="563c9-262">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="563c9-262">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="563c9-263">`LogLevel` 値の代わりに、ログレベル名を表す `string` の値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="563c9-263">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="563c9-264">これは、`LogLevel` 定数にアクセスできない環境で SignalR ログを構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="563c9-264">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="563c9-265">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="563c9-265">The following table lists the available log levels.</span></span> <span data-ttu-id="563c9-266">`configureLogging` に指定する値は、ログに記録される**最小**ログレベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-266">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="563c9-267">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-267">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="563c9-268">文字列型</span><span class="sxs-lookup"><span data-stu-id="563c9-268">String</span></span>                      | <span data-ttu-id="563c9-269">LogLevel</span><span class="sxs-lookup"><span data-stu-id="563c9-269">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="563c9-270">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="563c9-270">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="563c9-271">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="563c9-271">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="563c9-272">完全にログ記録を無効にするには、`configureLogging` 方法で `signalR.LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-272">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="563c9-273">ログ記録の詳細については、 [SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-273">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="563c9-274">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="563c9-274">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="563c9-275">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="563c9-275">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="563c9-276">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="563c9-276">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="563c9-277">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="563c9-277">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="563c9-278">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="563c9-278">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="563c9-279">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="563c9-279">Configure allowed transports</span></span>

<span data-ttu-id="563c9-280">SignalR によって使用されるトランスポートは、`WithUrl` 呼び出し (JavaScript では`withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-280">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="563c9-281">`HttpTransportType` の値のビットごとの OR を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-281">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="563c9-282">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="563c9-282">All transports are enabled by default.</span></span>

<span data-ttu-id="563c9-283">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="563c9-283">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="563c9-284">JavaScript クライアントでは、`withUrl`に提供される options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="563c9-284">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="563c9-285">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="563c9-285">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="563c9-286">Java クライアントでは、トランスポートは、`HttpHubConnectionBuilder`の `withTransport` メソッドを使用して選択されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-286">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="563c9-287">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-287">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="563c9-288">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="563c9-288">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="563c9-289">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="563c9-289">Configure bearer authentication</span></span>

<span data-ttu-id="563c9-290">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript では`accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-290">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="563c9-291">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (`Bearer`の種類の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="563c9-291">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="563c9-292">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="563c9-292">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="563c9-293">このような場合、アクセストークンは `access_token`クエリ文字列値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-293">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="563c9-294">.NET クライアントでは、`WithUrl`のオプションデリゲートを使用して、`AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-294">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="563c9-295">JavaScript クライアントでは、`withUrl`の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-295">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="563c9-296">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-296">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="563c9-297">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="563c9-298">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-298">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="563c9-299">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="563c9-299">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="563c9-300">タイムアウトとキープアライブの動作を構成するための追加オプションは、`HubConnection` オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-300">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="563c9-301">.NET</span><span class="sxs-lookup"><span data-stu-id="563c9-301">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="563c9-302">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-302">Option</span></span> | <span data-ttu-id="563c9-303">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-303">Default value</span></span> | <span data-ttu-id="563c9-304">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-304">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="563c9-305">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-305">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="563c9-306">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-306">Timeout for server activity.</span></span> <span data-ttu-id="563c9-307">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-307">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="563c9-308">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-308">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="563c9-309">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="563c9-309">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="563c9-310">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-310">15 seconds</span></span> | <span data-ttu-id="563c9-311">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-311">Timeout for initial server handshake.</span></span> <span data-ttu-id="563c9-312">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-312">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="563c9-313">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-313">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-314">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-314">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="563c9-315">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-315">15 seconds</span></span> | <span data-ttu-id="563c9-316">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-316">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="563c9-317">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="563c9-317">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="563c9-318">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="563c9-318">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="563c9-319">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-319">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="563c9-320">JavaScript</span><span class="sxs-lookup"><span data-stu-id="563c9-320">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="563c9-321">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-321">Option</span></span> | <span data-ttu-id="563c9-322">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-322">Default value</span></span> | <span data-ttu-id="563c9-323">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-323">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="563c9-324">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-324">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="563c9-325">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-325">Timeout for server activity.</span></span> <span data-ttu-id="563c9-326">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-326">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="563c9-327">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-327">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="563c9-328">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="563c9-328">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="563c9-329">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-329">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="563c9-330">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-330">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="563c9-331">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="563c9-331">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="563c9-332">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="563c9-332">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="563c9-333">Java</span><span class="sxs-lookup"><span data-stu-id="563c9-333">Java</span></span>](#tab/java)

| <span data-ttu-id="563c9-334">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-334">Option</span></span> | <span data-ttu-id="563c9-335">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-335">Default value</span></span> | <span data-ttu-id="563c9-336">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-336">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="563c9-337">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="563c9-337">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="563c9-338">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-338">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="563c9-339">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-339">Timeout for server activity.</span></span> <span data-ttu-id="563c9-340">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-340">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="563c9-341">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-341">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="563c9-342">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="563c9-342">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="563c9-343">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-343">15 seconds</span></span> | <span data-ttu-id="563c9-344">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-344">Timeout for initial server handshake.</span></span> <span data-ttu-id="563c9-345">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-345">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="563c9-346">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-346">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-347">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-347">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="563c9-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="563c9-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="563c9-349">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-349">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="563c9-350">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-350">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="563c9-351">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="563c9-351">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="563c9-352">クライアントがサーバーで設定された `ClientTimeoutInterval` にメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="563c9-352">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="563c9-353">.NET</span><span class="sxs-lookup"><span data-stu-id="563c9-353">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="563c9-354">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-354">Option</span></span> | <span data-ttu-id="563c9-355">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-355">Default value</span></span> | <span data-ttu-id="563c9-356">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-356">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="563c9-357">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-357">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="563c9-358">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-358">Timeout for server activity.</span></span> <span data-ttu-id="563c9-359">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-359">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="563c9-360">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-360">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="563c9-361">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="563c9-361">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="563c9-362">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-362">15 seconds</span></span> | <span data-ttu-id="563c9-363">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-363">Timeout for initial server handshake.</span></span> <span data-ttu-id="563c9-364">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed` イベント (JavaScript では`onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-364">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="563c9-365">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-365">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-366">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-366">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="563c9-367">.NET クライアントでは、タイムアウト値は `TimeSpan` 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-367">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="563c9-368">JavaScript</span><span class="sxs-lookup"><span data-stu-id="563c9-368">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="563c9-369">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-369">Option</span></span> | <span data-ttu-id="563c9-370">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-370">Default value</span></span> | <span data-ttu-id="563c9-371">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-371">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="563c9-372">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-372">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="563c9-373">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-373">Timeout for server activity.</span></span> <span data-ttu-id="563c9-374">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onclose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-374">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="563c9-375">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-375">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="563c9-376">推奨値は、ping が到着するまでの時間を考慮して、サーバーの `KeepAliveInterval` 値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="563c9-376">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="563c9-377">Java</span><span class="sxs-lookup"><span data-stu-id="563c9-377">Java</span></span>](#tab/java)

| <span data-ttu-id="563c9-378">オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-378">Option</span></span> | <span data-ttu-id="563c9-379">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-379">Default value</span></span> | <span data-ttu-id="563c9-380">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-380">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="563c9-381">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="563c9-381">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="563c9-382">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="563c9-382">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="563c9-383">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-383">Timeout for server activity.</span></span> <span data-ttu-id="563c9-384">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-384">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="563c9-385">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="563c9-385">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="563c9-386">推奨値は、サーバーの `KeepAliveInterval` 値の少なくとも2倍の数で、ping が到着するまでの時間を考慮します。</span><span class="sxs-lookup"><span data-stu-id="563c9-386">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="563c9-387">15 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-387">15 seconds</span></span> | <span data-ttu-id="563c9-388">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="563c9-388">Timeout for initial server handshake.</span></span> <span data-ttu-id="563c9-389">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`onClose` イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="563c9-389">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="563c9-390">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="563c9-390">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="563c9-391">ハンドシェイクプロセスの詳細については、「 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="563c9-391">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="563c9-392">追加のオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="563c9-392">Configure additional options</span></span>

<span data-ttu-id="563c9-393">追加のオプションは `HubConnectionBuilder` または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で `WithUrl` (JavaScript の`withUrl`) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-393">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="563c9-394">.NET</span><span class="sxs-lookup"><span data-stu-id="563c9-394">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="563c9-395">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-395">.NET Option</span></span> |  <span data-ttu-id="563c9-396">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-396">Default value</span></span> | <span data-ttu-id="563c9-397">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-397">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="563c9-398">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="563c9-398">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="563c9-399">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-399">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="563c9-400">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="563c9-400">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="563c9-401">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="563c9-401">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="563c9-402">Empty</span><span class="sxs-lookup"><span data-stu-id="563c9-402">Empty</span></span> | <span data-ttu-id="563c9-403">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="563c9-403">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="563c9-404">Empty</span><span class="sxs-lookup"><span data-stu-id="563c9-404">Empty</span></span> | <span data-ttu-id="563c9-405">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="563c9-405">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="563c9-406">Empty</span><span class="sxs-lookup"><span data-stu-id="563c9-406">Empty</span></span> | <span data-ttu-id="563c9-407">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="563c9-407">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="563c9-408">5 秒</span><span class="sxs-lookup"><span data-stu-id="563c9-408">5 seconds</span></span> | <span data-ttu-id="563c9-409">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="563c9-409">WebSockets only.</span></span> <span data-ttu-id="563c9-410">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="563c9-410">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="563c9-411">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-411">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="563c9-412">Empty</span><span class="sxs-lookup"><span data-stu-id="563c9-412">Empty</span></span> | <span data-ttu-id="563c9-413">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="563c9-413">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="563c9-414">HTTP 要求の送信に使用される `HttpMessageHandler` を構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="563c9-414">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="563c9-415">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="563c9-415">Not used for WebSocket connections.</span></span> <span data-ttu-id="563c9-416">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="563c9-416">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="563c9-417">既定値の設定を変更して返すか、新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="563c9-417">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="563c9-418">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="563c9-418">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="563c9-419">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="563c9-419">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="563c9-420">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="563c9-420">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="563c9-421">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="563c9-421">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="563c9-422">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="563c9-422">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="563c9-423">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="563c9-423">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="563c9-424">JavaScript</span><span class="sxs-lookup"><span data-stu-id="563c9-424">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="563c9-425">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-425">JavaScript Option</span></span> | <span data-ttu-id="563c9-426">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-426">Default Value</span></span> | <span data-ttu-id="563c9-427">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-427">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="563c9-428">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="563c9-428">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="563c9-429">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-429">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="563c9-430">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="563c9-430">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="563c9-431">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="563c9-431">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="563c9-432">Java</span><span class="sxs-lookup"><span data-stu-id="563c9-432">Java</span></span>](#tab/java)

| <span data-ttu-id="563c9-433">Java オプション</span><span class="sxs-lookup"><span data-stu-id="563c9-433">Java Option</span></span> | <span data-ttu-id="563c9-434">既定値</span><span class="sxs-lookup"><span data-stu-id="563c9-434">Default Value</span></span> | <span data-ttu-id="563c9-435">説明</span><span class="sxs-lookup"><span data-stu-id="563c9-435">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="563c9-436">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="563c9-436">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="563c9-437">ネゴシエーションの手順をスキップするには、これを `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="563c9-437">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="563c9-438">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="563c9-438">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="563c9-439">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="563c9-439">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="563c9-440">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="563c9-440">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="563c9-441">Empty</span><span class="sxs-lookup"><span data-stu-id="563c9-441">Empty</span></span> | <span data-ttu-id="563c9-442">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="563c9-442">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="563c9-443">.NET クライアントでは、これらのオプションは `WithUrl`に提供されるオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-443">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="563c9-444">JavaScript クライアントでは、これらのオプションは `withUrl`に提供される JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="563c9-444">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="563c9-445">Java クライアントでは、これらのオプションはから返された `HttpHubConnectionBuilder` のメソッドを使用して構成でき `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="563c9-445">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="563c9-446">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="563c9-446">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
