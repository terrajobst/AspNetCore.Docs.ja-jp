---
title: SignalR と ASP.NET Core の違い SignalR
author: bradygaster
description: SignalR と ASP.NET Core の違い SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: cca9a0cb0c46fc25eb5d1f7127d31fd3ab92f0b4
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880362"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="8bdc5-103">ASP.NET SignalR と ASP.NET Core SignalR の違い</span><span class="sxs-lookup"><span data-stu-id="8bdc5-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="8bdc5-104">ASP.NET Core SignalR は、ASP.NET SignalRのクライアントまたはサーバーと互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="8bdc5-105">この記事では ASP.NET Core SignalRで削除または変更された機能について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="8bdc5-106">SignalR のバージョンを特定する方法</span><span class="sxs-lookup"><span data-stu-id="8bdc5-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="8bdc5-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="8bdc5-107">ASP.NET SignalR</span></span> | <span data-ttu-id="8bdc5-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="8bdc5-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="8bdc5-109">サーバー NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-109">Server NuGet package</span></span> | <span data-ttu-id="8bdc5-110">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="8bdc5-111">ありません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-111">None.</span></span> <span data-ttu-id="8bdc5-112">[AspNetCore](xref:fundamentals/metapackage-app)共有フレームワークに含まれています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="8bdc5-113">クライアント NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-113">Client NuGet packages</span></span> | <span data-ttu-id="8bdc5-114">[SignalR。Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="8bdc5-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="8bdc5-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="8bdc5-117">JavaScript クライアント npm パッケージ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-117">JavaScript client npm package</span></span> | [<span data-ttu-id="8bdc5-118">signalr</span><span class="sxs-lookup"><span data-stu-id="8bdc5-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="8bdc5-119">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="8bdc5-119">Java client</span></span> | <span data-ttu-id="8bdc5-120">[GitHub リポジトリ](https://github.com/SignalR/java-client)(非推奨)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="8bdc5-121">Maven パッケージ[com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="8bdc5-122">サーバーアプリの種類</span><span class="sxs-lookup"><span data-stu-id="8bdc5-122">Server app type</span></span> | <span data-ttu-id="8bdc5-123">ASP.NET (System.web) または OWIN 自己ホスト</span><span class="sxs-lookup"><span data-stu-id="8bdc5-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="8bdc5-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bdc5-124">ASP.NET Core</span></span> |
| <span data-ttu-id="8bdc5-125">サポートされているサーバープラットフォーム</span><span class="sxs-lookup"><span data-stu-id="8bdc5-125">Supported server platforms</span></span> | <span data-ttu-id="8bdc5-126">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="8bdc5-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="8bdc5-127">.NET Core 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="8bdc5-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="8bdc5-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="8bdc5-128">ASP.NET SignalR</span></span> | <span data-ttu-id="8bdc5-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="8bdc5-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="8bdc5-130">サーバー NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-130">Server NuGet package</span></span> | <span data-ttu-id="8bdc5-131">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="8bdc5-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="8bdc5-133">[Microsoft.AspNetCore.SignalRSignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="8bdc5-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-134">(.NET Framework)</span></span> |
| <span data-ttu-id="8bdc5-135">クライアント NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-135">Client NuGet packages</span></span> | <span data-ttu-id="8bdc5-136">[SignalR。Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="8bdc5-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="8bdc5-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="8bdc5-139">JavaScript クライアント npm パッケージ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-139">JavaScript client npm package</span></span> | [<span data-ttu-id="8bdc5-140">signalr</span><span class="sxs-lookup"><span data-stu-id="8bdc5-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="8bdc5-141">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="8bdc5-141">Java client</span></span> | <span data-ttu-id="8bdc5-142">[GitHub リポジトリ](https://github.com/SignalR/java-client)(非推奨)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="8bdc5-143">Maven パッケージ[com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="8bdc5-144">サーバーアプリの種類</span><span class="sxs-lookup"><span data-stu-id="8bdc5-144">Server app type</span></span> | <span data-ttu-id="8bdc5-145">ASP.NET (System.web) または OWIN 自己ホスト</span><span class="sxs-lookup"><span data-stu-id="8bdc5-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="8bdc5-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bdc5-146">ASP.NET Core</span></span> |
| <span data-ttu-id="8bdc5-147">サポートされているサーバープラットフォーム</span><span class="sxs-lookup"><span data-stu-id="8bdc5-147">Supported server platforms</span></span> | <span data-ttu-id="8bdc5-148">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="8bdc5-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="8bdc5-149">.NET Framework 4.6.1 以降</span><span class="sxs-lookup"><span data-stu-id="8bdc5-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="8bdc5-150">.NET Core 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="8bdc5-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="8bdc5-151">機能の違い</span><span class="sxs-lookup"><span data-stu-id="8bdc5-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="8bdc5-152">自動再接続</span><span class="sxs-lookup"><span data-stu-id="8bdc5-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bdc5-153">ASP.NET SignalR:</span><span class="sxs-lookup"><span data-stu-id="8bdc5-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="8bdc5-154">既定では、接続が切断された場合、SignalR はサーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="8bdc5-155">ASP.NET Core SignalRでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="8bdc5-156">自動再接続は、 [.net クライアント](xref:signalr/dotnet-client#automatically-reconnect)と[JavaScript クライアント](xref:signalr/javascript-client#automatically-reconnect)の両方にオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bdc5-157">ASP.NET Core 3.0 より前では、SignalR は自動再接続をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="8bdc5-158">クライアントが切断されている場合、ユーザーは新しい接続を明示的に開始して再接続する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="8bdc5-159">ASP.NET SignalRでは、接続が切断された場合、SignalR サーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="8bdc5-160">プロトコルのサポート</span><span class="sxs-lookup"><span data-stu-id="8bdc5-160">Protocol support</span></span>

<span data-ttu-id="8bdc5-161">ASP.NET Core SignalR は、 [MessagePack](xref:signalr/messagepackhubprotocol) に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="8bdc5-162">さらに、カスタムプロトコルを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="8bdc5-163">トランスポート</span><span class="sxs-lookup"><span data-stu-id="8bdc5-163">Transports</span></span>

<span data-ttu-id="8bdc5-164">ASP.NET Core SignalRでは、永続的フレーム転送はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="8bdc5-165">サーバーの相違点</span><span class="sxs-lookup"><span data-stu-id="8bdc5-165">Differences on the server</span></span>

<span data-ttu-id="8bdc5-166">ASP.NET Core SignalR のサーバー側ライブラリは、AspNetCore に含まれています。この[アプリ](xref:fundamentals/metapackage-app)は、Razor プロジェクトと MVC プロジェクトの両方の**ASP.NET Core Web アプリケーション**テンプレートで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="8bdc5-167">ASP.NET Core SignalR は ASP.NET Core ミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="8bdc5-168">`Startup.ConfigureServices`で <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> を呼び出すことによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bdc5-169">ルーティングを構成するには、`Startup.Configure` メソッドの <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> メソッド呼び出し内のハブにルートをマップします。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8bdc5-170">ルーティングを構成するには、`Startup.Configure` メソッドの <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> メソッド呼び出し内のハブにルートをマップします。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="8bdc5-171">固定セッション</span><span class="sxs-lookup"><span data-stu-id="8bdc5-171">Sticky sessions</span></span>

<span data-ttu-id="8bdc5-172">ASP.NET SignalR のスケールアウトモデルを使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="8bdc5-173">ASP.NET Core SignalR では、クライアントは接続中に同じサーバーと通信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="8bdc5-174">Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="8bdc5-175">[Azure SignalR サービス](/azure/azure-signalr/)を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="8bdc5-176">接続ごとに1つのハブ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-176">Single hub per connection</span></span>

<span data-ttu-id="8bdc5-177">ASP.NET Core SignalRでは、接続モデルが単純化されています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="8bdc5-178">複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="8bdc5-179">ストリーム</span><span class="sxs-lookup"><span data-stu-id="8bdc5-179">Streaming</span></span>

<span data-ttu-id="8bdc5-180">ASP.NET Core SignalR では、ハブからクライアントへの[データのストリーミング](xref:signalr/streaming)がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="8bdc5-181">都道府県</span><span class="sxs-lookup"><span data-stu-id="8bdc5-181">State</span></span>

<span data-ttu-id="8bdc5-182">クライアントとハブ (多くの場合 `HubState`と呼ばれます) の間で任意の状態を渡す機能は削除されており、進行状況メッセージもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="8bdc5-183">現時点では、対応するハブプロキシはありません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="8bdc5-184">PersistentConnection の削除</span><span class="sxs-lookup"><span data-stu-id="8bdc5-184">PersistentConnection removal</span></span>

<span data-ttu-id="8bdc5-185">ASP.NET Core SignalRでは、 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))クラスは削除されています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="8bdc5-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="8bdc5-186">GlobalHost</span></span>

