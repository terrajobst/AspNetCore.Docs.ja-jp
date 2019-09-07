---
title: SignalR と ASP.NET Core SignalR の違い
author: bradygaster
description: SignalR と ASP.NET Core SignalR の違い
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746536"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="75abb-103">ASP.NET SignalR と ASP.NET Core SignalR の違い</span><span class="sxs-lookup"><span data-stu-id="75abb-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="75abb-104">ASP.NET Core SignalR は、ASP.NET SignalR のクライアントまたはサーバーと互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="75abb-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="75abb-105">この記事では ASP.NET Core SignalR で削除または変更された機能について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="75abb-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="75abb-106">SignalR のバージョンを識別する方法</span><span class="sxs-lookup"><span data-stu-id="75abb-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="75abb-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="75abb-107">ASP.NET SignalR</span></span> | <span data-ttu-id="75abb-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="75abb-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="75abb-109">サーバー NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="75abb-109">Server NuGet Package</span></span> | [<span data-ttu-id="75abb-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="75abb-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="75abb-111">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)</span><span class="sxs-lookup"><span data-stu-id="75abb-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="75abb-112">[AspNetCore. SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="75abb-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="75abb-113">クライアント NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="75abb-113">Client NuGet Packages</span></span> | [<span data-ttu-id="75abb-114">SignalR (Microsoft AspNet. クライアント)</span><span class="sxs-lookup"><span data-stu-id="75abb-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="75abb-115">SignalR (Microsoft AspNet)</span><span class="sxs-lookup"><span data-stu-id="75abb-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="75abb-116">AspNetCore. SignalR</span><span class="sxs-lookup"><span data-stu-id="75abb-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="75abb-117">クライアントの npm パッケージ</span><span class="sxs-lookup"><span data-stu-id="75abb-117">Client npm Package</span></span> | [<span data-ttu-id="75abb-118">signalr</span><span class="sxs-lookup"><span data-stu-id="75abb-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="75abb-119">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="75abb-119">Java Client</span></span> | <span data-ttu-id="75abb-120">[GitHub リポジトリ](https://github.com/SignalR/java-client)れ</span><span class="sxs-lookup"><span data-stu-id="75abb-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="75abb-121">Maven パッケージ[signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="75abb-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="75abb-122">サーバーアプリの種類</span><span class="sxs-lookup"><span data-stu-id="75abb-122">Server App Type</span></span> | <span data-ttu-id="75abb-123">ASP.NET (System.web) または OWIN 自己ホスト</span><span class="sxs-lookup"><span data-stu-id="75abb-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="75abb-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75abb-124">ASP.NET Core</span></span> |
| <span data-ttu-id="75abb-125">サポートされているサーバープラットフォーム</span><span class="sxs-lookup"><span data-stu-id="75abb-125">Supported Server Platforms</span></span> | <span data-ttu-id="75abb-126">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="75abb-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="75abb-127">.NET Framework 4.6.1 以降</span><span class="sxs-lookup"><span data-stu-id="75abb-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="75abb-128">.NET Core 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="75abb-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="75abb-129">機能の違い</span><span class="sxs-lookup"><span data-stu-id="75abb-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="75abb-130">自動再接続</span><span class="sxs-lookup"><span data-stu-id="75abb-130">Automatic reconnects</span></span>

<span data-ttu-id="75abb-131">自動再接続は、ASP.NET Core SignalR ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="75abb-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="75abb-132">クライアントが切断されている場合、ユーザーは再接続するときに、新しい接続を明示的に開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75abb-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="75abb-133">ASP.NET SignalR では、接続が切断された場合、SignalR はサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="75abb-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="75abb-134">プロトコルのサポート</span><span class="sxs-lookup"><span data-stu-id="75abb-134">Protocol support</span></span>

<span data-ttu-id="75abb-135">ASP.NET Core SignalR は、 [Messagepack](xref:signalr/messagepackhubprotocol)に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="75abb-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="75abb-136">さらに、カスタムプロトコルを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="75abb-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="75abb-137">トランスポート</span><span class="sxs-lookup"><span data-stu-id="75abb-137">Transports</span></span>

<span data-ttu-id="75abb-138">ASP.NET Core SignalR では、永続的なフレーム転送はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="75abb-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="75abb-139">サーバーの相違点</span><span class="sxs-lookup"><span data-stu-id="75abb-139">Differences on the server</span></span>

<span data-ttu-id="75abb-140">ASP.NET Core SignalR のサーバー側ライブラリは、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)パッケージに含まれています。このパッケージは、Razor プロジェクトと MVC プロジェクトの両方の**ASP.NET Core Web アプリケーション**テンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="75abb-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="75abb-141">ASP.NET Core SignalR は ASP.NET Core ミドルウェアであるため、で`Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)を呼び出すことによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75abb-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="75abb-142">ルーティングを構成するには、 `Startup.Configure`メソッドで[useendpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints)メソッド呼び出し内のハブにルートをマップします。</span><span class="sxs-lookup"><span data-stu-id="75abb-142">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="75abb-143">ルーティングを構成するには、 `Startup.Configure`メソッドのメソッド[呼び出し](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr)内のハブにルートをマップします。</span><span class="sxs-lookup"><span data-stu-id="75abb-143">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="75abb-144">固定セッション</span><span class="sxs-lookup"><span data-stu-id="75abb-144">Sticky sessions</span></span>

<span data-ttu-id="75abb-145">ASP.NET SignalR のスケールアウトモデルを使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="75abb-145">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="75abb-146">ASP.NET Core SignalR では、クライアントは接続中に同じサーバーと通信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75abb-146">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="75abb-147">Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="75abb-147">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="75abb-148">[Azure SignalR service](/azure/azure-signalr/)を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="75abb-148">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="75abb-149">接続ごとに1つのハブ</span><span class="sxs-lookup"><span data-stu-id="75abb-149">Single hub per connection</span></span>

<span data-ttu-id="75abb-150">ASP.NET Core SignalR では、接続モデルが簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="75abb-150">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="75abb-151">複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。</span><span class="sxs-lookup"><span data-stu-id="75abb-151">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="75abb-152">ストリーム</span><span class="sxs-lookup"><span data-stu-id="75abb-152">Streaming</span></span>

<span data-ttu-id="75abb-153">ASP.NET Core SignalR は、ハブからクライアントへの[データのストリーミング](xref:signalr/streaming)をサポートするようになりました。</span><span class="sxs-lookup"><span data-stu-id="75abb-153">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="75abb-154">状態</span><span class="sxs-lookup"><span data-stu-id="75abb-154">State</span></span>

<span data-ttu-id="75abb-155">クライアントとハブ (多くの場合 HubState と呼ばれます) の間で任意の状態を渡す機能は削除されており、進行状況メッセージもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="75abb-155">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="75abb-156">現時点では、対応するハブプロキシはありません。</span><span class="sxs-lookup"><span data-stu-id="75abb-156">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="75abb-157">PersistentConnection の削除</span><span class="sxs-lookup"><span data-stu-id="75abb-157">PersistentConnection removal</span></span>

<span data-ttu-id="75abb-158">ASP.NET Core SignalR では、 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))クラスは削除されています。</span><span class="sxs-lookup"><span data-stu-id="75abb-158">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="75abb-159">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="75abb-159">GlobalHost</span></span>

