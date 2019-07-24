---
title: ASP.NET Core SignalR JavaScript クライアント
author: bradygaster
description: ASP.NET Core SignalR JavaScript クライアントの概要です。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/28/2019
uid: signalr/javascript-client
ms.openlocfilehash: f314e1fe0ef0ea73a28b332404a09f2956524132
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412382"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript クライアント

作成者: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript クライアント ライブラリでは、サーバー側ハブのコードを呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-signalr-client-package"></a>SignalR クライアント パッケージをインストールします。

SignalR JavaScript クライアント ライブラリとして提供される、 [npm](https://www.npmjs.com/)パッケージ。 Visual Studio を使用している場合は、実行`npm install`から、**パッケージ マネージャー コンソール**ルート フォルダー内で。 Visual Studio Code でからのコマンドを実行、**統合ターミナル**します。

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

## <a name="use-the-signalr-javascript-client"></a>SignalR JavaScript クライアントを使用します。

SignalR JavaScript クライアントでの参照、`<script>`要素。

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

メソッド`invoke`は、JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)を返します。 は`Promise` 、サーバーのメソッドがを返すときに戻り値 (存在する場合) を使用して解決されます。 サーバー上のメソッドがエラー `Promise`をスローした場合、はエラーメッセージと共に拒否されます。 これらのケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。`then`

メソッド`send`は、JavaScript `Promise`を返します。 は`Promise` 、メッセージがサーバーに送信されたときに解決されます。 メッセージの送信中にエラーが発生した`Promise`場合、はエラーメッセージと共に拒否されます。 これらのケース`catch` (または`Promise` `await`構文) を処理するには、自身でメソッドとメソッドを使用します。`then`

> [!NOTE]
> を`send`使用すると、サーバーがメッセージを受信するまで待機しません。 このため、サーバーからデータまたはエラーを返すことはできません。

## <a name="call-client-methods-from-hub"></a>ハブからのクライアント メソッドを呼び出す

ハブからメッセージを受信する定義を使用して、メソッド、[で](/javascript/api/%40aspnet/signalr/hubconnection#on)のメソッド、`HubConnection`します。

* JavaScript クライアント メソッドの名前。 次の例では、メソッド名は`ReceiveMessage`します。
* 引数は、hub は、メソッドに渡します。 引数の値は、次の例では、`message`します。

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

上記のコードで`connection.on`を使用してサーバー側コードから呼び出すときに実行される、 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync)メソッド。

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR を呼び出すメソッド名を照合することによってクライアントの方法を指定してで定義されている引数`SendAsync`と`connection.on`します。

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

SignalR の JavaScript クライアントは、 `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)のメソッドを使用して自動的に再接続するように構成できます。 既定では、自動的に再接続されません。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

パラメーターを指定し`withAutomaticReconnect()`ない場合、は、再接続を試行する前に、それぞれ0、2、10、および30秒間待機するようにクライアントを構成します。これにより、4回の試行が失敗すると停止します。

`HubConnection`再接続`onreconnecting` `HubConnectionState.Reconnecting` の試行`Disconnected`を開始する前に、は状態に遷移し、コールバックを起動します。状態に遷移し、次のようにコールバックをトリガーするのではなく、`onclose` `HubConnection`自動再接続が構成されていません。 これにより、接続が失われたことをユーザーに警告し、UI 要素を無効にすることができます。

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

最初の4回の試行でクライアントが正常に再`HubConnection`接続した場合、 `Connected`は状態に戻り`onreconnected` 、コールバックを起動します。 これにより、接続が再確立されたことをユーザーに通知することができます。

接続はサーバーにまったく新しいものであるため、 `connectionId` `onreconnected`コールバックに新しいが提供されます。

> [!WARNING]
> が`onreconnected` [ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)するように構成さ`HubConnection`れている場合、コールバックの`connectionId`パラメーターは未定義になります。

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`最初の開始`HubConnection`エラーを再試行するようにを構成しません。そのため、開始エラーは手動で処理する必要があります。

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

最初の4回の試行でクライアントが正常に再接続`HubConnection`されない場合`Disconnected` 、は状態に遷移し、その[閉じる](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバックを起動します。 これにより、接続が完全に失われたことをユーザーに通知し、ページを最新の情報に更新することをお勧めします。

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

再接続のタイミングを切断または変更する前に、カスタムの再接続試行`withAutomaticReconnect`回数を構成するために、では、各再接続の試行を開始するまでの待機時間 (ミリ秒) を表す数値の配列を受け取ります。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

前の例では`HubConnection` 、接続が失われた直後に再接続を開始するようにを構成しています。 これは、既定の構成にも当てはまります。

最初の再接続の試行が失敗した場合、2回目の再接続試行も、既定の構成のように2秒間待機するのではなく、直ちに開始されます。

2回目の再接続の試行が失敗した場合、3回目の再接続は10秒後に開始され、既定の構成のようになります。

このカスタム動作は、既定の構成のように、別の30秒でもう一度再接続を試行するのではなく、3回目の再接続試行の失敗後に停止することで、既定の動作から再び逸脱します。

タイミングと自動再接続試行回数をさらに細かく制御する場合は、 `withAutomaticReconnect` `IRetryPolicy`インターフェイスを実装するオブジェクトを受け取ります。このインターフェイスには`nextRetryDelayInMilliseconds`、という名前の1つのメソッドがあります。

`nextRetryDelayInMilliseconds`型`RetryContext`の1つの引数を受け取ります。 `retryReason` `elapsedMilliseconds` `number` `previousRetryCount` `number` には`Error` 、、、およびの3つのプロパティがあります。`RetryContext` 最初の再接続を試行する`previousRetryCount`前`elapsedMilliseconds`に、との`retryReason`両方がゼロになり、は接続が失われる原因となったエラーになります。 再試行が再試行されるたび`previousRetryCount`に、が1ずつインクリメント`elapsedMilliseconds`され、これまでの再接続にかかった時間 (ミリ秒単位) を`retryReason`反映して更新されます。また、は、最後に再接続を試みたときに発生したエラーになります。オーバー.

`nextRetryDelayInMilliseconds`は、次回の再接続が試行される前に待機するミリ秒数を`null`表す数値`HubConnection`を返すか、再接続を停止する必要がある場合はを返します。

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
        })
    .build();
```

または、[手動で再接続](#manually-reconnect)する方法で示すように、手動でクライアントを再接続するコードを記述することもできます。

::: moniker-end

### <a name="manually-reconnect"></a>手動で再接続する

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3\.0 より前では、SignalR の JavaScript クライアントは自動的に再接続しません。 クライアントを手動で再接続するコードを記述する必要があります。

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
* [Azure SignalR Service サーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