<span data-ttu-id="8bdc5-187">ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="8bdc5-188">サービスは DI を使用して [HubContext](xref:signalr/hubcontext) にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="8bdc5-189">ASP.NET SignalR で`HubContext`を取得するために使用される `GlobalHost` オブジェクトは、ASP.NET Core SignalR に存在しません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="8bdc5-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="8bdc5-190">HubPipeline</span></span>

<span data-ttu-id="8bdc5-191">ASP.NET Core SignalR は`HubPipeline`モジュールをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="8bdc5-192">クライアントの相違点</span><span class="sxs-lookup"><span data-stu-id="8bdc5-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="8bdc5-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="8bdc5-193">TypeScript</span></span>

<span data-ttu-id="8bdc5-194">ASP.NET Core SignalR クライアントは[TypeScript](https://www.typescriptlang.org/) で記述されています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="8bdc5-195">Javascript[クライアント](xref:signalr/javascript-client)を使用する場合は、javascript または TypeScript で記述できます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="8bdc5-196">JavaScript クライアントは npm でホストされます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bdc5-197">ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="8bdc5-198">ASP.NET Core のバージョンでは、 [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="8bdc5-199">このパッケージは、Razor プロジェクトと MVC プロジェクトの両方の**ASP.NET Core Web アプリケーション**テンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="8bdc5-200">`@microsoft/signalr` npm パッケージを取得してインストールするには、npm を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8bdc5-201">ASP.NET のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを使用して取得されました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="8bdc5-202">ASP.NET Core のバージョンでは、 [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="8bdc5-203">このパッケージは、Razor プロジェクトと MVC プロジェクトの両方の**ASP.NET Core Web アプリケーション**テンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="8bdc5-204">`@aspnet/signalr` npm パッケージを取得してインストールするには、npm を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="8bdc5-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="8bdc5-205">jQuery</span></span>

<span data-ttu-id="8bdc5-206">jQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="8bdc5-207">Internet Explorer のサポート</span><span class="sxs-lookup"><span data-stu-id="8bdc5-207">Internet Explorer support</span></span>

<span data-ttu-id="8bdc5-208">ASP.NET Core SignalR には、Microsoft Internet Explorer 11 以降 (ASP.NET SignalR サポートされている Microsoft Internet Explorer 8 以降が必要です) が必要です。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="8bdc5-209">JavaScript クライアントメソッドの構文</span><span class="sxs-lookup"><span data-stu-id="8bdc5-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bdc5-210">JavaScript の構文は、SignalRの ASP.NET バージョンから変更されました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="8bdc5-211">`$connection` オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="8bdc5-212">[on](/javascript/api/@microsoft/signalr/HubConnection#on) メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8bdc5-213">JavaScript の構文は、SignalRの ASP.NET バージョンから変更されました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="8bdc5-214">`$connection` オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="8bdc5-215">[on](/javascript/api/@aspnet/signalr/HubConnection#on) メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="8bdc5-216">クライアントメソッドを作成したら、ハブ接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="8bdc5-217">[catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) メソッドをチェーンして、エラーをログに記録または処理します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="8bdc5-218">ハブプロキシ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bdc5-219">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="8bdc5-220">代わりに、メソッド名が文字列として[invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="8bdc5-221">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="8bdc5-222">代わりに、メソッド名が文字列として[invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="8bdc5-223">.NET とその他のクライアント</span><span class="sxs-lookup"><span data-stu-id="8bdc5-223">.NET and other clients</span></span>

<span data-ttu-id="8bdc5-224">[SignalRAspNetCore です。クライアント](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)NuGet パッケージには、ASP.NET Core SignalR用の .net クライアントライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="8bdc5-225">ハブへの接続のインスタンスを作成して構築するには、<xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="8bdc5-226">スケールアウトの相違点</span><span class="sxs-lookup"><span data-stu-id="8bdc5-226">Scaleout differences</span></span>

<span data-ttu-id="8bdc5-227">ASP.NET Core SignalR は、Azure SignalR Service と Redis をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="8bdc5-228">ASP.NET Core SignalR では、Azure SignalR サービスと Redis がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8bdc5-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="8bdc5-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="8bdc5-229">ASP.NET</span></span>

* <span data-ttu-id="8bdc5-230">[Azure Service Bus による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="8bdc5-231">[Redis による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="8bdc5-232">[SQL Server による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="8bdc5-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8bdc5-233">ASP.NET Core</span></span>

* <span data-ttu-id="8bdc5-234">[Azure SignalR サービス](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="8bdc5-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="8bdc5-235">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="8bdc5-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="8bdc5-236">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8bdc5-236">Additional resources</span></span>

* [<span data-ttu-id="8bdc5-237">ハブ</span><span class="sxs-lookup"><span data-stu-id="8bdc5-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8bdc5-238">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="8bdc5-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="8bdc5-239">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="8bdc5-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="8bdc5-240">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="8bdc5-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
