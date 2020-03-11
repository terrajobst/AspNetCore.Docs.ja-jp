---
title: バックグラウンドサービスでのホスト ASP.NET Core SignalR
author: bradygaster
description: .NET Core BackgroundService クラスから SignalR クライアントにメッセージを送信する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 86319cc93febab18c29e2fb6366cef0d025943ba
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651584"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a><span data-ttu-id="f3a8b-103">バックグラウンドサービスでのホスト ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f3a8b-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="f3a8b-104">[Brady](https://twitter.com/bradygaster)による</span><span class="sxs-lookup"><span data-stu-id="f3a8b-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="f3a8b-105">この記事では、次のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-105">This article provides guidance for:</span></span>

* <span data-ttu-id="f3a8b-106">ASP.NET Core でホストされているバックグラウンドワーカープロセスを使用して SignalR ハブをホストする。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="f3a8b-107">.NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)内から接続されたクライアントにメッセージを送信する。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="f3a8b-108">[サンプルコードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f3a8b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-opno-locsignalr-in-startup"></a><span data-ttu-id="f3a8b-109">起動時に SignalR を有効にする</span><span class="sxs-lookup"><span data-stu-id="f3a8b-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3a8b-110">バックグラウンドワーカープロセスのコンテキストで ASP.NET Core SignalR ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="f3a8b-111">`Startup.ConfigureServices` メソッドで `services.AddSignalR` を呼び出すと、SignalRをサポートするために必要なサービスが ASP.NET Core 依存関係の挿入 (DI) 層に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="f3a8b-112">`Startup.Configure`では、ASP.NET Core 要求パイプラインでハブエンドポイントを接続するために、`UseEndpoints` コールバックで `MapHub` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f3a8b-113">バックグラウンドワーカープロセスのコンテキストで ASP.NET Core SignalR ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="f3a8b-114">`Startup.ConfigureServices` メソッドで `services.AddSignalR` を呼び出すと、SignalRをサポートするために必要なサービスが ASP.NET Core 依存関係の挿入 (DI) 層に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="f3a8b-115">`Startup.Configure`では、`UseSignalR` メソッドを呼び出して、ASP.NET Core 要求パイプラインのハブエンドポイントを接続します。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="f3a8b-116">前の例では、`ClockHub` クラスは、厳密に型指定されたハブを作成するために `Hub<T>` クラスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="f3a8b-117">`ClockHub` は、エンドポイント `/hubs/clock`での要求に応答するように `Startup` クラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="f3a8b-118">厳密に型指定されたハブの詳細については、「 [SignalR のハブを使用](xref:signalr/hubs#strongly-typed-hubs)した ASP.NET Core」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="f3a8b-119">この機能は、[ハブ\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)クラスに限定されません。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="f3a8b-120">また、 [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)など、[ハブ](xref:Microsoft.AspNetCore.SignalR.Hub)から継承されるすべてのクラスも機能します。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="f3a8b-121">厳密に型指定された `ClockHub` によって使用されるインターフェイスは、`IClock` インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a><span data-ttu-id="f3a8b-122">バックグラウンドサービスから SignalR ハブを呼び出す</span><span class="sxs-lookup"><span data-stu-id="f3a8b-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="f3a8b-123">起動時には、`AddHostedService`を使用して、`BackgroundService``Worker` クラスが有効になります。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="f3a8b-124">SignalR は `Startup` フェーズ中にも有効になるため、各ハブは ASP.NET Core の HTTP 要求パイプライン内の個々のエンドポイントにアタッチされます。各ハブは、サーバー上の `IHubContext<T>` によって表されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="f3a8b-125">ASP.NET Core の DI 機能を使用して、`BackgroundService` クラス、MVC コントローラークラス、Razor ページモデルなどのホスト層によってインスタンス化された他のクラスは、構築中に `IHubContext<ClockHub, IClock>` のインスタンスを受け入れることによって、サーバー側ハブへの参照を取得できます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="f3a8b-126">バックグラウンドサービスで `ExecuteAsync` メソッドが繰り返し呼び出されると、サーバーの現在の日付と時刻が、`ClockHub`を使用して接続されたクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-opno-locsignalr-events-with-background-services"></a><span data-ttu-id="f3a8b-127">バックグラウンドサービスを使用した SignalR イベントへの対応</span><span class="sxs-lookup"><span data-stu-id="f3a8b-127">React to SignalR events with background services</span></span>

<span data-ttu-id="f3a8b-128">SignalR 用の JavaScript クライアントを使用するシングルページアプリの場合と同様に、.NET デスクトップアプリでは <xref:signalr/dotnet-client>を使用してを使用できますが、`BackgroundService` または `IHostedService` の実装を使用して SignalR ハブに接続し、イベントに応答することもできます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="f3a8b-129">`ClockHubClient` クラスは、`IClock` インターフェイスと `IHostedService` インターフェイスの両方を実装します。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="f3a8b-130">このようにして、`Startup` 中に有効にし、サーバーからハブイベントに応答して実行することができます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="f3a8b-131">初期化中に、`ClockHubClient` によって `HubConnection` のインスタンスが作成され、ハブの `ShowTime` イベントのハンドラーとして `IClock.ShowTime` メソッドが有効になります。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="f3a8b-132">`IHostedService.StartAsync` の実装では、`HubConnection` が非同期的に開始されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="f3a8b-133">`IHostedService.StopAsync` メソッドでは、`HubConnection` が非同期的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="f3a8b-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="f3a8b-134">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f3a8b-134">Additional resources</span></span>

* [<span data-ttu-id="f3a8b-135">開始するには</span><span class="sxs-lookup"><span data-stu-id="f3a8b-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f3a8b-136">ハブ</span><span class="sxs-lookup"><span data-stu-id="f3a8b-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f3a8b-137">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="f3a8b-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="f3a8b-138">厳密に型指定されたハブ</span><span class="sxs-lookup"><span data-stu-id="f3a8b-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
