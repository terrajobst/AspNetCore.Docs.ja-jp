---
title: ASP.NET Core SignalR JavaScript クライアント
author: bradygaster
description: ASP.NET Core SignalR JavaScript クライアントの概要。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: eaf737642cdbd7ab2b1b5c16538b47a70cddd332
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354698"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET Core SignalR JavaScript クライアント

作成者: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript クライアントライブラリを使用すると、開発者はサーバー側のハブコードを呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-opno-locsignalr-client-package"></a>SignalR クライアントパッケージをインストールする

SignalR JavaScript クライアントライブラリは[npm](https://www.npmjs.com/)パッケージとして配信されます。 Visual Studio を使用している場合は、実行`npm install`から、**パッケージ マネージャー コンソール**ルート フォルダー内で。 Visual Studio Code でからのコマンドを実行、**統合ターミナル**します。

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

npm のインストール パッケージの内容を *node_modules\\@microsoft\signalr\dist\browser* フォルダー。 という名前の新しいフォルダーを作成する*signalr*下、 *wwwroot\\lib*フォルダー。 コピー、 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダー。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm のインストール パッケージの内容を *node_modules\\@aspnet\signalr\dist\browser* フォルダー。 という名前の新しいフォルダーを作成する*signalr*下、 *wwwroot\\lib*フォルダー。 コピー、 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダー。

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>SignalR JavaScript クライアントを使用する

`<script>` 要素で SignalR JavaScript クライアントを参照します。

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>ハブへの接続します。

次のコードでは、作成し、接続を開始します。 ハブの名前は大文字小文字を区別します。

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>クロス オリジンの接続

通常、ブラウザーでは、要求されたページと同じドメインから接続を読み込みます。 ただし、状況が別のドメインへの接続が必要な場合があります。

悪意のあるサイトが別のサイトから機密データを読み取ることを防ぐために[クロス オリジン接続](xref:security/cors)は既定で無効になります。 クロス オリジン要求を許可することで有効にする、`Startup`クラス。

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>クライアントからのハブ メソッドの呼び出し

JavaScript クライアントは、ハブ経由でのパブリック メソッドを呼び出して、[呼び出す](/javascript/api/%40aspnet/signalr/hubconnection#invoke)のメソッド、 [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection)します。 `invoke`メソッドは 2 つの引数を受け取ります。

* ハブ メソッドの名前。 次の例では、ハブのメソッド名は`SendMessage`します。
* ハブ メソッドで定義されている引数。 次の例では引数名は`message`します。 このコード例では、Internet Explorer を除くすべての主要なブラウザーの現在のバージョンでサポートされている、矢印関数の構文を使用します。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> *サーバーレスモード*で Azure SignalR サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。 詳細については、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。

`invoke` メソッドは、JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。 `Promise` は、サーバーのメソッドがを返すときに戻り値 (存在する場合) で解決されます。 サーバー上のメソッドがエラーをスローした場合、`Promise` はエラーメッセージと共に拒否されます。 これらのケース (または `await` 構文) を処理するには、`Promise` 自体で `then` および `catch` メソッドを使用します。

`send` メソッドは、JavaScript `Promise`を返します。 `Promise` は、メッセージがサーバーに送信されたときに解決されます。 メッセージの送信中にエラーが発生した場合、`Promise` はエラーメッセージと共に拒否されます。 これらのケース (または `await` 構文) を処理するには、`Promise` 自体で `then` および `catch` メソッドを使用します。

> [!NOTE]
> `send` を使用すると、サーバーがメッセージを受信するまで待機しません。 このため、サーバーからデータまたはエラーを返すことはできません。

## <a name="call-client-methods-from-hub"></a>ハブからのクライアント メソッドを呼び出す

ハブからメッセージを受信する定義を使用して、メソッド、[で](/javascript/api/%40aspnet/signalr/hubconnection#on)のメソッド、`HubConnection`します。

* JavaScript クライアント メソッドの名前。 次の例では、メソッド名は`ReceiveMessage`します。
* 引数は、hub は、メソッドに渡します。 引数の値は、次の例では、`message`します。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

上記のコードで`connection.on`を使用してサーバー側コードから呼び出すときに実行される、 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッド。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR は、`SendAsync` と `connection.on`で定義されたメソッド名と引数を照合することによって、どのクライアントメソッドを呼び出すかを決定します。

> [!NOTE]
> ベスト プラクティスを呼び出して、[開始](/javascript/api/%40aspnet/signalr/hubconnection#start)メソッドを`HubConnection`後`on`します。 これにより、すべてのメッセージを受信する前に、ハンドラーが登録されます。

## <a name="error-handling-and-logging"></a>エラー処理とログ記録

チェーンを`catch`メソッドの末尾に、`start`クライアント側のエラーを処理するメソッド。 使用`console.error`ブラウザーのコンソールにエラーを出力します。

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

接続が行われたときにログに記録するには、logger とイベントの種類を渡すことによって、クライアント側のログ トレースをセットアップします。 指定されたログ レベル以降、メッセージが記録されます。 使用可能なログ レベルは次のとおりです。

* `signalR.LogLevel.Error` &ndash; エラー メッセージ。 ログ`Error`メッセージのみです。
* `signalR.LogLevel.Warning` &ndash; 可能性のあるエラーについての警告メッセージ。 ログ`Warning`、および`Error`メッセージ。
* `signalR.LogLevel.Information` &ndash; ステータス メッセージがエラーなし。 ログ`Information`、 `Warning`、および`Error`メッセージ。
* `signalR.LogLevel.Trace` &ndash; メッセージをトレースします。 ハブとクライアント間で転送されるデータを含め、すべてログに記録します。

使用して、 [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging)メソッド[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)ログ レベルを構成します。 メッセージは、ブラウザーのコンソールに記録されます。

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>クライアントを再接続します。

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>自動的に再接続する

SignalR の JavaScript クライアントは、 [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)の `withAutomaticReconnect` メソッドを使用して自動的に再接続するように構成できます。 既定では、自動的に再接続されません。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

パラメーターを指定しない場合、`withAutomaticReconnect()` は、再接続を試行する前にそれぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。

再接続を試行する前に、`HubConnection` は `HubConnectionState.Reconnecting` 状態に遷移し、`onreconnecting` コールバックを起動します。これは、`Disconnected` 状態に遷移し、自動再接続が構成されていない `onclose` のような `HubConnection` コールバックをトリガーします。 これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

最初の4回の試行でクライアントが正常に再接続した場合、`HubConnection` は `Connected` 状態に戻り、`onreconnected` コールバックを起動します。 これにより、接続が再確立されたことをユーザーに通知することができます。

接続はサーバーにまったく新しいものであるため、`onreconnected` コールバックに新しい `connectionId` が提供されます。

> [!WARNING]
> `HubConnection` が[ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成されている場合、`onreconnected` コールバックの `connectionId` パラメーターは未定義になります。

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` は、最初の開始エラーを再試行するように `HubConnection` を構成しません。そのため、開始エラーは手動で処理する必要があります。

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

最初の4回の試行でクライアントが正常に再接続されなかった場合、`HubConnection` は `Disconnected` 状態に遷移し、その[閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックが発生します。 これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

再接続のタイミングを切断または変更する前に、カスタムの再接続試行回数を構成するために、`withAutomaticReconnect` は、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け入れます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前の例では、接続が失われた直後に再接続を開始するように `HubConnection` を構成しています。 これは、既定の構成にも当てはまります。

最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。

2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。

このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。

自動再接続試行のタイミングと回数をさらに細かく制御する場合、`withAutomaticReconnect` は、`nextRetryDelayInMilliseconds`という名前の1つのメソッドを持つ `IRetryPolicy` インターフェイスを実装するオブジェクトを受け入れます。

`nextRetryDelayInMilliseconds` は、`RetryContext`型の1つの引数を受け取ります。 `RetryContext` には、`previousRetryCount`、`elapsedMilliseconds` と `retryReason` という3つのプロパティがあります。これは、それぞれ `number`、`number`、および `Error` です。 最初の再接続を試行する前に、`previousRetryCount` と `elapsedMilliseconds` の両方がゼロになり、`retryReason` は接続が失われる原因となったエラーになります。 再試行が再試行されるたびに、`previousRetryCount` が1ずつインクリメントされます。これまでの再接続にかかった時間 (ミリ秒単位) が反映されるように `elapsedMilliseconds` が更新され、最後の再接続の試行が失敗した原因となったエラーが `retryReason` ます。

`nextRetryDelayInMilliseconds` は、次の再接続を試行する前に待機するミリ秒数を表す数値を返すか、`HubConnection` の再接続を停止する必要がある場合は `null` する必要があります。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。

::: moniker-end

### <a name="manually-reconnect"></a>手動で再接続する

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3\.0 より前の SignalR の JavaScript クライアントは、自動的に再接続されません。 クライアントを手動で再接続するコードを記述する必要があります。

::: moniker-end

次のコードは、標準的な手動再接続アプローチを示しています。

1. 関数 (ここで、`start`関数)、接続を開始するが作成されます。
1. 呼び出す、`start`関数で、接続の`onclose`イベント ハンドラー。

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

実際の実装は、指数バックオフを使用して、または断念する前に指定された回数を再試行してください。

## <a name="additional-resources"></a>その他の技術情報

* [JavaScript API リファレンス](/javascript/api/?view=signalr-js-latest)
* [JavaScript のチュートリアル](xref:tutorials/signalr)
* [WebPack と TypeScript のチュートリアル](xref:tutorials/signalr-typescript-webpack)
* [ハブ](xref:signalr/hubs)
* [.NET クライアント](xref:signalr/dotnet-client)
* [Azure に発行する](xref:signalr/publish-to-azure-web-app)
* [クロス オリジン要求 (CORS)](xref:security/cors)
* [Azure SignalR サービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
