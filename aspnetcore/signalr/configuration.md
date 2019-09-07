---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core SignalR アプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773932"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR の構成

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack のシリアル化オプション

ASP.NET Core SignalR は、メッセージをエンコードするための2つのプロトコルをサポートしています。[JSON](https://www.json.org/)および[messagepack](https://msgpack.org/index.html)。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化は、 [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用してサーバー上で構成できます。これは `Startup.ConfigureServices` メソッドの [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) の後に追加できます。 メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。 そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と`JsonSerializerSettings`戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。 詳細については、 [JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。

たとえば、"キャメルケース" という既定の名前の代わりに "" プロパティ名を使用するようにシリアライザーを構成するには、次のコードを使用します。

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

.Net クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。 拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。

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

> [!NOTE]
> 現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。

### <a name="messagepack-serialization-options"></a>MessagePack のシリアル化オプション

MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。 詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバーオプションの構成

次の表では、SignalR hub を構成するためのオプションについて説明します。

::: moniker range=">= aspnetcore-3.0"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。 クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。 推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。|
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更`KeepAliveInterval`する場合は、 `ServerTimeout`クライアントの`serverTimeoutInMilliseconds`設定を/変更します。 推奨`ServerTimeout` / `KeepAliveInterval`値は、値の倍精度浮動小数点数です。 `serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |
| `StreamBufferCapacity` | `10` | クライアントアップロードストリームに対してバッファーできる項目の最大数。 この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。|
| `MaximumReceiveMessageSize` | 32 KB | 1つの受信ハブメッセージの最大サイズ。 |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。 クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。 推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。|
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更`KeepAliveInterval`する場合は、 `ServerTimeout`クライアントの`serverTimeoutInMilliseconds`設定を/変更します。 推奨`ServerTimeout` / `KeepAliveInterval`値は、値の倍精度浮動小数点数です。 `serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更`KeepAliveInterval`する場合は、 `ServerTimeout`クライアントの`serverTimeoutInMilliseconds`設定を/変更します。 推奨`ServerTimeout` / `KeepAliveInterval`値は、値の倍精度浮動小数点数です。 `serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |

::: moniker-end

オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。

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

1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書きします。また、次のものを使用し<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>て構成できます。

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>詳細な HTTP 構成オプション

::: moniker range=">= aspnetcore-3.0"

トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で`Startup.Configure` [maphub\<T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。

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

トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で`Startup.Configure` [maphub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。

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

次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。

::: moniker range=">= aspnetcore-3.0"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。 この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。 |
| `AuthorizationData` | ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。 この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 以下を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 以下を参照してください。 | Websocket トランスポートに固有の追加オプション。 |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | クライアントから受信した、サーバーがバッファーする最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを受け取ることができますが、メモリの消費に悪影響を与える可能性があります。 |
| `AuthorizationData` | ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバッファーするアプリによって送信される最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 以下を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 以下を参照してください。 | Websocket トランスポートに固有の追加オプション。 |

::: moniker-end

長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。 |

WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。 |

## <a name="configure-client-options"></a>クライアントオプションを構成する

クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。 Java クライアントでも使用できますが`HttpHubConnectionBuilder` 、サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。

### <a name="configure-logging"></a>ログの構成

ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。 ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。 詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。

> [!NOTE]
> ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。 完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。

たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。 拡張メソッド`AddConsole`を呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。 生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。 ログは、ブラウザーのコンソールウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

`LogLevel`値の代わりに、ログレベル名を表す`string`値を指定することもできます。 これは、 `LogLevel`定数にアクセスできない環境で SignalR のログ記録を構成する場合に便利です。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

次の表は、使用可能なログレベルを示しています。 ログに記録される`configureLogging` **最小**ログレベルを設定するために指定する値。 このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。

| String                      | ログ レベル               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**または**`information` | `LogLevel.Information` |
| `warn`**または**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> 完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。

ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。

SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。 の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。 既定では、すべてのトランスポートが有効になっています。

たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Java クライアントでは、 `withTransport` `HttpHubConnectionBuilder`でメソッドを使用してトランスポートを選択します。 Java クライアントでは、既定で Websocket トランスポートが使用されます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。

::: moniker-end

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成する

SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。 .Net クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。 JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。 このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。

.Net クライアント`AccessTokenProvider`では、の`WithUrl`オプションデリゲートを使用してオプションを指定できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。

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

SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列 >](https://reactivex.io/documentation/single.html)を指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブオプションを構成する

タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。 この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。 |

.Net クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。 この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。 |

# <a name="javatabjava"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。 この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。 |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。 この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |

.Net クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。 この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。 |

# <a name="javatabjava"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。 この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、サーバーの`KeepAliveInterval`値の少なくとも2倍の数で、ping が到着するまでの時間を確保します。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |

::: moniker-end

---

### <a name="configure-additional-options"></a>追加のオプションを構成する

追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript `HubConnectionBuilder`では) メソッドまたはのさまざまな構成 api で構成できます。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | 説明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションの手順`true`をスキップするには、これをに設定します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求と共に送信する資格情報。 |
| `CloseTimeout` | 5 秒 | Websocket のみ。 サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。 この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。 WebSocket 接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。 既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。 **ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。 |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| JavaScript オプション | 既定値 | 説明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `skipNegotiation` | `false` | ネゴシエーションの手順`true`をスキップするには、これをに設定します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |

# <a name="javatabjava"></a>[Java](#tab/java)

| Java オプション | 既定値 | 説明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションの手順`true`をスキップするには、これをに設定します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |

---

.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
