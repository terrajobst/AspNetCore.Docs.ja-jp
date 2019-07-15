---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core SignalR アプリケーションを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813448"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR の構成

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack シリアル化オプション

ASP.NET Core SignalR には、メッセージをエンコードするための 2 つのプロトコルがサポートされています。[JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)します。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化を使用して、サーバーで構成できます、 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドは後に、追加する[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)で、`Startup.ConfigureServices`メソッド。 `AddJsonProtocol`メソッドが受信するデリゲートを受け取り、`options`オブジェクト。 [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)そのオブジェクトのプロパティは、JSON.NET`JsonSerializerSettings`引数のシリアル化の構成し、戻り値を使用できるオブジェクト。 参照してください、 [JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)の詳細。

たとえば、「キャメル ケース」の既定名ではなく"PascalCase"プロパティの名前を使用するシリアライザーを構成する次のコードを使用します。

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

.NET クライアントで同じ`AddJsonProtocol`に拡張メソッドが存在する[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)します。 `Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するのには名前空間をインポートする必要があります。

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
> この時点で、JavaScript クライアント内で JSON のシリアル化を構成することはできません。

### <a name="messagepack-serialization-options"></a>MessagePack シリアル化オプション

デリゲートを提供することで MessagePack シリアル化を構成することができます、 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出します。 参照してください[SignalR で MessagePack](xref:signalr/messagepackhubprotocol)の詳細。

> [!NOTE]
> この時点で、JavaScript クライアント内で MessagePack シリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバー オプションを構成します。

次の表では、SignalR ハブを構成するためのオプションについて説明します。

::: moniker range=">= aspnetcore-3.0"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、クライアントを検討してください、この間隔で (キープ アライブを含む) メッセージを受信していない場合は切断されます。 実際にマーク済みであるため、これを実装する方法、切断されたクライアントの場合は、このタイムアウト間隔よりも長い時間がかかります。 推奨値は二重、`KeepAliveInterval`値。|
| `HandshakeTimeout` | 15 秒 | クライアントがこの時間間隔内で最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。 これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。 ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内でメッセージを送信していない場合、接続を開いたままに ping メッセージが自動的に送信されます。 変更するときに`KeepAliveInterval`、変更、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントに設定します。 推奨される`ServerTimeout` / `serverTimeoutInMilliseconds`値が double、`KeepAliveInterval`値。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされるプロトコル。 既定では、サーバーに登録されているプロトコルをすべて許可されますが、プロトコルは、個々 のハブに対する特定のプロトコルを無効にするには、この一覧から削除できます。 |
| `EnableDetailedErrors` | `false` | 場合`true`詳細のハブ メソッドで例外がスローされたときに、例外メッセージがクライアントに返されます。 既定値は`false`、これらの例外メッセージは、機密情報を含めることができます。 |
| `StreamBufferCapacity` | `10` | クライアントのバッファーに格納できる項目の最大数は、ストリームをアップロードします。 この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理がブロックされます。|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、クライアントを検討してください、この間隔で (キープ アライブを含む) メッセージを受信していない場合は切断されます。 実際にマーク済みであるため、これを実装する方法、切断されたクライアントの場合は、このタイムアウト間隔よりも長い時間がかかります。 推奨値は二重、`KeepAliveInterval`値。|
| `HandshakeTimeout` | 15 秒 | クライアントがこの時間間隔内で最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。 これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。 ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内でメッセージを送信していない場合、接続を開いたままに ping メッセージが自動的に送信されます。 変更するときに`KeepAliveInterval`、変更、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントに設定します。 推奨される`ServerTimeout` / `serverTimeoutInMilliseconds`値が double、`KeepAliveInterval`値。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされるプロトコル。 既定では、サーバーに登録されているプロトコルをすべて許可されますが、プロトコルは、個々 のハブに対する特定のプロトコルを無効にするには、この一覧から削除できます。 |
| `EnableDetailedErrors` | `false` | 場合`true`詳細のハブ メソッドで例外がスローされたときに、例外メッセージがクライアントに返されます。 既定値は`false`、これらの例外メッセージは、機密情報を含めることができます。 |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 秒 | クライアントがこの時間間隔内で最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。 これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。 ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内でメッセージを送信していない場合、接続を開いたままに ping メッセージが自動的に送信されます。 変更するときに`KeepAliveInterval`、変更、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントに設定します。 推奨される`ServerTimeout` / `serverTimeoutInMilliseconds`値が double、`KeepAliveInterval`値。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされるプロトコル。 既定では、サーバーに登録されているプロトコルをすべて許可されますが、プロトコルは、個々 のハブに対する特定のプロトコルを無効にするには、この一覧から削除できます。 |
| `EnableDetailedErrors` | `false` | 場合`true`詳細のハブ メソッドで例外がスローされたときに、例外メッセージがクライアントに返されます。 既定値は`false`、これらの例外メッセージは、機密情報を含めることができます。 |

::: moniker-end

オプションのデリゲートを提供することですべてのハブのオプションを構成でき、`AddSignalR`呼び出す`Startup.ConfigureServices`します。

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

1 つのハブのオプションで提供されるグローバル オプションを上書き`AddSignalR`を使用して構成できると<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高度な HTTP 構成オプション

使用`HttpConnectionDispatcherOptions`トランスポートおよびメモリ バッファーの管理に関連する高度な設定を構成します。 これらのオプションを構成するデリゲートを渡すことによって[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)で`Startup.Configure`します。

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

次の表では、ASP.NET Core SignalR の高度な HTTP オプションを構成するためのオプションについて説明します。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | クライアントから受信したバイトの最大数をサーバーのバッファー。 この値を増やすことによりより大きなメッセージを受信するサーバーが、メモリ消費量に悪影響を与えることができます。 |
| `AuthorizationData` | データが自動的に収集、`Authorize`ハブ クラスに適用される属性。 | 一連の[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトは、クライアントがハブへの接続を承認されているかどうかを判断するために使用します。 |
| `TransportMaxBufferSize` | 32 KB | アプリによって送信されたバイト数の最大数をサーバーのバッファー。 この値を増やすことによりより大きなメッセージを送信するサーバーが、メモリ消費量に悪影響を与えることができます。 |
| `Transports` | すべてのトランスポートが有効になります。 | ビットマスク`HttpTransportType`トランスポートを制限する値をクライアントが接続に使用できます。 |
| `LongPolling` | 以下を参照してください。 | 長いポーリングのトランスポートに固有の追加オプション。 |
| `WebSockets` | 以下を参照してください。 | Websocket トランスポートに固有の追加オプション。 |

ポーリングの長いトランスポートを使用して構成できるその他のオプションを持つ、`LongPolling`プロパティ。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 秒 | 1 つのポーリング要求を終了する前に、クライアントに送信するメッセージのサーバーが待機する最長時間。 新しいポーリング要求をより頻繁に発行するクライアントは、この値を小さきます。 |

WebSocket トランスポートが追加のオプションを使用して構成できる、`WebSockets`プロパティ。

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じ、クライアントがこの時間間隔内に閉じるが失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | 設定するために使用するデリゲート、`Sec-WebSocket-Protocol`ヘッダーをカスタム値。 デリゲートは、入力としてクライアントによって要求された値を受け取るし、目的の値を返します。 |

## <a name="configure-client-options"></a>クライアント オプションを構成します。

クライアント オプションを構成でき、`HubConnectionBuilder`型 (.NET、JavaScript クライアントで使用可能)。 Java クライアントで使用できますが、`HttpHubConnectionBuilder`サブクラスはでもビルダー構成オプションが含まれます、`HubConnection`自体。

### <a name="configure-logging"></a>ログを構成します。

.NET を使用してクライアントでログ記録が構成されている、`ConfigureLogging`メソッド。 サーバー上にある、同じ方法でログ記録プロバイダーおよびフィルターを登録できます。 参照してください、 [ASP.NET Core でのログ記録](xref:fundamentals/logging/index)詳細についてはドキュメントです。

> [!NOTE]
> ログ プロバイダーを登録するために必要なパッケージをインストールする必要があります。 参照してください、[組み込みのログ プロバイダー](xref:fundamentals/logging/index#built-in-logging-providers)完全な一覧については、ドキュメントのセクション。

たとえば、コンソールのログ記録を有効にするインストール、 `Microsoft.Extensions.Logging.Console` NuGet パッケージ。 呼び出す、`AddConsole`拡張メソッド。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

同様に、JavaScript クライアント内で`configureLogging`メソッドが存在します。 提供、`LogLevel`を生成するログ メッセージの最小レベルを示す値。 ブラウザーのコンソール ウィンドウには、ログが書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

代わりに、`LogLevel`値を提供することも、`string`ログのレベル名を表す値。 アクセスの必要がない SignalR 環境でのログ記録を構成するときに便利ですが、`LogLevel`定数。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

次の表では、使用可能なログ レベルを示します。 指定した値`configureLogging`設定、**最小**ログ レベルをログに記録されます。 このレベルで記録されたメッセージ**レベルがその後に、表に示すまたは**、ログに記録されます。

| string | ログ レベル |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| `"info"` **または** `"information"` | `LogLevel.Information` |
| `"warn"` **または** `"warning"` | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> 完全ログ記録を無効にするには指定`signalR.LogLevel.None`で、`configureLogging`メソッド。

ログ記録の詳細については、次を参照してください。、 [SignalR 診断のドキュメント](xref:signalr/diagnostics)します。

SignalR の Java クライアントを使用して、 [SLF4J](https://www.slf4j.org/)のログ記録ライブラリです。 ライブラリのユーザーは、特定のログ出力の依存関係に導入することで独自の特定のログ記録の実装を選択できるようにする高度なログ記録 API になります。 次のコード スニペットは、使用する方法を示します`java.util.logging`SignalR Java クライアントを使用します。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係でのログ記録を構成しない場合、SLF4J は、次の警告メッセージが既定の操作なしロガーを読み込みます。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視しても。

### <a name="configure-allowed-transports"></a>許可されているトランスポートを構成します。

SignalR によって使用されるトランスポートで構成できる、`WithUrl`を呼び出す (`withUrl` JavaScript で)。 値のビットごとの OR`HttpTransportType`のみ指定されたトランスポートを使用するクライアントを制限するために使用できます。 既定では、すべてのトランスポートが有効にします。

たとえば、Server-Sent イベント転送を無効にすることを許可する WebSockets および長いポーリングの接続。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

設定して、JavaScript クライアントでのトランスポートが構成されている、`transport`フィールドに指定されたオプションのオブジェクトに`withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