<span data-ttu-id="75abb-160">ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="75abb-160">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="75abb-161">サービスは DI を使用して[HubContext](xref:signalr/hubcontext)にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="75abb-161">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="75abb-162">ASP.NET `GlobalHost` SignalR でを`HubContext`取得するために使用されるオブジェクトは、ASP.NET Core SignalR に存在しません。</span><span class="sxs-lookup"><span data-stu-id="75abb-162">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="75abb-163">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="75abb-163">HubPipeline</span></span>

<span data-ttu-id="75abb-164">ASP.NET Core SignalR はモジュールを`HubPipeline`サポートしていません。</span><span class="sxs-lookup"><span data-stu-id="75abb-164">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="75abb-165">クライアントの相違点</span><span class="sxs-lookup"><span data-stu-id="75abb-165">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="75abb-166">TypeScript</span><span class="sxs-lookup"><span data-stu-id="75abb-166">TypeScript</span></span>

<span data-ttu-id="75abb-167">ASP.NET Core SignalR クライアントは[TypeScript](https://www.typescriptlang.org/)で記述されています。</span><span class="sxs-lookup"><span data-stu-id="75abb-167">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="75abb-168">Javascript[クライアント](xref:signalr/javascript-client)を使用する場合は、javascript または TypeScript で記述できます。</span><span class="sxs-lookup"><span data-stu-id="75abb-168">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="75abb-169">JavaScript クライアントは[npm](https://www.npmjs.com/)でホストされます。</span><span class="sxs-lookup"><span data-stu-id="75abb-169">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="75abb-170">以前のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを通じて取得されました。</span><span class="sxs-lookup"><span data-stu-id="75abb-170">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="75abb-171">コアバージョン[@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr)では、npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="75abb-171">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="75abb-172">このパッケージは、 **ASP.NET Core Web アプリケーション**テンプレートには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="75abb-172">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="75abb-173">Npm パッケージを取得してインストール`@aspnet/signalr`するには、npm を使用します。</span><span class="sxs-lookup"><span data-stu-id="75abb-173">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="75abb-174">jQuery</span><span class="sxs-lookup"><span data-stu-id="75abb-174">jQuery</span></span>

<span data-ttu-id="75abb-175">JQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。</span><span class="sxs-lookup"><span data-stu-id="75abb-175">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="75abb-176">Internet Explorer のサポート</span><span class="sxs-lookup"><span data-stu-id="75abb-176">Internet Explorer support</span></span>

<span data-ttu-id="75abb-177">ASP.NET Core SignalR には、Microsoft Internet Explorer 11 以降 (ASP.NET SignalR supported Microsoft Internet Explorer 8 以降がサポートされています) が必要です。</span><span class="sxs-lookup"><span data-stu-id="75abb-177">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="75abb-178">JavaScript クライアントメソッドの構文</span><span class="sxs-lookup"><span data-stu-id="75abb-178">JavaScript client method syntax</span></span>

<span data-ttu-id="75abb-179">JavaScript の構文は、以前のバージョンの SignalR から変更されています。</span><span class="sxs-lookup"><span data-stu-id="75abb-179">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="75abb-180">`$connection`オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="75abb-180">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="75abb-181">[On](/javascript/api/@aspnet/signalr/HubConnection#on)メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="75abb-181">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="75abb-182">クライアントメソッドを作成したら、ハブ接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="75abb-182">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="75abb-183">[Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)メソッドをチェーンして、エラーをログに記録または処理します。</span><span class="sxs-lookup"><span data-stu-id="75abb-183">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="75abb-184">ハブプロキシ</span><span class="sxs-lookup"><span data-stu-id="75abb-184">Hub proxies</span></span>

<span data-ttu-id="75abb-185">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="75abb-185">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="75abb-186">代わりに、メソッド名が文字列として[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="75abb-186">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="75abb-187">.NET とその他のクライアント</span><span class="sxs-lookup"><span data-stu-id="75abb-187">.NET and other clients</span></span>

<span data-ttu-id="75abb-188">`Microsoft.AspNetCore.SignalR.Client` NuGet パッケージには ASP.NET Core SignalR 用の .net クライアントライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="75abb-188">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="75abb-189">[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)を使用して、ハブへの接続のインスタンスを作成し、構築します。</span><span class="sxs-lookup"><span data-stu-id="75abb-189">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="75abb-190">スケールアウトの相違点</span><span class="sxs-lookup"><span data-stu-id="75abb-190">Scaleout differences</span></span>

<span data-ttu-id="75abb-191">ASP.NET SignalR は SQL Server と Redis をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="75abb-191">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="75abb-192">ASP.NET Core SignalR は、Azure SignalR サービスと Redis をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="75abb-192">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="75abb-193">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="75abb-193">ASP.NET</span></span>

* [<span data-ttu-id="75abb-194">Azure Service Bus によるスケールアウトの SignalR</span><span class="sxs-lookup"><span data-stu-id="75abb-194">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="75abb-195">Redis による SignalR スケールアウト</span><span class="sxs-lookup"><span data-stu-id="75abb-195">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="75abb-196">SQL Server によるスケールアウトの SignalR</span><span class="sxs-lookup"><span data-stu-id="75abb-196">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="75abb-197">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75abb-197">ASP.NET Core</span></span>

* [<span data-ttu-id="75abb-198">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="75abb-198">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="75abb-199">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="75abb-199">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="75abb-200">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="75abb-200">Additional resources</span></span>

* [<span data-ttu-id="75abb-201">ハブ</span><span class="sxs-lookup"><span data-stu-id="75abb-201">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="75abb-202">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="75abb-202">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="75abb-203">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="75abb-203">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="75abb-204">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="75abb-204">Supported platforms</span></span>](xref:signalr/supported-platforms)
