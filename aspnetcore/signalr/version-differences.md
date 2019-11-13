---
title: SignalR と ASP.NET Core の違い SignalR
author: bradygaster
description: SignalR と ASP.NET Core の違い SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963722"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="b5fa2-103">ASP.NET SignalR と ASP.NET Core SignalR の違い</span><span class="sxs-lookup"><span data-stu-id="b5fa2-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="b5fa2-104">ASP.NET Core SignalR は、ASP.NET SignalRのクライアントまたはサーバーと互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="b5fa2-105">この記事では ASP.NET Core SignalRで削除または変更された機能について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="b5fa2-106">SignalR のバージョンを特定する方法</span><span class="sxs-lookup"><span data-stu-id="b5fa2-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="b5fa2-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="b5fa2-107">ASP.NET SignalR</span></span> | <span data-ttu-id="b5fa2-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="b5fa2-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="b5fa2-109">サーバー NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="b5fa2-109">Server NuGet Package</span></span> | <span data-ttu-id="b5fa2-110">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="b5fa2-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="b5fa2-112">[SignalR AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="b5fa2-113">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-113">(.NET Framework)</span></span> |
| <span data-ttu-id="b5fa2-114">クライアント NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="b5fa2-114">Client NuGet Packages</span></span> | <span data-ttu-id="b5fa2-115">[SignalR。Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-115">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="b5fa2-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="b5fa2-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="b5fa2-118">クライアントの npm パッケージ</span><span class="sxs-lookup"><span data-stu-id="b5fa2-118">Client npm Package</span></span> | [<span data-ttu-id="b5fa2-119">signalr</span><span class="sxs-lookup"><span data-stu-id="b5fa2-119">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="b5fa2-120">Java クライアント</span><span class="sxs-lookup"><span data-stu-id="b5fa2-120">Java Client</span></span> | <span data-ttu-id="b5fa2-121">[GitHub リポジトリ](https://github.com/SignalR/java-client)(非推奨)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-121">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="b5fa2-122">Maven パッケージ[com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-122">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="b5fa2-123">サーバーアプリの種類</span><span class="sxs-lookup"><span data-stu-id="b5fa2-123">Server App Type</span></span> | <span data-ttu-id="b5fa2-124">ASP.NET (System.web) または OWIN 自己ホスト</span><span class="sxs-lookup"><span data-stu-id="b5fa2-124">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="b5fa2-125">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5fa2-125">ASP.NET Core</span></span> |
| <span data-ttu-id="b5fa2-126">サポートされているサーバープラットフォーム</span><span class="sxs-lookup"><span data-stu-id="b5fa2-126">Supported Server Platforms</span></span> | <span data-ttu-id="b5fa2-127">.NET Framework 4.5 以降</span><span class="sxs-lookup"><span data-stu-id="b5fa2-127">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="b5fa2-128">.NET Framework 4.6.1 以降</span><span class="sxs-lookup"><span data-stu-id="b5fa2-128">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="b5fa2-129">.NET Core 2.1 以降</span><span class="sxs-lookup"><span data-stu-id="b5fa2-129">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="b5fa2-130">機能の違い</span><span class="sxs-lookup"><span data-stu-id="b5fa2-130">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="b5fa2-131">自動再接続</span><span class="sxs-lookup"><span data-stu-id="b5fa2-131">Automatic reconnects</span></span>

<span data-ttu-id="b5fa2-132">自動再接続は、ASP.NET Core SignalRではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-132">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="b5fa2-133">クライアントが切断されている場合、ユーザーは再接続するときに、新しい接続を明示的に開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-133">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="b5fa2-134">ASP.NET SignalRでは、接続が切断された場合、SignalR サーバーへの再接続を試みます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-134">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="b5fa2-135">プロトコルのサポート</span><span class="sxs-lookup"><span data-stu-id="b5fa2-135">Protocol support</span></span>

<span data-ttu-id="b5fa2-136">ASP.NET Core SignalR は、 [MessagePack](xref:signalr/messagepackhubprotocol) に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-136">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="b5fa2-137">さらに、カスタムプロトコルを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-137">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="b5fa2-138">トランスポート</span><span class="sxs-lookup"><span data-stu-id="b5fa2-138">Transports</span></span>

<span data-ttu-id="b5fa2-139">ASP.NET Core SignalR では、Forever Frame トランスポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-139">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="b5fa2-140">サーバーの相違点</span><span class="sxs-lookup"><span data-stu-id="b5fa2-140">Differences on the server</span></span>

<span data-ttu-id="b5fa2-141">ASP.NET Core SignalR のサーバー側ライブラリは、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)パッケージに含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-141">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="b5fa2-142">ASP.NET Core SignalR は ASP.NET Core ミドルウェアであるため、`Startup.ConfigureServices` 内で [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) を呼び出すことによって構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-142">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5fa2-143">ルーティングを構成するには、 `Startup.Configure`メソッドの[UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) メソッド呼び出し内でハブにルートをマップします。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-143">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b5fa2-144">ルーティングを構成するには、 `Startup.Configure` メソッドの[UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) メソッド呼び出し内でハブにルートをマップします。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-144">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="b5fa2-145">固定セッション</span><span class="sxs-lookup"><span data-stu-id="b5fa2-145">Sticky sessions</span></span>

<span data-ttu-id="b5fa2-146">ASP.NET SignalR のスケールアウトモデルを使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-146">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="b5fa2-147">ASP.NET Core SignalR では、クライアントは接続中に同じサーバーと通信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-147">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="b5fa2-148">Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-148">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="b5fa2-149">[Azure ](/azure/azure-signalr/) ServiceSignalR を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-149">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="b5fa2-150">接続ごとに1つのハブ</span><span class="sxs-lookup"><span data-stu-id="b5fa2-150">Single hub per connection</span></span>

<span data-ttu-id="b5fa2-151">ASP.NET Core SignalRでは、接続モデルが単純化されています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-151">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="b5fa2-152">複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-152">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="b5fa2-153">ストリーム</span><span class="sxs-lookup"><span data-stu-id="b5fa2-153">Streaming</span></span>

<span data-ttu-id="b5fa2-154">ASP.NET Core SignalR では、ハブからクライアントへの[データのストリーミング](xref:signalr/streaming)がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-154">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="b5fa2-155">状態</span><span class="sxs-lookup"><span data-stu-id="b5fa2-155">State</span></span>

<span data-ttu-id="b5fa2-156">クライアントとハブ (多くの場合 HubState と呼ばれます) の間で任意の状態を渡す機能と進行状況メッセージのサポートは削除されました。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-156">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="b5fa2-157">現時点では、対応するハブプロキシはありません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-157">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="b5fa2-158">PersistentConnection の削除</span><span class="sxs-lookup"><span data-stu-id="b5fa2-158">PersistentConnection removal</span></span>

<span data-ttu-id="b5fa2-159">ASP.NET Core SignalRでは、 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))クラスは削除されています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-159">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="b5fa2-160">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="b5fa2-160">GlobalHost</span></span>

