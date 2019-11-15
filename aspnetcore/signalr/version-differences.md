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
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a>ASP.NET SignalR と ASP.NET Core SignalR の違い

ASP.NET Core SignalR は、ASP.NET SignalRのクライアントまたはサーバーと互換性がありません。 この記事では ASP.NET Core SignalRで削除または変更された機能について詳しく説明します。

## <a name="how-to-identify-the-opno-locsignalr-version"></a>SignalR のバージョンを特定する方法

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| サーバー NuGet パッケージ | [Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| クライアント NuGet パッケージ | [Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| クライアントの npm パッケージ | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Java クライアント | [GitHub リポジトリ](https://github.com/SignalR/java-client)(非推奨)  | Maven パッケージ[com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| サーバーアプリの種類 | ASP.NET (System.web) または OWIN 自己ホスト | ASP.NET Core |
| サポートされているサーバープラットフォーム | .NET Framework 4.5 以降 | .NET Framework 4.6.1 以降<br>.NET Core 2.1 以降 |

## <a name="feature-differences"></a>機能の違い

### <a name="automatic-reconnects"></a>自動再接続

自動再接続は、ASP.NET Core SignalRではサポートされていません。 クライアントが切断されている場合、ユーザーは再接続するときに、新しい接続を明示的に開始する必要があります。 ASP.NET SignalRでは、接続が切断された場合、SignalR サーバーへの再接続を試みます。

### <a name="protocol-support"></a>プロトコルのサポート

ASP.NET Core SignalR は、 [MessagePack](xref:signalr/messagepackhubprotocol) に基づく新しいバイナリプロトコルだけでなく、JSON もサポートしています。 さらに、カスタムプロトコルを作成することもできます。

### <a name="transports"></a>トランスポート

ASP.NET Core SignalR では、Forever Frame トランスポートはサポートされていません。

## <a name="differences-on-the-server"></a>サーバーの相違点

ASP.NET Core SignalR のサーバー側ライブラリは、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)パッケージに含まれています。このパッケージは、Razor プロジェクトと MVC プロジェクトの両方の**ASP.NET Core Web アプリケーション**テンプレートの一部です。

ASP.NET Core SignalR は ASP.NET Core ミドルウェアであるため、`Startup.ConfigureServices` 内で [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) を呼び出すことによって構成する必要があります。

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

ルーティングを構成するには、 `Startup.Configure`メソッドの [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) メソッド呼び出し内でハブにルートをマップします。


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ルーティングを構成するには、 `Startup.Configure`メソッドの [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) メソッド呼び出し内でハブにルートをマップします。

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>固定セッション

ASP.NET SignalR のスケールアウトモデルを使用すると、クライアントは再接続し、ファーム内の任意のサーバーにメッセージを送信できます。 ASP.NET Core SignalR では、クライアントは接続中に同じサーバーと通信する必要があります。 Redis を使用したスケールアウトの場合、これは固定セッションが必要であることを意味します。 [Azure SignalR Service](/azure/azure-signalr/)を使用したスケールアウトでは、サービスがクライアントへの接続を処理するため、固定セッションは必要ありません。

### <a name="single-hub-per-connection"></a>接続ごとに1つのハブ

ASP.NET Core SignalRでは、接続モデルが単純化されています。 複数のハブへのアクセスを共有するために使用される単一の接続ではなく、1つのハブに直接接続されます。

### <a name="streaming"></a>ストリーム

ASP.NET Core SignalR では、ハブからクライアントへの[データのストリーミング](xref:signalr/streaming)がサポートされるようになりました。

### <a name="state"></a>状態

クライアントとハブ (多くの場合 HubState と呼ばれます) の間で任意の状態を渡す機能と進行状況メッセージのサポートは削除されました。 現時点では、対応するハブプロキシはありません。

### <a name="persistentconnection-removal"></a>PersistentConnection の削除

ASP.NET Core SignalRでは、 [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118))クラスは削除されています。

### <a name="globalhost"></a>GlobalHost

ASP.NET Core には、フレームワークに依存関係の挿入 (DI) が組み込まれています。 サービスは DI を使用して [HubContext](xref:signalr/hubcontext) にアクセスできます。 ASP.NET SignalR で`HubContext`を取得するために使用される `GlobalHost` オブジェクトは、ASP.NET Core SignalR に存在しません。

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR は`HubPipeline`モジュールをサポートしていません。

## <a name="differences-on-the-client"></a>クライアントの相違点

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR クライアントは[TypeScript](https://www.typescriptlang.org/) で記述されています。 [JavaScript クライアント](xref:signalr/javascript-client)を使用する場合は、JavaScript または TypeScript で記述できます。

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>JavaScript クライアントは[npm](https://www.npmjs.com/)でホストされます。

以前のバージョンでは、JavaScript クライアントは Visual Studio の NuGet パッケージを通じて取得されました。 コアバージョン [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) では、npm パッケージに JavaScript ライブラリが含まれています。 このパッケージは、 **ASP.NET Core Web アプリケーション** テンプレートには含まれていません。 `@aspnet/signalr` npm パッケージを取得してインストールするには、npm を使用します。

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

jQuery への依存関係は削除されましたが、プロジェクトは引き続き jQuery を使用できます。

### <a name="internet-explorer-support"></a>Internet Explorer のサポート

ASP.NET Core SignalR には、Microsoft Internet Explorer 11 以降 (ASP.NET SignalR サポートされている Microsoft Internet Explorer 8 以降が必要です) が必要です。

### <a name="javascript-client-method-syntax"></a>JavaScript クライアントメソッドの構文

JavaScript の構文は、以前のバージョンの SignalRから変更されています。 `$connection` オブジェクトを使用するのではなく、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API を使用して接続を作成します。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

[on](/javascript/api/@aspnet/signalr/HubConnection#on) メソッドを使用して、ハブが呼び出すことができるクライアントメソッドを指定します。

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

クライアントメソッドを作成したら、ハブ接続を開始します。 [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) メソッドをチェーンして、エラーをログに記録または処理します。

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>ハブプロキシ

ハブプロキシが自動的に生成されなくなりました。 代わりに、メソッド名が文字列として[invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API に渡されます。

### <a name="net-and-other-clients"></a>.NET とその他のクライアント

`Microsoft.AspNetCore.SignalR.Client` NuGet パッケージには ASP.NET Core SignalR 用の .NET クライアントライブラリが含まれています。

[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)を使用して、ハブへの接続のインスタンスを作成し、構築します。

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>スケールアウトの相違点

ASP.NET SignalR は SQL Server と Redis をサポートしています。 ASP.NET Core SignalR は、Azure SignalR Service と Redis をサポートしています。

### <a name="aspnet"></a>ASP.NET

* [Azure Service Bus による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [Redis による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SQL Server による SignalR のスケールアウト](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure SignalR サービス](/azure/azure-signalr/)
* [Redis バックプレーン](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>その他の技術情報

* [ハブ](xref:signalr/hubs)
* [JavaScript クライアント](xref:signalr/javascript-client)
* [.NET クライアント](xref:signalr/dotnet-client)
* [サポートされているプラットフォーム](xref:signalr/supported-platforms)
