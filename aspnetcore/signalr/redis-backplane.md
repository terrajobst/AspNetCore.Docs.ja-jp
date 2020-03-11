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
ms.openlocfilehash: 0461fc6a212ba78111bc2054cca74951721c5820
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652664"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a><span data-ttu-id="35b0c-103">ASP.NET Core SignalR スケールアウトのための Redis バックプレーンの設定</span><span class="sxs-lookup"><span data-stu-id="35b0c-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="35b0c-104">[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、</span><span class="sxs-lookup"><span data-stu-id="35b0c-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="35b0c-105">この記事では、ASP.NET Core SignalR アプリをスケールアウトするために使用する[Redis](https://redis.io/)サーバーの設定の SignalR固有の側面について説明します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="35b0c-106">Redis バックプレーンを設定する</span><span class="sxs-lookup"><span data-stu-id="35b0c-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="35b0c-107">Redis サーバーをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="35b0c-108">運用環境で使用する場合、Redis バックプレーンは、SignalR アプリと同じデータセンターで実行されている場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="35b0c-109">そうしないと、ネットワーク待機時間がパフォーマンスを低下します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="35b0c-110">SignalR アプリが Azure クラウドで実行されている場合は、Redis バックプレーンではなく Azure SignalR サービスをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="35b0c-111">開発環境とテスト環境では、Azure Redis Cache サービスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="35b0c-112">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="35b0c-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="35b0c-113">Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="35b0c-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="35b0c-114">Azure Redis Cache のドキュメント</span><span class="sxs-lookup"><span data-stu-id="35b0c-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="35b0c-115">SignalR アプリで `Microsoft.AspNetCore.SignalR.Redis` NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="35b0c-116">`Startup.ConfigureServices` メソッドで、`AddSignalR`の後に `AddRedis` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="35b0c-117">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="35b0c-118">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="35b0c-119">`ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="35b0c-120">次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="35b0c-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="35b0c-121">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="35b0c-122">前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="35b0c-123">SignalR アプリで、次のいずれかの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="35b0c-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-StackExchange に依存します。 Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="35b0c-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="35b0c-125">これは ASP.NET Core 2.2 以降で推奨されるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="35b0c-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="35b0c-126">`Microsoft.AspNetCore.SignalR.Redis`-StackExchange に依存します。 Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="35b0c-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="35b0c-127">このパッケージは ASP.NET Core 3.0 以降には含まれていません。</span><span class="sxs-lookup"><span data-stu-id="35b0c-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="35b0c-128">`Startup.ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="35b0c-129">`Microsoft.AspNetCore.SignalR.Redis`を使用する場合は、<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="35b0c-130">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="35b0c-131">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="35b0c-132">`ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="35b0c-133">次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="35b0c-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="35b0c-134">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="35b0c-135">`Microsoft.AspNetCore.SignalR.Redis`を使用する場合は、<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="35b0c-136">前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="35b0c-137">Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="35b0c-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="35b0c-138">SignalR アプリで、次の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="35b0c-139">`Startup.ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="35b0c-140">必要に応じてオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="35b0c-141">ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="35b0c-142">`ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="35b0c-143">次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="35b0c-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="35b0c-144">この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="35b0c-145">前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="35b0c-146">Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="35b0c-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="35b0c-147">複数の SignalR アプリに1つの Redis サーバーを使用している場合は、SignalR アプリごとに異なるチャネルプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="35b0c-148">チャネルプレフィックスを設定すると、別のチャネルプレフィックスを使用する他のアプリから、1つの SignalR アプリが分離されます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="35b0c-149">異なるプレフィックスを割り当てない場合、1つのアプリからすべてのクライアントに送信されるメッセージは、Redis サーバーをバックプレーンとして使用するすべてのアプリのすべてのクライアントに送られます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="35b0c-150">固定セッション用にサーバーファームの負荷分散ソフトウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="35b0c-151">その方法に関するドキュメントの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="35b0c-152">IIS</span><span class="sxs-lookup"><span data-stu-id="35b0c-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="35b0c-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="35b0c-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="35b0c-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="35b0c-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="35b0c-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="35b0c-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="35b0c-156">Redis サーバーのエラー</span><span class="sxs-lookup"><span data-stu-id="35b0c-156">Redis server errors</span></span>

<span data-ttu-id="35b0c-157">Redis サーバーがダウンすると、SignalR は、メッセージが配信されないことを示す例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="35b0c-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="35b0c-158">一般的な例外メッセージを次に示します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-158">Some typical exception messages:</span></span>

* <span data-ttu-id="35b0c-159">*メッセージの書き込みに失敗しました*</span><span class="sxs-lookup"><span data-stu-id="35b0c-159">*Failed writing message*</span></span>
* <span data-ttu-id="35b0c-160">*ハブメソッド ' MethodName ' を呼び出すことができませんでした*</span><span class="sxs-lookup"><span data-stu-id="35b0c-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="35b0c-161">*Redis への接続に失敗しました*</span><span class="sxs-lookup"><span data-stu-id="35b0c-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="35b0c-162"> は、サーバーがバックアップされたときにメッセージを送信するようにメッセージをバッファリングしません。</span><span class="sxs-lookup"><span data-stu-id="35b0c-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="35b0c-163">Redis サーバーが停止している間に送信されたメッセージはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-163">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="35b0c-164">Redis サーバーが再び使用可能になると、SignalR 自動的に再接続されます。</span><span class="sxs-lookup"><span data-stu-id="35b0c-164">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="35b0c-165">接続エラーのカスタム動作</span><span class="sxs-lookup"><span data-stu-id="35b0c-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="35b0c-166">Redis 接続エラーイベントの処理方法を示す例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="35b0c-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="35b0c-167">Redis クラスタリング</span><span class="sxs-lookup"><span data-stu-id="35b0c-167">Redis Clustering</span></span>

<span data-ttu-id="35b0c-168">[Redis クラスタリング](https://redis.io/topics/cluster-spec)は、複数の redis サーバーを使用して高可用性を実現するための方法です。</span><span class="sxs-lookup"><span data-stu-id="35b0c-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="35b0c-169">クラスタリングは正式にはサポートされていませんが、動作する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="35b0c-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="35b0c-170">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="35b0c-170">Next steps</span></span>

<span data-ttu-id="35b0c-171">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="35b0c-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="35b0c-172">Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="35b0c-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="35b0c-173">StackExchange Redis のドキュメント</span><span class="sxs-lookup"><span data-stu-id="35b0c-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="35b0c-174">Azure Redis Cache のドキュメント</span><span class="sxs-lookup"><span data-stu-id="35b0c-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