<span data-ttu-id="b5fa2-161">ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-161">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="b5fa2-162">サービスは DI を使用して [HubContext](xref:signalr/hubcontext) にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-162">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="b5fa2-163">ASP.NET SignalR で`HubContext`を取得するために使用される `GlobalHost` オブジェクトは、ASP.NET Core SignalR に存在しません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-163">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="b5fa2-164">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="b5fa2-164">HubPipeline</span></span>

<span data-ttu-id="b5fa2-165">ASP.NET Core SignalR は`HubPipeline`モジュールをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-165">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="b5fa2-166">クライアントの相違点</span><span class="sxs-lookup"><span data-stu-id="b5fa2-166">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="b5fa2-167">TypeScript</span><span class="sxs-lookup"><span data-stu-id="b5fa2-167">TypeScript</span></span>

<span data-ttu-id="b5fa2-168">ASP.NET Core SignalR クライアントは[TypeScript](https://www.typescriptlang.org/) で記述されています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-168">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="b5fa2-169">Javascript[クライアント](xref:signalr/javascript-client)を使用する場合は、javascript または TypeScript で記述できます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-169">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="b5fa2-170">JavaScript クライアントは[npm](https://www.npmjs.com/)でホストされます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-170">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="b5fa2-171">以前のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを通じて取得されました。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-171">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="b5fa2-172">コアバージョン[@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) では、npm パッケージに JavaScript ライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-172">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="b5fa2-173">このパッケージは、Razor プロジェクトと MVC プロジェクトの両方の**ASP.NET Core Web アプリケーション**テンプレートの一部です。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-173">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="b5fa2-174">`@aspnet/signalr` npm パッケージを取得してインストールするには、npm を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-174">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="b5fa2-175">jQuery</span><span class="sxs-lookup"><span data-stu-id="b5fa2-175">jQuery</span></span>

<span data-ttu-id="b5fa2-176">jQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-176">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="b5fa2-177">Internet Explorer のサポート</span><span class="sxs-lookup"><span data-stu-id="b5fa2-177">Internet Explorer support</span></span>

<span data-ttu-id="b5fa2-178">ASP.NET Core SignalR には、Microsoft Internet Explorer 11 以降 (ASP.NET SignalR サポートされている Microsoft Internet Explorer 8 以降が必要です) が必要です。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-178">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="b5fa2-179">JavaScript クライアントメソッドの構文</span><span class="sxs-lookup"><span data-stu-id="b5fa2-179">JavaScript client method syntax</span></span>

<span data-ttu-id="b5fa2-180">JavaScript の構文は、以前のバージョンの SignalRから変更されています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-180">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="b5fa2-181">`$connection` オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-181">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="b5fa2-182">[on](/javascript/api/@aspnet/signalr/HubConnection#on) メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-182">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="b5fa2-183">クライアントメソッドを作成したら、ハブ接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-183">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="b5fa2-184">[catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) メソッドをチェーンして、エラーをログに記録または処理します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-184">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="b5fa2-185">ハブプロキシ</span><span class="sxs-lookup"><span data-stu-id="b5fa2-185">Hub proxies</span></span>

<span data-ttu-id="b5fa2-186">ハブプロキシが自動的に生成されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-186">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="b5fa2-187">代わりに、メソッド名が文字列として[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API に渡されます。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-187">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="b5fa2-188">.NET とその他のクライアント</span><span class="sxs-lookup"><span data-stu-id="b5fa2-188">.NET and other clients</span></span>

<span data-ttu-id="b5fa2-189">`Microsoft.AspNetCore.SignalR.Client` NuGet パッケージには ASP.NET Core SignalR 用の .NET クライアントライブラリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-189">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="b5fa2-190">[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)を使用して、ハブへの接続のインスタンスを作成し、構築します。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-190">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="b5fa2-191">スケールアウトの相違点</span><span class="sxs-lookup"><span data-stu-id="b5fa2-191">Scaleout differences</span></span>

<span data-ttu-id="b5fa2-192">ASP.NET Core SignalR は、Azure SignalR Service と Redis をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-192">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="b5fa2-193">ASP.NET Core SignalR では、Azure SignalR サービスと Redis がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b5fa2-193">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="b5fa2-194">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="b5fa2-194">ASP.NET</span></span>

* <span data-ttu-id="b5fa2-195">[Azure Service Bus による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-195">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="b5fa2-196">[Redis による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-196">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="b5fa2-197">[SQL Server による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-197">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="b5fa2-198">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5fa2-198">ASP.NET Core</span></span>

* <span data-ttu-id="b5fa2-199">[Azure SignalR サービス](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="b5fa2-199">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="b5fa2-200">Redis バックプレーン</span><span class="sxs-lookup"><span data-stu-id="b5fa2-200">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="b5fa2-201">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b5fa2-201">Additional resources</span></span>

* [<span data-ttu-id="b5fa2-202">ハブ</span><span class="sxs-lookup"><span data-stu-id="b5fa2-202">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b5fa2-203">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="b5fa2-203">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="b5fa2-204">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="b5fa2-204">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b5fa2-205">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="b5fa2-205">Supported platforms</span></span>](xref:signalr/supported-platforms)
