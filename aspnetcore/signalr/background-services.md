---
title: バックグラウンドサービスでのホスト ASP.NET Core SignalR
author: bradygaster
description: .NET Core BackgroundService クラスから SignalR クライアントにメッセージを送信する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773931"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="8c981-103">バックグラウンドサービスでのホスト ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="8c981-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="8c981-104">[Brady](https://twitter.com/bradygaster)による</span><span class="sxs-lookup"><span data-stu-id="8c981-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="8c981-105">この記事では、次のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="8c981-105">This article provides guidance for:</span></span>

* <span data-ttu-id="8c981-106">ASP.NET Core でホストされたバックグラウンドワーカープロセスを使用して SignalR Hub をホストする。</span><span class="sxs-lookup"><span data-stu-id="8c981-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="8c981-107">.NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)内から接続されたクライアントにメッセージを送信する。</span><span class="sxs-lookup"><span data-stu-id="8c981-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="8c981-108">[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/)する[(ダウンロード方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="8c981-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="wire-up-signalr-during-startup"></a><span data-ttu-id="8c981-109">スタートアップ時の SignalR の接続</span><span class="sxs-lookup"><span data-stu-id="8c981-109">Wire up SignalR during startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c981-110">バックグラウンドワーカープロセスのコンテキストで ASP.NET Core SignalR ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="8c981-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="8c981-111">メソッドで、を呼び`services.AddSignalR`出すと、SignalR をサポートするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) レイヤーに追加されます。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8c981-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="8c981-112">で`Startup.Configure`は、 `UseEndpoints`メソッドがコールバックで呼び出され、ASP.NET Core 要求パイプラインでハブエンドポイントを接続します`MapHub` 。</span><span class="sxs-lookup"><span data-stu-id="8c981-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

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

<span data-ttu-id="8c981-113">バックグラウンドワーカープロセスのコンテキストで ASP.NET Core SignalR ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。</span><span class="sxs-lookup"><span data-stu-id="8c981-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="8c981-114">メソッドで、を呼び`services.AddSignalR`出すと、SignalR をサポートするために必要なサービスが ASP.NET Core 依存関係挿入 (DI) レイヤーに追加されます。 `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8c981-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="8c981-115">で`Startup.Configure`は、 `UseSignalR`メソッドを呼び出して、ASP.NET Core 要求パイプラインでハブエンドポイントを接続します。</span><span class="sxs-lookup"><span data-stu-id="8c981-115">In `Startup.Configure`, the `UseSignalR` method is called to wire up the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="8c981-116">前の例では、 `ClockHub`クラスは、 `Hub<T>`厳密に型指定されたハブを作成するクラスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="8c981-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="8c981-117">は`ClockHub` 、エンドポイント`/hubs/clock`での`Startup`要求に応答するようにクラスで構成されています。</span><span class="sxs-lookup"><span data-stu-id="8c981-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="8c981-118">厳密に型指定されたハブの詳細については、「 [Use hub in ASP.NET Core SignalR」](xref:signalr/hubs#strongly-typed-hubs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c981-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="8c981-119">この機能は[\<ハブ t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)クラスに限定されません。</span><span class="sxs-lookup"><span data-stu-id="8c981-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="8c981-120">また、 [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)など、[ハブ](xref:Microsoft.AspNetCore.SignalR.Hub)から継承されるすべてのクラスも機能します。</span><span class="sxs-lookup"><span data-stu-id="8c981-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="8c981-121">厳密に型指定`ClockHub` `IClock`されたによって使用されるインターフェイスは、インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="8c981-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="8c981-122">バックグラウンドサービスから SignalR Hub を呼び出す</span><span class="sxs-lookup"><span data-stu-id="8c981-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="8c981-123">起動時`Worker`に、クラス`BackgroundService`はを使用して`AddHostedService`接続されます。</span><span class="sxs-lookup"><span data-stu-id="8c981-123">During startup, the `Worker` class, a `BackgroundService`, is wired up using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="8c981-124">SignalR は`Startup`フェーズ中にも接続されるため、各ハブは ASP.NET Core の HTTP 要求パイプラインの個々のエンドポイントにアタッチされます。各ハブは`IHubContext<T>` 、サーバー上のによって表されます。</span><span class="sxs-lookup"><span data-stu-id="8c981-124">Since SignalR is also wired up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="8c981-125">ASP.NET Core の DI 機能を使用して、 `BackgroundService`クラス、MVC コントローラークラス、Razor ページモデルなどのホスト層によってインスタンス化された他のクラスは、構築中にのインスタンスを受け入れることによって、サーバー側ハブへの`IHubContext<ClockHub, IClock>`参照を取得できます。</span><span class="sxs-lookup"><span data-stu-id="8c981-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="8c981-126">バックグラウンドサービスで`ClockHub`メソッドが繰り返し呼び出されると、サーバーの現在の日付と時刻が、を使用して接続されたクライアントに送信`ExecuteAsync`されます。</span><span class="sxs-lookup"><span data-stu-id="8c981-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="8c981-127">バックグラウンドサービスを使用した SignalR イベントへの対応</span><span class="sxs-lookup"><span data-stu-id="8c981-127">React to SignalR events with background services</span></span>

<span data-ttu-id="8c981-128">SignalR 用の JavaScript クライアントを使用するシングルページアプリの場合と同様に、.net デスクトップアプリでを<xref:signalr/dotnet-client>使用`BackgroundService`して`IHostedService`を使用する場合は、またはの実装を使用して SignalR hub に接続し、イベントに応答することもできます。</span><span class="sxs-lookup"><span data-stu-id="8c981-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="8c981-129">クラス`ClockHubClient`は、インターフェイスと`IClock` `IHostedService`インターフェイスの両方を実装します。</span><span class="sxs-lookup"><span data-stu-id="8c981-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="8c981-130">これにより、の実行中`Startup`に接続して、サーバーからハブイベントに応答できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8c981-130">This way it can be wired up during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="8c981-131">初期化`ClockHubClient`中に、は`HubConnection`のインスタンスを作成し`IClock.ShowTime` 、ハブの`ShowTime`イベントのハンドラーとしてメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="8c981-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and wires up the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="8c981-132">実装では、 `HubConnection`が非同期的に開始されます。 `IHostedService.StartAsync`</span><span class="sxs-lookup"><span data-stu-id="8c981-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="8c981-133">メソッドの`IHostedService.StopAsync`実行中`HubConnection` 、は非同期的に破棄されます。</span><span class="sxs-lookup"><span data-stu-id="8c981-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="8c981-134">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8c981-134">Additional resources</span></span>

* [<span data-ttu-id="8c981-135">開始するには</span><span class="sxs-lookup"><span data-stu-id="8c981-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="8c981-136">ハブ</span><span class="sxs-lookup"><span data-stu-id="8c981-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8c981-137">Azure に発行する</span><span class="sxs-lookup"><span data-stu-id="8c981-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="8c981-138">厳密に型指定されたハブ</span><span class="sxs-lookup"><span data-stu-id="8c981-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
