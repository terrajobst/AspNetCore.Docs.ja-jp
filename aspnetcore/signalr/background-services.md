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
ms.openlocfilehash: 000732115153eeafed3948c2a07acf77ffc34218
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73964044"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a>バックグラウンドサービスでのホスト ASP.NET Core SignalR

[Brady](https://twitter.com/bradygaster)による

この記事では、次のガイダンスを提供します。

* ASP.NET Core でホストされているバックグラウンドワーカープロセスを使用して SignalR ハブをホストする。
* .NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)内から接続されたクライアントにメッセージを送信する。

[サンプルコードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/)[する (ダウンロードする方法)](xref:index#how-to-download-a-sample)

## <a name="wire-up-opno-locsignalr-during-startup"></a>起動時の SignalR の接続

::: moniker range=">= aspnetcore-3.0"

バックグラウンドワーカープロセスのコンテキストで ASP.NET Core SignalR ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。 `Startup.ConfigureServices` メソッドで `services.AddSignalR` を呼び出すと、SignalRをサポートするために必要なサービスが ASP.NET Core 依存関係の挿入 (DI) 層に追加されます。 `Startup.Configure`では、ASP.NET Core 要求パイプラインでハブエンドポイントを接続するために、`UseEndpoints` コールバックで `MapHub` メソッドが呼び出されます。

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

バックグラウンドワーカープロセスのコンテキストで ASP.NET Core SignalR ハブをホストすることは、ASP.NET Core web アプリでハブをホストすることと同じです。 `Startup.ConfigureServices` メソッドで `services.AddSignalR` を呼び出すと、SignalRをサポートするために必要なサービスが ASP.NET Core 依存関係の挿入 (DI) 層に追加されます。 `Startup.Configure`では、`UseSignalR` メソッドを呼び出して、ASP.NET Core 要求パイプラインでハブエンドポイントを接続します。

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

前の例では、`ClockHub` クラスは、厳密に型指定されたハブを作成するために `Hub<T>` クラスを実装しています。 `ClockHub` は、エンドポイント `/hubs/clock`での要求に応答するように `Startup` クラスで構成されています。

厳密に型指定されたハブの詳細については、「 [SignalR のハブを使用](xref:signalr/hubs#strongly-typed-hubs)した ASP.NET Core」を参照してください。

> [!NOTE]
> この機能は、[ハブ\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1)クラスに限定されません。 また、 [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)など、[ハブ](xref:Microsoft.AspNetCore.SignalR.Hub)から継承されるすべてのクラスも機能します。

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

厳密に型指定された `ClockHub` によって使用されるインターフェイスは、`IClock` インターフェイスです。

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a>バックグラウンドサービスから SignalR ハブを呼び出す

起動時に、`Worker` クラス (`BackgroundService`) は `AddHostedService`を使用して接続されます。

```csharp
services.AddHostedService<Worker>();
```

SignalR は `Startup` フェーズ中にも接続されるため、各ハブは ASP.NET Core の HTTP 要求パイプライン内の個々のエンドポイントに接続されます。各ハブは、サーバー上の `IHubContext<T>` によって表されます。 ASP.NET Core の DI 機能を使用して、`BackgroundService` クラス、MVC コントローラークラス、Razor ページモデルなどのホスト層によってインスタンス化された他のクラスは、構築中に `IHubContext<ClockHub, IClock>` のインスタンスを受け入れることによって、サーバー側ハブへの参照を取得できます。

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

バックグラウンドサービスで `ExecuteAsync` メソッドが繰り返し呼び出されると、サーバーの現在の日付と時刻が、`ClockHub`を使用して接続されたクライアントに送信されます。

## <a name="react-to-opno-locsignalr-events-with-background-services"></a>バックグラウンドサービスを使用した SignalR イベントへの対応

SignalR 用の JavaScript クライアントを使用するシングルページアプリの場合と同様に、.NET デスクトップアプリでは <xref:signalr/dotnet-client>を使用してを使用できますが、`BackgroundService` または `IHostedService` の実装を使用して SignalR ハブに接続し、イベントに応答することもできます。

`ClockHubClient` クラスは、`IClock` インターフェイスと `IHostedService` インターフェイスの両方を実装します。 このようにすることで、`Startup` 中に接続して、サーバーからハブイベントに応答できるようになります。

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

初期化中に、`ClockHubClient` によって `HubConnection` のインスタンスが作成され、`IClock.ShowTime` メソッドがハブの `ShowTime` イベントのハンドラーとして使用されます。

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

`IHostedService.StartAsync` の実装では、`HubConnection` が非同期的に開始されます。

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

`IHostedService.StopAsync` メソッドでは、`HubConnection` が非同期的に破棄されます。

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>その他の技術情報

* [開始するには](xref:tutorials/signalr)
* [ハブ](xref:signalr/hubs)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
* [厳密に型指定されたハブ](xref:signalr/hubs#strongly-typed-hubs)
