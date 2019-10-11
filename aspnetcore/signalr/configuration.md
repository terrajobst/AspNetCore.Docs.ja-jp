---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core SignalR アプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 66f274fcda27392091de6b4be8c7221bc87b7585
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246490"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="673b2-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="673b2-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="673b2-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="673b2-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="673b2-105">ASP.NET Core SignalR は、メッセージをエンコードするための2つのプロトコルをサポートしています。[JSON](https://www.json.org/)および[messagepack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="673b2-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="673b2-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="673b2-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="673b2-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="673b2-108">`AddJsonProtocol` は、`Startup.ConfigureServices` の[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="673b2-109">@No__t-0 メソッドは、@no__t 1 つのオブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="673b2-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="673b2-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数と戻り値のシリアル化を構成するために使用できる @no__t 1 @no__t のオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="673b2-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="673b2-111">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="673b2-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、`Startup.ConfigureServices` で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="673b2-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="673b2-113">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ @no__t 0 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="673b2-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="673b2-114">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="673b2-115">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="673b2-115">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="673b2-116">@No__t-1 でサポートされていない `Newtonsoft.Json` の機能が必要な場合は、「 [Newtonsoft. Json に切り替える」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-116">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="673b2-117">JSON のシリアル化は、 [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用してサーバー上で構成できます。これは `Startup.ConfigureServices` メソッドの [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-117">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="673b2-118">@No__t-0 メソッドは、@no__t 1 つのオブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="673b2-118">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="673b2-119">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と戻り値のシリアル化を構成するために使用できる JSON.NET `JsonSerializerSettings` オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="673b2-119">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="673b2-120">詳細については、 [JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-120">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="673b2-121">たとえば、既定の "キャメルケース" 名の代わりに "" という名前のプロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices` で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="673b2-121">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="673b2-122">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ @no__t 0 拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="673b2-122">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="673b2-123">拡張メソッドを解決するには、`Microsoft.Extensions.DependencyInjection` 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-123">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

::: moniker-end

> [!NOTE]
> <span data-ttu-id="673b2-124">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="673b2-124">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="673b2-125">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="673b2-125">MessagePack serialization options</span></span>

<span data-ttu-id="673b2-126">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-126">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="673b2-127">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-127">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="673b2-128">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="673b2-128">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="673b2-129">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="673b2-129">Configure server options</span></span>

<span data-ttu-id="673b2-130">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="673b2-130">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="673b2-131">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-131">Option</span></span> | <span data-ttu-id="673b2-132">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-132">Default Value</span></span> | <span data-ttu-id="673b2-133">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-133">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="673b2-134">30 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-134">30 seconds</span></span> | <span data-ttu-id="673b2-135">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="673b2-135">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="673b2-136">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="673b2-136">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="673b2-137">推奨値は、`KeepAliveInterval` の倍精度浮動小数点値です。</span><span class="sxs-lookup"><span data-stu-id="673b2-137">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="673b2-138">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-138">15 seconds</span></span> | <span data-ttu-id="673b2-139">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="673b2-139">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="673b2-140">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-140">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-141">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-141">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="673b2-142">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-142">15 seconds</span></span> | <span data-ttu-id="673b2-143">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-143">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="673b2-144">@No__t-0 に変更する場合は、クライアントの `ServerTimeout` @ no__t @ no__t の設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="673b2-144">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="673b2-145">推奨される `ServerTimeout` @ no__t @ no__t は2倍 @no__t の値です。</span><span class="sxs-lookup"><span data-stu-id="673b2-145">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="673b2-146">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="673b2-146">All installed protocols</span></span> | <span data-ttu-id="673b2-147">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="673b2-147">Protocols supported by this hub.</span></span> <span data-ttu-id="673b2-148">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="673b2-148">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="673b2-149">@No__t が0の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-149">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="673b2-150">既定値は `false` です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="673b2-150">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="673b2-151">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="673b2-151">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="673b2-152">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="673b2-152">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="673b2-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="673b2-153">32 KB</span></span> | <span data-ttu-id="673b2-154">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="673b2-154">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="673b2-155">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-155">Option</span></span> | <span data-ttu-id="673b2-156">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-156">Default Value</span></span> | <span data-ttu-id="673b2-157">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-157">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="673b2-158">30 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-158">30 seconds</span></span> | <span data-ttu-id="673b2-159">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="673b2-159">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="673b2-160">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="673b2-160">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="673b2-161">推奨値は、`KeepAliveInterval` の倍精度浮動小数点値です。</span><span class="sxs-lookup"><span data-stu-id="673b2-161">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="673b2-162">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-162">15 seconds</span></span> | <span data-ttu-id="673b2-163">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="673b2-163">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="673b2-164">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-164">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-165">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-165">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="673b2-166">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-166">15 seconds</span></span> | <span data-ttu-id="673b2-167">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-167">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="673b2-168">@No__t-0 に変更する場合は、クライアントの `ServerTimeout` @ no__t @ no__t の設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="673b2-168">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="673b2-169">推奨される `ServerTimeout` @ no__t @ no__t は2倍 @no__t の値です。</span><span class="sxs-lookup"><span data-stu-id="673b2-169">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="673b2-170">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="673b2-170">All installed protocols</span></span> | <span data-ttu-id="673b2-171">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="673b2-171">Protocols supported by this hub.</span></span> <span data-ttu-id="673b2-172">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="673b2-172">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="673b2-173">@No__t が0の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-173">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="673b2-174">既定値は `false` です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="673b2-174">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="673b2-175">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-175">Option</span></span> | <span data-ttu-id="673b2-176">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-176">Default Value</span></span> | <span data-ttu-id="673b2-177">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="673b2-178">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-178">15 seconds</span></span> | <span data-ttu-id="673b2-179">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="673b2-179">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="673b2-180">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-181">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="673b2-182">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-182">15 seconds</span></span> | <span data-ttu-id="673b2-183">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-183">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="673b2-184">@No__t-0 に変更する場合は、クライアントの `ServerTimeout` @ no__t @ no__t の設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="673b2-184">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="673b2-185">推奨される `ServerTimeout` @ no__t @ no__t は2倍 @no__t の値です。</span><span class="sxs-lookup"><span data-stu-id="673b2-185">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="673b2-186">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="673b2-186">All installed protocols</span></span> | <span data-ttu-id="673b2-187">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="673b2-187">Protocols supported by this hub.</span></span> <span data-ttu-id="673b2-188">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="673b2-188">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="673b2-189">@No__t が0の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-189">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="673b2-190">既定値は `false` です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="673b2-190">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="673b2-191">オプションは、`Startup.ConfigureServices` の `AddSignalR` 呼び出しに委任オプションを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-191">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="673b2-192">1つのハブのオプションは `AddSignalR` で指定されたグローバルオプションを上書きし、<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-192">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="673b2-193">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="673b2-193">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="673b2-194">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="673b2-194">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="673b2-195">これらのオプションは、`Startup.Configure` の[Maphub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-195">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="673b2-196">トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、`HttpConnectionDispatcherOptions` を使用します。</span><span class="sxs-lookup"><span data-stu-id="673b2-196">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="673b2-197">これらのオプションは、`Startup.Configure` の[Maphub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-197">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="673b2-198">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="673b2-198">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="673b2-199">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-199">Option</span></span> | <span data-ttu-id="673b2-200">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-200">Default Value</span></span> | <span data-ttu-id="673b2-201">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-201">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="673b2-202">32 KB</span><span class="sxs-lookup"><span data-stu-id="673b2-202">32 KB</span></span> | <span data-ttu-id="673b2-203">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="673b2-203">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="673b2-204">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-204">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="673b2-205">ハブクラスに適用された @no__t 0 属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="673b2-205">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="673b2-206">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="673b2-206">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="673b2-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="673b2-207">32 KB</span></span> | <span data-ttu-id="673b2-208">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="673b2-208">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="673b2-209">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-209">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="673b2-210">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="673b2-210">All Transports are enabled.</span></span> | <span data-ttu-id="673b2-211">クライアントが接続に使用できるトランスポートを制限できる @no__t 0 の値を列挙したビットフラグ。</span><span class="sxs-lookup"><span data-stu-id="673b2-211">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="673b2-212">下記を参照。</span><span class="sxs-lookup"><span data-stu-id="673b2-212">See below.</span></span> | <span data-ttu-id="673b2-213">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="673b2-213">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="673b2-214">下記を参照。</span><span class="sxs-lookup"><span data-stu-id="673b2-214">See below.</span></span> | <span data-ttu-id="673b2-215">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="673b2-215">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="673b2-216">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-216">Option</span></span> | <span data-ttu-id="673b2-217">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-217">Default Value</span></span> | <span data-ttu-id="673b2-218">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-218">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="673b2-219">32 KB</span><span class="sxs-lookup"><span data-stu-id="673b2-219">32 KB</span></span> | <span data-ttu-id="673b2-220">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="673b2-220">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="673b2-221">この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-221">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="673b2-222">ハブクラスに適用された @no__t 0 属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="673b2-222">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="673b2-223">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="673b2-223">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="673b2-224">32 KB</span><span class="sxs-lookup"><span data-stu-id="673b2-224">32 KB</span></span> | <span data-ttu-id="673b2-225">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="673b2-225">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="673b2-226">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-226">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="673b2-227">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="673b2-227">All Transports are enabled.</span></span> | <span data-ttu-id="673b2-228">クライアントが接続に使用できるトランスポートを制限できる @no__t 0 の値を列挙したビットフラグ。</span><span class="sxs-lookup"><span data-stu-id="673b2-228">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="673b2-229">下記を参照。</span><span class="sxs-lookup"><span data-stu-id="673b2-229">See below.</span></span> | <span data-ttu-id="673b2-230">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="673b2-230">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="673b2-231">下記を参照。</span><span class="sxs-lookup"><span data-stu-id="673b2-231">See below.</span></span> | <span data-ttu-id="673b2-232">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="673b2-232">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="673b2-233">長いポーリングトランスポートには、`LongPolling` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="673b2-233">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="673b2-234">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-234">Option</span></span> | <span data-ttu-id="673b2-235">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-235">Default Value</span></span> | <span data-ttu-id="673b2-236">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-236">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="673b2-237">90秒</span><span class="sxs-lookup"><span data-stu-id="673b2-237">90 seconds</span></span> | <span data-ttu-id="673b2-238">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="673b2-238">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="673b2-239">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="673b2-239">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="673b2-240">WebSocket トランスポートには、`WebSockets` プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="673b2-240">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="673b2-241">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-241">Option</span></span> | <span data-ttu-id="673b2-242">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-242">Default Value</span></span> | <span data-ttu-id="673b2-243">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-243">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="673b2-244">5 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-244">5 seconds</span></span> | <span data-ttu-id="673b2-245">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="673b2-245">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="673b2-246">@No__t-0 ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="673b2-246">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="673b2-247">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="673b2-247">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="673b2-248">クライアントオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="673b2-248">Configure client options</span></span>

<span data-ttu-id="673b2-249">クライアントオプションは、@no__t 0 の型 (.NET および JavaScript クライアントで使用可能) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-249">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="673b2-250">Java クライアントでも使用できますが、`HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションと `HubConnection` 自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="673b2-250">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="673b2-251">ログの構成</span><span class="sxs-lookup"><span data-stu-id="673b2-251">Configure logging</span></span>

<span data-ttu-id="673b2-252">ログは、.NET クライアントで `ConfigureLogging` メソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-252">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="673b2-253">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-253">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="673b2-254">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-254">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="673b2-255">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-255">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="673b2-256">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-256">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="673b2-257">たとえば、コンソールのログ記録を有効にするには、@no__t 0 の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="673b2-257">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="673b2-258">@No__t-0 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="673b2-258">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="673b2-259">JavaScript クライアントでは、同様の @no__t 0 メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="673b2-259">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="673b2-260">生成するログメッセージの最小レベルを示す @no__t 0 の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-260">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="673b2-261">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="673b2-261">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="673b2-262">@No__t 0 の値の代わりに、ログレベル名を表す `string` の値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="673b2-262">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="673b2-263">これは、@no__t 0 の定数にアクセスできない環境で SignalR のログ記録を構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="673b2-263">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="673b2-264">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="673b2-264">The following table lists the available log levels.</span></span> <span data-ttu-id="673b2-265">@No__t-0 に指定した値は、ログに記録される**最小**ログレベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-265">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="673b2-266">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-266">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="673b2-267">String</span><span class="sxs-lookup"><span data-stu-id="673b2-267">String</span></span>                      | <span data-ttu-id="673b2-268">LogLevel</span><span class="sxs-lookup"><span data-stu-id="673b2-268">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="673b2-269">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="673b2-269">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="673b2-270">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="673b2-270">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="673b2-271">完全にログ記録を無効にするには、`configureLogging` メソッドに `signalR.LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-271">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="673b2-272">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-272">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="673b2-273">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="673b2-273">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="673b2-274">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="673b2-274">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="673b2-275">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="673b2-275">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="673b2-276">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="673b2-276">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="673b2-277">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="673b2-277">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="673b2-278">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="673b2-278">Configure allowed transports</span></span>

<span data-ttu-id="673b2-279">SignalR によって使用されるトランスポートは @no__t 0 呼び出し (JavaScript では `withUrl`) で構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-279">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="673b2-280">@No__t-0 の値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-280">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="673b2-281">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="673b2-281">All transports are enabled by default.</span></span>

<span data-ttu-id="673b2-282">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="673b2-282">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="673b2-283">JavaScript クライアントでは、`withUrl` に指定された options オブジェクトの `transport` フィールドを設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="673b2-283">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="673b2-284">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="673b2-284">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="673b2-285">Java クライアントでは、トランスポートは、`HttpHubConnectionBuilder` の `withTransport` メソッドを使用して選択されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-285">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="673b2-286">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-286">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="673b2-287">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="673b2-287">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="673b2-288">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="673b2-288">Configure bearer authentication</span></span>

<span data-ttu-id="673b2-289">SignalR 要求と共に認証データを提供するには、`AccessTokenProvider` オプション (JavaScript の `accessTokenFactory`) を使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-289">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="673b2-290">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます (型が `Bearer` の `Authorization` ヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="673b2-290">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="673b2-291">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="673b2-291">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="673b2-292">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` です。</span><span class="sxs-lookup"><span data-stu-id="673b2-292">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="673b2-293">.NET クライアントでは、`WithUrl` のオプションデリゲートを使用して `AccessTokenProvider` オプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-293">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="673b2-294">JavaScript クライアントでは、`withUrl` の options オブジェクトの `accessTokenFactory` フィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-294">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="673b2-295">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-295">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="673b2-296">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava) [単一の @ no__t-3string >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="673b2-297">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-297">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="673b2-298">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="673b2-298">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="673b2-299">タイムアウトとキープアライブの動作を構成するための追加オプションは、@no__t 0 のオブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-299">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="673b2-300">.NET</span><span class="sxs-lookup"><span data-stu-id="673b2-300">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="673b2-301">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-301">Option</span></span> | <span data-ttu-id="673b2-302">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-302">Default value</span></span> | <span data-ttu-id="673b2-303">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-303">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="673b2-304">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-304">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="673b2-305">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-305">Timeout for server activity.</span></span> <span data-ttu-id="673b2-306">サーバーがこの期間内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、@no__t 0 イベント (JavaScript では `onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-306">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="673b2-307">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-307">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="673b2-308">推奨値は、少なくともサーバーの @no__t 0 の値で、ping が到着するまでの時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-308">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="673b2-309">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-309">15 seconds</span></span> | <span data-ttu-id="673b2-310">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-310">Timeout for initial server handshake.</span></span> <span data-ttu-id="673b2-311">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、@no__t 0 イベント (JavaScript では `onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-311">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="673b2-312">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-312">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-313">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-313">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="673b2-314">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-314">15 seconds</span></span> | <span data-ttu-id="673b2-315">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-315">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="673b2-316">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="673b2-316">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="673b2-317">クライアントがサーバーで設定された @no__t 0 のメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="673b2-317">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="673b2-318">.NET クライアントでは、タイムアウト値は @no__t 0 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-318">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="673b2-319">JavaScript</span><span class="sxs-lookup"><span data-stu-id="673b2-319">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="673b2-320">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-320">Option</span></span> | <span data-ttu-id="673b2-321">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-321">Default value</span></span> | <span data-ttu-id="673b2-322">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-322">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="673b2-323">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-323">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="673b2-324">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-324">Timeout for server activity.</span></span> <span data-ttu-id="673b2-325">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、@no__t 0 イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-325">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="673b2-326">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-326">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="673b2-327">推奨値は、少なくともサーバーの @no__t 0 の値で、ping が到着するまでの時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-327">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="673b2-328">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-328">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="673b2-329">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-329">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="673b2-330">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="673b2-330">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="673b2-331">クライアントがサーバーで設定された @no__t 0 のメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="673b2-331">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="673b2-332">Java</span><span class="sxs-lookup"><span data-stu-id="673b2-332">Java</span></span>](#tab/java)

| <span data-ttu-id="673b2-333">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-333">Option</span></span> | <span data-ttu-id="673b2-334">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-334">Default value</span></span> | <span data-ttu-id="673b2-335">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-335">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="673b2-336">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="673b2-336">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="673b2-337">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-337">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="673b2-338">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-338">Timeout for server activity.</span></span> <span data-ttu-id="673b2-339">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、@no__t 0 イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-339">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="673b2-340">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-340">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="673b2-341">推奨値は、少なくともサーバーの @no__t 0 の値で、ping が到着するまでの時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-341">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="673b2-342">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-342">15 seconds</span></span> | <span data-ttu-id="673b2-343">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-343">Timeout for initial server handshake.</span></span> <span data-ttu-id="673b2-344">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、@no__t 0 イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-344">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="673b2-345">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-345">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-346">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-346">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="673b2-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="673b2-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="673b2-348">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-348">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="673b2-349">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-349">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="673b2-350">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="673b2-350">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="673b2-351">クライアントがサーバーで設定された @no__t 0 のメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="673b2-351">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="673b2-352">.NET</span><span class="sxs-lookup"><span data-stu-id="673b2-352">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="673b2-353">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-353">Option</span></span> | <span data-ttu-id="673b2-354">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-354">Default value</span></span> | <span data-ttu-id="673b2-355">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-355">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="673b2-356">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-356">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="673b2-357">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-357">Timeout for server activity.</span></span> <span data-ttu-id="673b2-358">サーバーがこの期間内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、@no__t 0 イベント (JavaScript では `onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-358">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="673b2-359">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-359">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="673b2-360">推奨値は、少なくともサーバーの @no__t 0 の値で、ping が到着するまでの時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-360">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="673b2-361">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-361">15 seconds</span></span> | <span data-ttu-id="673b2-362">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-362">Timeout for initial server handshake.</span></span> <span data-ttu-id="673b2-363">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、@no__t 0 イベント (JavaScript では `onclose`) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-363">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="673b2-364">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-364">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-365">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-365">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="673b2-366">.NET クライアントでは、タイムアウト値は @no__t 0 値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-366">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="673b2-367">JavaScript</span><span class="sxs-lookup"><span data-stu-id="673b2-367">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="673b2-368">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-368">Option</span></span> | <span data-ttu-id="673b2-369">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-369">Default value</span></span> | <span data-ttu-id="673b2-370">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="673b2-371">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="673b2-372">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-372">Timeout for server activity.</span></span> <span data-ttu-id="673b2-373">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、@no__t 0 イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="673b2-374">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="673b2-375">推奨値は、少なくともサーバーの @no__t 0 の値で、ping が到着するまでの時間を指定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-375">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="673b2-376">Java</span><span class="sxs-lookup"><span data-stu-id="673b2-376">Java</span></span>](#tab/java)

| <span data-ttu-id="673b2-377">OPTION</span><span class="sxs-lookup"><span data-stu-id="673b2-377">Option</span></span> | <span data-ttu-id="673b2-378">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-378">Default value</span></span> | <span data-ttu-id="673b2-379">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-379">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="673b2-380">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="673b2-380">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="673b2-381">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="673b2-381">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="673b2-382">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-382">Timeout for server activity.</span></span> <span data-ttu-id="673b2-383">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、@no__t 0 イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-383">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="673b2-384">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="673b2-384">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="673b2-385">推奨値は、少なくともサーバーの @no__t 0 の値で、ping が到着するまでの時間を考慮した値です。</span><span class="sxs-lookup"><span data-stu-id="673b2-385">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="673b2-386">15 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-386">15 seconds</span></span> | <span data-ttu-id="673b2-387">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="673b2-387">Timeout for initial server handshake.</span></span> <span data-ttu-id="673b2-388">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、@no__t 0 イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="673b2-388">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="673b2-389">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="673b2-389">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="673b2-390">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="673b2-390">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="673b2-391">追加のオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="673b2-391">Configure additional options</span></span>

<span data-ttu-id="673b2-392">追加のオプションは、`HubConnectionBuilder` の `WithUrl` (JavaScript の `withUrl`) メソッド、または Java クライアントの `HttpHubConnectionBuilder` のさまざまな構成 Api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-392">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="673b2-393">.NET</span><span class="sxs-lookup"><span data-stu-id="673b2-393">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="673b2-394">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="673b2-394">.NET Option</span></span> |  <span data-ttu-id="673b2-395">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-395">Default value</span></span> | <span data-ttu-id="673b2-396">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-396">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="673b2-397">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="673b2-397">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="673b2-398">ネゴシエーションの手順をスキップするには、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-398">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="673b2-399">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="673b2-399">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="673b2-400">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="673b2-400">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="673b2-401">Empty</span><span class="sxs-lookup"><span data-stu-id="673b2-401">Empty</span></span> | <span data-ttu-id="673b2-402">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="673b2-402">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="673b2-403">Empty</span><span class="sxs-lookup"><span data-stu-id="673b2-403">Empty</span></span> | <span data-ttu-id="673b2-404">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="673b2-404">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="673b2-405">Empty</span><span class="sxs-lookup"><span data-stu-id="673b2-405">Empty</span></span> | <span data-ttu-id="673b2-406">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="673b2-406">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="673b2-407">5 秒</span><span class="sxs-lookup"><span data-stu-id="673b2-407">5 seconds</span></span> | <span data-ttu-id="673b2-408">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="673b2-408">WebSockets only.</span></span> <span data-ttu-id="673b2-409">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="673b2-409">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="673b2-410">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-410">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="673b2-411">Empty</span><span class="sxs-lookup"><span data-stu-id="673b2-411">Empty</span></span> | <span data-ttu-id="673b2-412">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="673b2-412">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="673b2-413">HTTP 要求を送信するために使用される @no__t 0 の構成または置換に使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="673b2-413">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="673b2-414">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="673b2-414">Not used for WebSocket connections.</span></span> <span data-ttu-id="673b2-415">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="673b2-415">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="673b2-416">既定値の設定を変更して返すか、または新しい `HttpMessageHandler` インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="673b2-416">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="673b2-417">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="673b2-417">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="673b2-418">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="673b2-418">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="673b2-419">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="673b2-419">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="673b2-420">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="673b2-420">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="673b2-421">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="673b2-421">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="673b2-422">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="673b2-422">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="673b2-423">JavaScript</span><span class="sxs-lookup"><span data-stu-id="673b2-423">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="673b2-424">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="673b2-424">JavaScript Option</span></span> | <span data-ttu-id="673b2-425">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-425">Default Value</span></span> | <span data-ttu-id="673b2-426">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-426">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="673b2-427">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="673b2-427">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="673b2-428">ネゴシエーションの手順をスキップするには、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-428">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="673b2-429">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="673b2-429">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="673b2-430">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="673b2-430">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="673b2-431">Java</span><span class="sxs-lookup"><span data-stu-id="673b2-431">Java</span></span>](#tab/java)

| <span data-ttu-id="673b2-432">Java オプション</span><span class="sxs-lookup"><span data-stu-id="673b2-432">Java Option</span></span> | <span data-ttu-id="673b2-433">既定値</span><span class="sxs-lookup"><span data-stu-id="673b2-433">Default Value</span></span> | <span data-ttu-id="673b2-434">説明</span><span class="sxs-lookup"><span data-stu-id="673b2-434">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="673b2-435">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="673b2-435">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="673b2-436">ネゴシエーションの手順をスキップするには、`true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="673b2-436">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="673b2-437">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="673b2-437">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="673b2-438">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="673b2-438">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="673b2-439">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="673b2-439">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="673b2-440">Empty</span><span class="sxs-lookup"><span data-stu-id="673b2-440">Empty</span></span> | <span data-ttu-id="673b2-441">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="673b2-441">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="673b2-442">.NET クライアントでは、これらのオプションは `WithUrl` に指定されたオプションデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-442">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="673b2-443">JavaScript クライアントでは、これらのオプションは `withUrl` に指定された JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-443">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="673b2-444">Java クライアントでは、これらのオプションは、@no__t から返された @no__t 0 のメソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="673b2-444">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="673b2-445">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="673b2-445">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
