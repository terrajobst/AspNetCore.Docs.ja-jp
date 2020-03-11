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
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a>ASP.NET Core SignalR スケールアウトのための Redis バックプレーンの設定

[Andrew](https://twitter.com/anurse)、 [Brady](https://twitter.com/bradygaster)、および[Tom Dykstra](https://github.com/tdykstra)によって、

この記事では、ASP.NET Core SignalR アプリをスケールアウトするために使用する[Redis](https://redis.io/)サーバーの設定の SignalR固有の側面について説明します。

## <a name="set-up-a-redis-backplane"></a>Redis バックプレーンを設定する

* Redis サーバーをデプロイします。

  > [!IMPORTANT] 
  > 運用環境で使用する場合、Redis バックプレーンは、SignalR アプリと同じデータセンターで実行されている場合にのみお勧めします。 そうしないと、ネットワーク待機時間がパフォーマンスを低下します。 SignalR アプリが Azure クラウドで実行されている場合は、Redis バックプレーンではなく Azure SignalR サービスをお勧めします。 開発環境とテスト環境では、Azure Redis Cache サービスを使用できます。

  詳細については、次のリソースを参照してください。

  * <xref:signalr/scale>
  * [Redis のドキュメント](https://redis.io/)
  * [Azure Redis Cache のドキュメント](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* SignalR アプリで `Microsoft.AspNetCore.SignalR.Redis` NuGet パッケージをインストールします。
* `Startup.ConfigureServices` メソッドで、`AddSignalR`の後に `AddRedis` を呼び出します。

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* 必要に応じてオプションを構成します。
 
  ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。 `ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。

  次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。 この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* SignalR アプリで、次のいずれかの NuGet パッケージをインストールします。

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`-StackExchange に依存します。 Redis 2. X.X. これは ASP.NET Core 2.2 以降で推奨されるパッケージです。
  * `Microsoft.AspNetCore.SignalR.Redis`-StackExchange に依存します。 Redis 1. X.X. このパッケージは ASP.NET Core 3.0 以降には含まれていません。

* `Startup.ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>を呼び出します。

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 `Microsoft.AspNetCore.SignalR.Redis`を使用する場合は、<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>を呼び出します。

* 必要に応じてオプションを構成します。
 
  ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。 `ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。

  次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。 この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 `Microsoft.AspNetCore.SignalR.Redis`を使用する場合は、<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>を呼び出します。

  前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。

  Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* SignalR アプリで、次の NuGet パッケージをインストールします。

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* `Startup.ConfigureServices` メソッドで、<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>を呼び出します。

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* 必要に応じてオプションを構成します。
 
  ほとんどのオプションは、接続文字列または[configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)オブジェクトで設定できます。 `ConfigurationOptions` で指定されたオプションは、接続文字列で設定されているオプションをオーバーライドします。

  次の例は、`ConfigurationOptions` オブジェクトのオプションを設定する方法を示しています。 この例では、次の手順で説明するように、複数のアプリが同じ Redis インスタンスを共有できるように、チャネルプレフィックスを追加します。

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  前のコードでは、`options.Configuration` は接続文字列で指定された値で初期化されます。

  Redis オプションの詳細については、 [Stackexchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)を参照してください。

::: moniker-end

* 複数の SignalR アプリに1つの Redis サーバーを使用している場合は、SignalR アプリごとに異なるチャネルプレフィックスを使用します。

  チャネルプレフィックスを設定すると、別のチャネルプレフィックスを使用する他のアプリから、1つの SignalR アプリが分離されます。 異なるプレフィックスを割り当てない場合、1つのアプリからすべてのクライアントに送信されるメッセージは、Redis サーバーをバックプレーンとして使用するすべてのアプリのすべてのクライアントに送られます。

* 固定セッション用にサーバーファームの負荷分散ソフトウェアを構成します。 その方法に関するドキュメントの例を次に示します。

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis サーバーのエラー

Redis サーバーがダウンすると、SignalR は、メッセージが配信されないことを示す例外をスローします。 一般的な例外メッセージを次に示します。

* *メッセージの書き込みに失敗しました*
* *ハブメソッド ' MethodName ' を呼び出すことができませんでした*
* *Redis への接続に失敗しました*

SignalR は、サーバーがバックアップされたときにメッセージを送信するようにメッセージをバッファリングしません。 Redis サーバーが停止している間に送信されたメッセージはすべて失われます。

Redis サーバーが再び使用可能になると、SignalR 自動的に再接続されます。

### <a name="custom-behavior-for-connection-failures"></a>接続エラーのカスタム動作

Redis 接続エラーイベントの処理方法を示す例を次に示します。

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

## <a name="redis-clustering"></a>Redis クラスタリング

[Redis クラスタリング](https://redis.io/topics/cluster-spec)は、複数の redis サーバーを使用して高可用性を実現するための方法です。 クラスタリングは正式にはサポートされていませんが、動作する可能性があります。

## <a name="next-steps"></a>次のステップ:

詳細については、次のリソースを参照してください。

* <xref:signalr/scale>
* [Redis のドキュメント](https://redis.io/documentation)
* [StackExchange Redis のドキュメント](https://stackexchange.github.io/StackExchange.Redis/)
* [Azure Redis Cache のドキュメント](https://docs.microsoft.com/azure/redis-cache/)
