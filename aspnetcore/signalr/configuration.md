---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core SignalR アプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 475d9664c588c06bfcd816959be8a425ee01c023
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68915079"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="09938-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="09938-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="09938-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="09938-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="09938-105">ASP.NET Core SignalR は、メッセージをエンコードするための2つのプロトコルをサポートしています。[JSON](https://www.json.org/)および[messagepack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="09938-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="09938-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="09938-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="09938-107">JSON のシリアル化は、 [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用してサーバー上で構成できます。これは `Startup.ConfigureServices` メソッドの [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="09938-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="09938-108">メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="09938-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="09938-109">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と`JsonSerializerSettings`戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="09938-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="09938-110">詳細については、 [JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="09938-111">たとえば、"キャメルケース" という既定の名前の代わりに "" プロパティ名を使用するようにシリアライザーを構成するには、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="09938-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="09938-112">.Net クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="09938-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="09938-113">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="09938-114">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="09938-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="09938-115">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="09938-115">MessagePack serialization options</span></span>

<span data-ttu-id="09938-116">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="09938-117">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="09938-118">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="09938-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="09938-119">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="09938-119">Configure server options</span></span>

<span data-ttu-id="09938-120">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="09938-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="09938-121">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-121">Option</span></span> | <span data-ttu-id="09938-122">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-122">Default Value</span></span> | <span data-ttu-id="09938-123">説明</span><span class="sxs-lookup"><span data-stu-id="09938-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="09938-124">30 秒</span><span class="sxs-lookup"><span data-stu-id="09938-124">30 seconds</span></span> | <span data-ttu-id="09938-125">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="09938-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="09938-126">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="09938-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="09938-127">推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="09938-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="09938-128">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-128">15 seconds</span></span> | <span data-ttu-id="09938-129">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="09938-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="09938-130">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-131">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="09938-132">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-132">15 seconds</span></span> | <span data-ttu-id="09938-133">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="09938-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="09938-134">変更`KeepAliveInterval`する場合は、 `ServerTimeout`クライアントの`serverTimeoutInMilliseconds`設定を/変更します。</span><span class="sxs-lookup"><span data-stu-id="09938-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="09938-135">推奨`ServerTimeout` / `KeepAliveInterval`値は、値の倍精度浮動小数点数です。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="09938-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="09938-136">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="09938-136">All installed protocols</span></span> | <span data-ttu-id="09938-137">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="09938-137">Protocols supported by this hub.</span></span> <span data-ttu-id="09938-138">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="09938-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="09938-139">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="09938-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="09938-140">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="09938-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="09938-141">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="09938-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="09938-142">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="09938-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="09938-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="09938-143">32 KB</span></span> | <span data-ttu-id="09938-144">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="09938-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="09938-145">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-145">Option</span></span> | <span data-ttu-id="09938-146">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-146">Default Value</span></span> | <span data-ttu-id="09938-147">説明</span><span class="sxs-lookup"><span data-stu-id="09938-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="09938-148">30 秒</span><span class="sxs-lookup"><span data-stu-id="09938-148">30 seconds</span></span> | <span data-ttu-id="09938-149">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="09938-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="09938-150">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="09938-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="09938-151">推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="09938-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="09938-152">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-152">15 seconds</span></span> | <span data-ttu-id="09938-153">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="09938-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="09938-154">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-155">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="09938-156">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-156">15 seconds</span></span> | <span data-ttu-id="09938-157">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="09938-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="09938-158">変更`KeepAliveInterval`する場合は、 `ServerTimeout`クライアントの`serverTimeoutInMilliseconds`設定を/変更します。</span><span class="sxs-lookup"><span data-stu-id="09938-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="09938-159">推奨`ServerTimeout` / `KeepAliveInterval`値は、値の倍精度浮動小数点数です。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="09938-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="09938-160">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="09938-160">All installed protocols</span></span> | <span data-ttu-id="09938-161">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="09938-161">Protocols supported by this hub.</span></span> <span data-ttu-id="09938-162">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="09938-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="09938-163">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="09938-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="09938-164">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="09938-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="09938-165">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-165">Option</span></span> | <span data-ttu-id="09938-166">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-166">Default Value</span></span> | <span data-ttu-id="09938-167">説明</span><span class="sxs-lookup"><span data-stu-id="09938-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="09938-168">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-168">15 seconds</span></span> | <span data-ttu-id="09938-169">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="09938-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="09938-170">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-171">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="09938-172">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-172">15 seconds</span></span> | <span data-ttu-id="09938-173">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="09938-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="09938-174">変更`KeepAliveInterval`する場合は、 `ServerTimeout`クライアントの`serverTimeoutInMilliseconds`設定を/変更します。</span><span class="sxs-lookup"><span data-stu-id="09938-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="09938-175">推奨`ServerTimeout` / `KeepAliveInterval`値は、値の倍精度浮動小数点数です。 `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="09938-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="09938-176">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="09938-176">All installed protocols</span></span> | <span data-ttu-id="09938-177">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="09938-177">Protocols supported by this hub.</span></span> <span data-ttu-id="09938-178">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="09938-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="09938-179">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="09938-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="09938-180">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="09938-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="09938-181">オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="09938-182">1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書きします。また、次のものを使用し<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>て構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="09938-183">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="09938-183">Advanced HTTP configuration options</span></span>

<span data-ttu-id="09938-184">トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="09938-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="09938-185">これらのオプションは、で`Startup.Configure` [maphub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="09938-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="09938-186">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="09938-186">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="09938-187">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-187">Option</span></span> | <span data-ttu-id="09938-188">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-188">Default Value</span></span> | <span data-ttu-id="09938-189">説明</span><span class="sxs-lookup"><span data-stu-id="09938-189">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="09938-190">32 KB</span><span class="sxs-lookup"><span data-stu-id="09938-190">32 KB</span></span> | <span data-ttu-id="09938-191">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="09938-191">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="09938-192">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09938-192">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="09938-193">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="09938-193">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="09938-194">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="09938-194">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="09938-195">32 KB</span><span class="sxs-lookup"><span data-stu-id="09938-195">32 KB</span></span> | <span data-ttu-id="09938-196">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="09938-196">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="09938-197">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09938-197">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="09938-198">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="09938-198">All Transports are enabled.</span></span> | <span data-ttu-id="09938-199">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="09938-199">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="09938-200">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-200">See below.</span></span> | <span data-ttu-id="09938-201">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="09938-201">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="09938-202">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-202">See below.</span></span> | <span data-ttu-id="09938-203">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="09938-203">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="09938-204">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-204">Option</span></span> | <span data-ttu-id="09938-205">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-205">Default Value</span></span> | <span data-ttu-id="09938-206">説明</span><span class="sxs-lookup"><span data-stu-id="09938-206">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="09938-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="09938-207">32 KB</span></span> | <span data-ttu-id="09938-208">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="09938-208">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="09938-209">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09938-209">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="09938-210">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="09938-210">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="09938-211">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="09938-211">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="09938-212">32 KB</span><span class="sxs-lookup"><span data-stu-id="09938-212">32 KB</span></span> | <span data-ttu-id="09938-213">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="09938-213">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="09938-214">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09938-214">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="09938-215">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="09938-215">All Transports are enabled.</span></span> | <span data-ttu-id="09938-216">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="09938-216">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="09938-217">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-217">See below.</span></span> | <span data-ttu-id="09938-218">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="09938-218">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="09938-219">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-219">See below.</span></span> | <span data-ttu-id="09938-220">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="09938-220">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="09938-221">長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="09938-221">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="09938-222">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-222">Option</span></span> | <span data-ttu-id="09938-223">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-223">Default Value</span></span> | <span data-ttu-id="09938-224">説明</span><span class="sxs-lookup"><span data-stu-id="09938-224">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="09938-225">90秒</span><span class="sxs-lookup"><span data-stu-id="09938-225">90 seconds</span></span> | <span data-ttu-id="09938-226">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="09938-226">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="09938-227">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="09938-227">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="09938-228">WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="09938-228">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="09938-229">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-229">Option</span></span> | <span data-ttu-id="09938-230">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-230">Default Value</span></span> | <span data-ttu-id="09938-231">説明</span><span class="sxs-lookup"><span data-stu-id="09938-231">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="09938-232">5 秒</span><span class="sxs-lookup"><span data-stu-id="09938-232">5 seconds</span></span> | <span data-ttu-id="09938-233">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="09938-233">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="09938-234">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="09938-234">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="09938-235">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="09938-235">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="09938-236">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="09938-236">Configure client options</span></span>

<span data-ttu-id="09938-237">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-237">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="09938-238">Java クライアントでも使用できますが`HttpHubConnectionBuilder` 、サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="09938-238">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="09938-239">ログの構成</span><span class="sxs-lookup"><span data-stu-id="09938-239">Configure logging</span></span>

<span data-ttu-id="09938-240">ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="09938-240">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="09938-241">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="09938-241">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="09938-242">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-242">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="09938-243">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-243">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="09938-244">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-244">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="09938-245">たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="09938-245">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="09938-246">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="09938-246">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="09938-247">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="09938-247">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="09938-248">生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="09938-248">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="09938-249">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="09938-249">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="09938-250">`LogLevel`値の代わりに、ログレベル名を表す`string`値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="09938-250">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="09938-251">これは、 `LogLevel`定数にアクセスできない環境で SignalR のログ記録を構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="09938-251">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="09938-252">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="09938-252">The following table lists the available log levels.</span></span> <span data-ttu-id="09938-253">ログに記録される`configureLogging` **最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="09938-253">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="09938-254">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="09938-254">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="09938-255">String</span><span class="sxs-lookup"><span data-stu-id="09938-255">String</span></span>                      | <span data-ttu-id="09938-256">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="09938-256">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="09938-257">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="09938-257">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="09938-258">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="09938-258">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="09938-259">完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。</span><span class="sxs-lookup"><span data-stu-id="09938-259">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="09938-260">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-260">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="09938-261">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="09938-261">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="09938-262">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="09938-262">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="09938-263">次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="09938-263">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="09938-264">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="09938-264">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="09938-265">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="09938-265">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="09938-266">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="09938-266">Configure allowed transports</span></span>

<span data-ttu-id="09938-267">SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-267">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="09938-268">の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="09938-268">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="09938-269">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="09938-269">All transports are enabled by default.</span></span>

<span data-ttu-id="09938-270">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="09938-270">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="09938-271">JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="09938-271">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="09938-272">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="09938-272">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="09938-273">Java クライアントでは、 `withTransport` `HttpHubConnectionBuilder`でメソッドを使用してトランスポートを選択します。</span><span class="sxs-lookup"><span data-stu-id="09938-273">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="09938-274">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="09938-274">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="09938-275">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="09938-275">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="09938-276">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="09938-276">Configure bearer authentication</span></span>

<span data-ttu-id="09938-277">SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="09938-277">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="09938-278">.Net クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="09938-278">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="09938-279">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="09938-279">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="09938-280">このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。</span><span class="sxs-lookup"><span data-stu-id="09938-280">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="09938-281">.Net クライアント`AccessTokenProvider`では、の`WithUrl`オプションデリゲートを使用してオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="09938-281">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="09938-282">JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="09938-282">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="09938-283">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-283">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="09938-284">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="09938-284">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="09938-285">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-285">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="09938-286">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="09938-286">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="09938-287">タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="09938-287">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="09938-288">.NET</span><span class="sxs-lookup"><span data-stu-id="09938-288">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="09938-289">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-289">Option</span></span> | <span data-ttu-id="09938-290">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-290">Default value</span></span> | <span data-ttu-id="09938-291">説明</span><span class="sxs-lookup"><span data-stu-id="09938-291">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="09938-292">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-292">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="09938-293">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-293">Timeout for server activity.</span></span> <span data-ttu-id="09938-294">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-294">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="09938-295">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-295">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="09938-296">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="09938-296">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="09938-297">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-297">15 seconds</span></span> | <span data-ttu-id="09938-298">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-298">Timeout for initial server handshake.</span></span> <span data-ttu-id="09938-299">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-299">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="09938-300">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-300">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-301">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-301">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="09938-302">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-302">15 seconds</span></span> | <span data-ttu-id="09938-303">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="09938-303">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="09938-304">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="09938-304">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="09938-305">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="09938-305">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="09938-306">.Net クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="09938-306">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="09938-307">JavaScript</span><span class="sxs-lookup"><span data-stu-id="09938-307">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="09938-308">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-308">Option</span></span> | <span data-ttu-id="09938-309">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-309">Default value</span></span> | <span data-ttu-id="09938-310">説明</span><span class="sxs-lookup"><span data-stu-id="09938-310">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="09938-311">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-311">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="09938-312">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-312">Timeout for server activity.</span></span> <span data-ttu-id="09938-313">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-313">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="09938-314">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-314">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="09938-315">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="09938-315">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="09938-316">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-316">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="09938-317">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="09938-317">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="09938-318">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="09938-318">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="09938-319">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="09938-319">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="09938-320">Java</span><span class="sxs-lookup"><span data-stu-id="09938-320">Java</span></span>](#tab/java)

| <span data-ttu-id="09938-321">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-321">Option</span></span> | <span data-ttu-id="09938-322">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-322">Default value</span></span> | <span data-ttu-id="09938-323">説明</span><span class="sxs-lookup"><span data-stu-id="09938-323">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="09938-324">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="09938-324">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="09938-325">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-325">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="09938-326">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-326">Timeout for server activity.</span></span> <span data-ttu-id="09938-327">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-327">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="09938-328">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-328">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="09938-329">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="09938-329">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="09938-330">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-330">15 seconds</span></span> | <span data-ttu-id="09938-331">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-331">Timeout for initial server handshake.</span></span> <span data-ttu-id="09938-332">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-332">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="09938-333">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-333">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-334">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-334">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="09938-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="09938-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="09938-336">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-336">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="09938-337">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="09938-337">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="09938-338">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="09938-338">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="09938-339">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="09938-339">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="09938-340">.NET</span><span class="sxs-lookup"><span data-stu-id="09938-340">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="09938-341">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-341">Option</span></span> | <span data-ttu-id="09938-342">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-342">Default value</span></span> | <span data-ttu-id="09938-343">説明</span><span class="sxs-lookup"><span data-stu-id="09938-343">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="09938-344">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-344">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="09938-345">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-345">Timeout for server activity.</span></span> <span data-ttu-id="09938-346">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-346">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="09938-347">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-347">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="09938-348">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="09938-348">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="09938-349">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-349">15 seconds</span></span> | <span data-ttu-id="09938-350">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-350">Timeout for initial server handshake.</span></span> <span data-ttu-id="09938-351">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-351">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="09938-352">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-352">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-353">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-353">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="09938-354">.Net クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="09938-354">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="09938-355">JavaScript</span><span class="sxs-lookup"><span data-stu-id="09938-355">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="09938-356">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-356">Option</span></span> | <span data-ttu-id="09938-357">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-357">Default value</span></span> | <span data-ttu-id="09938-358">説明</span><span class="sxs-lookup"><span data-stu-id="09938-358">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="09938-359">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-359">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="09938-360">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-360">Timeout for server activity.</span></span> <span data-ttu-id="09938-361">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-361">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="09938-362">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-362">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="09938-363">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="09938-363">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="09938-364">Java</span><span class="sxs-lookup"><span data-stu-id="09938-364">Java</span></span>](#tab/java)

| <span data-ttu-id="09938-365">オプション</span><span class="sxs-lookup"><span data-stu-id="09938-365">Option</span></span> | <span data-ttu-id="09938-366">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-366">Default value</span></span> | <span data-ttu-id="09938-367">説明</span><span class="sxs-lookup"><span data-stu-id="09938-367">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="09938-368">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="09938-368">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="09938-369">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="09938-369">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="09938-370">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-370">Timeout for server activity.</span></span> <span data-ttu-id="09938-371">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-371">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="09938-372">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="09938-372">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="09938-373">推奨値は、サーバーの`KeepAliveInterval`値の少なくとも2倍の数で、ping が到着するまでの時間を確保します。</span><span class="sxs-lookup"><span data-stu-id="09938-373">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="09938-374">15 秒</span><span class="sxs-lookup"><span data-stu-id="09938-374">15 seconds</span></span> | <span data-ttu-id="09938-375">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="09938-375">Timeout for initial server handshake.</span></span> <span data-ttu-id="09938-376">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="09938-376">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="09938-377">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="09938-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="09938-378">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="09938-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="09938-379">追加のオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="09938-379">Configure additional options</span></span>

<span data-ttu-id="09938-380">追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript `HubConnectionBuilder`では) メソッドまたはのさまざまな構成 api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="09938-380">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="09938-381">.NET</span><span class="sxs-lookup"><span data-stu-id="09938-381">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="09938-382">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="09938-382">.NET Option</span></span> |  <span data-ttu-id="09938-383">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-383">Default value</span></span> | <span data-ttu-id="09938-384">説明</span><span class="sxs-lookup"><span data-stu-id="09938-384">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="09938-385">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="09938-385">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="09938-386">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="09938-386">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="09938-387">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="09938-387">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="09938-388">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="09938-388">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="09938-389">Empty</span><span class="sxs-lookup"><span data-stu-id="09938-389">Empty</span></span> | <span data-ttu-id="09938-390">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="09938-390">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="09938-391">Empty</span><span class="sxs-lookup"><span data-stu-id="09938-391">Empty</span></span> | <span data-ttu-id="09938-392">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="09938-392">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="09938-393">Empty</span><span class="sxs-lookup"><span data-stu-id="09938-393">Empty</span></span> | <span data-ttu-id="09938-394">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="09938-394">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="09938-395">5 秒</span><span class="sxs-lookup"><span data-stu-id="09938-395">5 seconds</span></span> | <span data-ttu-id="09938-396">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="09938-396">WebSockets only.</span></span> <span data-ttu-id="09938-397">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="09938-397">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="09938-398">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="09938-398">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="09938-399">Empty</span><span class="sxs-lookup"><span data-stu-id="09938-399">Empty</span></span> | <span data-ttu-id="09938-400">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="09938-400">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="09938-401">HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="09938-401">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="09938-402">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="09938-402">Not used for WebSocket connections.</span></span> <span data-ttu-id="09938-403">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="09938-403">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="09938-404">既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="09938-404">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="09938-405">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="09938-405">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="09938-406">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="09938-406">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="09938-407">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="09938-407">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="09938-408">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="09938-408">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="09938-409">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="09938-409">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="09938-410">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="09938-410">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="09938-411">JavaScript</span><span class="sxs-lookup"><span data-stu-id="09938-411">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="09938-412">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="09938-412">JavaScript Option</span></span> | <span data-ttu-id="09938-413">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-413">Default Value</span></span> | <span data-ttu-id="09938-414">説明</span><span class="sxs-lookup"><span data-stu-id="09938-414">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="09938-415">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="09938-415">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="09938-416">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="09938-416">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="09938-417">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="09938-417">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="09938-418">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="09938-418">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="09938-419">Java</span><span class="sxs-lookup"><span data-stu-id="09938-419">Java</span></span>](#tab/java)

| <span data-ttu-id="09938-420">Java オプション</span><span class="sxs-lookup"><span data-stu-id="09938-420">Java Option</span></span> | <span data-ttu-id="09938-421">既定値</span><span class="sxs-lookup"><span data-stu-id="09938-421">Default Value</span></span> | <span data-ttu-id="09938-422">説明</span><span class="sxs-lookup"><span data-stu-id="09938-422">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="09938-423">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="09938-423">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="09938-424">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="09938-424">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="09938-425">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="09938-425">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="09938-426">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="09938-426">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="09938-427">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="09938-427">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="09938-428">Empty</span><span class="sxs-lookup"><span data-stu-id="09938-428">Empty</span></span> | <span data-ttu-id="09938-429">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="09938-429">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="09938-430">.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="09938-430">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="09938-431">JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="09938-431">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="09938-432">Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="09938-432">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="09938-433">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="09938-433">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
