---
title: ASP.NET Core SignalR スケールアウトのための Redis バックプレーン
author: bradygaster
description: Redis バックプレーンを設定して、ASP.NET Core SignalR アプリのスケールアウトを有効にする方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 379d46fcaabb8eb0d04e521a5ad698229f947b7c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963916"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a><span data-ttu-id="577b9-103">ASP.NET Core SignalR スケールアウトのための Redis バックプレーンの設定</span><span class="sxs-lookup"><span data-stu-id="577b9-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="577b9-104">[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、</span><span class="sxs-lookup"><span data-stu-id="577b9-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="577b9-105">この記事では、ASP.NET Core SignalR アプリをスケールアウトするために使用する[Redis](https://redis.io/)サーバーの設定の SignalR固有の側面について説明します。</span><span class="sxs-lookup"><span data-stu-id="577b9-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="577b9-106">Redis バックプレーンを設定する</span><span class="sxs-lookup"><span data-stu-id="577b9-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="577b9-107">Redis サーバーをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="577b9-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="577b9-108">運用環境で使用する場合、Redis バックプレーンは、SignalR アプリと同じデータセンターで実行されている場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="577b9-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="577b9-109">そうしないと、ネットワーク待機時間がパフォーマンスを低下します。</span><span class="sxs-lookup"><span data-stu-id="577b9-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="577b9-110">SignalR アプリが Azure クラウドで実行されている場合は、Redis バックプレーンではなく Azure SignalR サービスをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="577b9-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="577b9-111">開発環境とテスト環境では、Azure Redis Cache サービスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="577b9-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="577b9-112">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="577b9-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="577b9-113">Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="577b9-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="577b9-114">Azure Redis Cache のドキュメント</span><span class="sxs-lookup"><span data-stu-id="577b9-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="577b9-115">SignalR アプリで `Microsoft.AspNetCore.SignalR.Redis` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="577b9-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="577b9-116">(`Microsoft.AspNetCore.SignalR.StackExchangeRedis` パッケージもありますが、ASP.NET Core 2.2 以降を対象としています)。</span><span class="sxs-lookup"><span data-stu-id="577b9-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="577b9-117">`Startup.ConfigureServices` メソッドで、`AddSignalR`の後に `AddRedis` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="577b9-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="577b9-118">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="577b9-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="577b9-119">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="577b9-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="577b9-120">`ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="577b9-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="577b9-121">次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="577b9-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="577b9-122">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="577b9-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="577b9-123">前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="577b9-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="577b9-124">SignalR アプリで、次のいずれかの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="577b9-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="577b9-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-StackExchange に依存します。 Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="577b9-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="577b9-126">これは ASP.NET Core 2.2 以降で推奨されるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="577b9-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="577b9-127">`Microsoft.AspNetCore.SignalR.Redis`-StackExchange に依存します。 Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="577b9-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="577b9-128">このパッケージは ASP.NET Core 3.0 では出荷されません。</span><span class="sxs-lookup"><span data-stu-id="577b9-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="577b9-129">`Startup.ConfigureServices` メソッドで、`AddSignalR`の後に `AddStackExchangeRedis` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="577b9-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="577b9-130">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="577b9-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="577b9-131">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="577b9-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="577b9-132">`ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="577b9-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="577b9-133">次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="577b9-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="577b9-134">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="577b9-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="577b9-135">前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="577b9-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="577b9-136">Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="577b9-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="577b9-137">複数の SignalR アプリに1つの Redis サーバーを使用している場合は、SignalR アプリごとに異なるチャネルプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="577b9-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="577b9-138">チャネルプレフィックスを設定すると、別のチャネルプレフィックスを使用する他のアプリから、1つの SignalR アプリが分離されます。</span><span class="sxs-lookup"><span data-stu-id="577b9-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="577b9-139">異なるプレフィックスを割り当てない場合、1つのアプリからすべてのクライアントに送信されるメッセージは、Redis サーバーをバックプレーンとして使用するすべてのアプリのすべてのクライアントに送られます。</span><span class="sxs-lookup"><span data-stu-id="577b9-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="577b9-140">固定セッション用にサーバーファームの負荷分散ソフトウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="577b9-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="577b9-141">その方法に関するドキュメントの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="577b9-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="577b9-142">IIS</span><span class="sxs-lookup"><span data-stu-id="577b9-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="577b9-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="577b9-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="577b9-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="577b9-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="577b9-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="577b9-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="577b9-146">Redis サーバーのエラー</span><span class="sxs-lookup"><span data-stu-id="577b9-146">Redis server errors</span></span>

<span data-ttu-id="577b9-147">Redis サーバーがダウンすると、SignalR は、メッセージが配信されないことを示す例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="577b9-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="577b9-148">一般的な例外メッセージを次に示します。</span><span class="sxs-lookup"><span data-stu-id="577b9-148">Some typical exception messages:</span></span>

* <span data-ttu-id="577b9-149">*メッセージの書き込みに失敗しました*</span><span class="sxs-lookup"><span data-stu-id="577b9-149">*Failed writing message*</span></span>
* <span data-ttu-id="577b9-150">*ハブメソッド ' MethodName ' を呼び出すことができませんでした*</span><span class="sxs-lookup"><span data-stu-id="577b9-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="577b9-151">*Redis への接続に失敗しました*</span><span class="sxs-lookup"><span data-stu-id="577b9-151">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="577b9-152"> は、サーバーがバックアップされたときにメッセージを送信するようにメッセージをバッファリングしません。</span><span class="sxs-lookup"><span data-stu-id="577b9-152"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="577b9-153">Redis サーバーが停止している間に送信されたメッセージはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="577b9-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="577b9-154">Redis サーバーが再び使用可能になると、SignalR 自動的に再接続されます。</span><span class="sxs-lookup"><span data-stu-id="577b9-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="577b9-155">接続エラーのカスタム動作</span><span class="sxs-lookup"><span data-stu-id="577b9-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="577b9-156">Redis 接続エラーイベントの処理方法を示す例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="577b9-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="577b9-157">Redis クラスタリング</span><span class="sxs-lookup"><span data-stu-id="577b9-157">Redis Clustering</span></span>

<span data-ttu-id="577b9-158">[Redis クラスタリング](https://redis.io/topics/cluster-spec)は、複数の redis サーバーを使用して高可用性を実現するための方法です。</span><span class="sxs-lookup"><span data-stu-id="577b9-158">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="577b9-159">クラスタリングは正式にはサポートされていませんが、動作する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="577b9-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="577b9-160">次のステップ</span><span class="sxs-lookup"><span data-stu-id="577b9-160">Next steps</span></span>

<span data-ttu-id="577b9-161">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="577b9-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="577b9-162">Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="577b9-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="577b9-163">StackExchange Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="577b9-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="577b9-164">Azure Redis Cache のドキュメント</span><span class="sxs-lookup"><span data-stu-id="577b9-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