このバージョンの Java では、クライアント websocket は、のみ使用可能なトランスポートです。

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Java クライアント トランスポートが選択されている、`withTransport`メソッドを`HttpHubConnectionBuilder`します。 Java クライアント Websocket トランスポートを使用して既定値です。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR の Java クライアントは、まだトランスポートにフォールバックをサポートしていません。

::: moniker-end

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成します。

SignalR 要求と共に認証データを提供するには、使用、`AccessTokenProvider`オプション (`accessTokenFactory` JavaScript で) 必要なアクセス トークンを返す関数を指定します。 .NET クライアントでこのアクセス トークンとして渡される HTTP「ベアラー認証」トークン (を使用して、`Authorization`ヘッダーの型を持つ`Bearer`)。 アクセス トークンをベアラー トークンとして使用、JavaScript クライアントで**を除く**ブラウザー Api が (具体的には、Server-Sent イベントと Websocket の要求) のヘッダーを適用する機能を制限するような場合。 このような場合は、アクセス トークンはクエリ文字列値として指定`access_token`します。

.NET クライアントで、`AccessTokenProvider`でオプションのデリゲートを使用してオプションを指定することができます`WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

設定して、JavaScript クライアントでアクセス トークンが構成されている、`accessTokenFactory`フィールド内のオプション オブジェクトに`withUrl`:

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

アクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成する、SignalR の Java クライアントで、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)します。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を提供する、 [RxJava](https://github.com/ReactiveX/RxJava) [単一\<文字列 >](https://reactivex.io/documentation/single.html)します。 呼び出して[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)クライアントのアクセス トークンを生成するロジックを記述することができます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープ アライブ オプションを構成します。

タイムアウトとキープ アライブ動作を構成するための追加のオプションがあります、`HubConnection`オブジェクト自体。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーは、この間隔でメッセージを送信していない、クライアントと見なされ、サーバーが切断されているトリガー、`Closed`イベント (`onclose` JavaScript で)。 この値は、サーバーから送信される ping メッセージに十分な大きさである必要があります**と**タイムアウト間隔内に、クライアントによって受信されます。 推奨値は数値、サーバーに少なくとも 2 倍の`KeepAliveInterval`の ping が到着するまでの値。 |
| `HandshakeTimeout` | 15 秒 | サーバーの初期ハンドシェイクのタイムアウト。 サーバーは、この間隔でハンドシェイクの応答を送信しない場合、は、クライアントがキャンセル ハンドシェイクとトリガー、`Closed`イベント (`onclose` JavaScript で)。 これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。 ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。 |

として、.NET クライアントでタイムアウト値が指定された`TimeSpan`値。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒 (30,000 ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーは、この間隔でメッセージを送信していない、クライアントと見なされ、サーバーが切断されているトリガー、`onclose`イベント。 この値は、サーバーから送信される ping メッセージに十分な大きさである必要があります**と**タイムアウト間隔内に、クライアントによって受信されます。 推奨値は数値、サーバーに少なくとも 2 倍の`KeepAliveInterval`の ping が到着するまでの値。 |

# <a name="javatabjava"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ----------- | ------------- | ----------- |
|`getServerTimeout` `setServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーは、この間隔でメッセージを送信していない、クライアントと見なされ、サーバーが切断されているトリガー、`onClose`イベント。 この値は、サーバーから送信される ping メッセージに十分な大きさである必要があります**と**タイムアウト間隔内に、クライアントによって受信されます。 推奨値は数値、サーバーに少なくとも 2 倍の`KeepAliveInterval`の ping が到着するまでの値。 |
| `withHandshakeResponseTimeout` | 15 秒 | サーバーの初期ハンドシェイクのタイムアウト。 サーバーは、この間隔でハンドシェイクの応答を送信しない場合、は、クライアントがキャンセル ハンドシェイクとトリガー、`onClose`イベント。 これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。 ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。 |

---

### <a name="configure-additional-options"></a>追加のオプションを構成します。

追加のオプションを構成することができます、 `WithUrl` (`withUrl` JavaScript で) メソッドを`HubConnectionBuilder`やさまざまな構成 Api を上、 `HttpHubConnectionBuilder` Java クライアント。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | 説明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供されている文字列を返す関数。 |
| `SkipNegotiation` | `false` | これを設定`true`ネゴシエーションの手順をスキップします。 **Websocket トランスポートがトランスポートには、のみの有効な場合にのみサポート**します。 Azure SignalR サービスを使用する場合は、この設定を有効にすることはできません。 |
| `ClientCertificates` | Empty | 要求の認証に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | すべての HTTP 要求を送信する HTTP クッキーのコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求を送信する資格情報です。 |
| `CloseTimeout` | 5 秒 | Websocket だけです。 最長時間、クライアントは、サーバーが閉じる要求を確認するための終了後に待機します。 サーバーは、この時間内終了を確認は、クライアントが切断されます。 |
| `Headers` | Empty | すべての HTTP 要求を送信する追加の HTTP ヘッダーのマップです。 |
| `HttpMessageHandlerFactory` | `null` | 構成または置換するために使用するデリゲート、 `HttpMessageHandler` HTTP 要求を送信するために使用します。 WebSocket 接続に使用されません。 このデリゲートは、null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。 その既定値の設定を変更し、それを返すかが返さ新しい`HttpMessageHandler`インスタンス。 **ときは、ハンドラーを置き換える、指定したハンドラーから保持する設定をコピーすることを確認して、それ以外の場合、(Cookie やヘッダー) の構成オプションが新しいハンドラーを適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | HTTP と Websocket の要求の既定の資格情報を送信するこのブール値を設定します。 これにより、Windows 認証を使用できます。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用するデリゲート。 インスタンスを受け取る[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)オプションを構成できます。 |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| JavaScript のオプション | 既定値 | 説明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして提供されている文字列を返す関数。 |
| `skipNegotiation` | `false` | これを設定`true`ネゴシエーションの手順をスキップします。 **Websocket トランスポートがトランスポートには、のみの有効な場合にのみサポート**します。 Azure SignalR サービスを使用する場合は、この設定を有効にすることはできません。 |

# <a name="javatabjava"></a>[Java](#tab/java)

| Java オプション | 既定値 | 説明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供されている文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | これを設定`true`ネゴシエーションの手順をスキップします。 **Websocket トランスポートがトランスポートには、のみの有効な場合にのみサポート**します。 Azure SignalR サービスを使用する場合は、この設定を有効にすることはできません。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求を送信する追加の HTTP ヘッダーのマップです。 |

---

指定されたオプションのデリゲートで、.NET クライアントでこれらのオプションを変更できます`WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

指定された JavaScript オブジェクトで、JavaScript クライアントでこれらのオプションを指定できる`withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでこれらのオプションで構成できますメソッドを使って、`HttpHubConnectionBuilder`から返される、 `HubConnectionBuilder.create("HUB URL")`

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
