---
title: ASP.NET Core SignalR JavaScript クライアント
author: bradygaster
description: ASP.NET Core SignalR JavaScript クライアントの概要です。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: 8b645304b597db0c37fb9cd8814c493ca1c6ee62
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814963"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript クライアント

作成者: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript クライアント ライブラリでは、サーバー側ハブのコードを呼び出すことができます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-signalr-client-package"></a>SignalR クライアント パッケージをインストールします。

SignalR JavaScript クライアント ライブラリとして提供される、 [npm](https://www.npmjs.com/)パッケージ。 Visual Studio を使用している場合は、実行`npm install`から、**パッケージ マネージャー コンソール**ルート フォルダー内で。 Visual Studio Code でからのコマンドを実行、**統合ターミナル**します。

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

npm のインストール パッケージの内容を *node_modules\\@aspnet\signalr\dist\browser* フォルダー。 という名前の新しいフォルダーを作成する*signalr*下、 *wwwroot\\lib*フォルダー。 コピー、 *signalr.js*ファイルを*wwwroot\lib\signalr*フォルダー。

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
* ハブ メソッドで定義されている引数。 次の例では引数名は`message`します。 コード例では、現在のバージョンの Internet Explorer を除くすべての主要ブラウザーでサポートされている矢印関数の構文を使用します。

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Azure SignalR サービスを使用している場合*サーバーレス モード*、クライアントからハブ メソッドを呼び出すことはできません。 詳細については、次を参照してください。、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)します。

`invoke`メソッドは、JavaScript を返します[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)します。 `Promise`解決戻り値 (ある場合) サーバー上のメソッドが返されます。 サーバー上のメソッドが、エラーをスローした場合、`Promise`と、エラー メッセージは拒否されます。 使用して、`then`と`catch`メソッド、`Promise`自体と、このような場合を処理するために (または`await`構文)。

`send`メソッドは、JavaScript を返します`Promise`します。 `Promise`サーバーに、メッセージが送信されたときに解決されます。 メッセージを送信中にエラーがある場合、`Promise`と、エラー メッセージは拒否されます。 使用して、`then`と`catch`メソッド、`Promise`自体と、このような場合を処理するために (または`await`構文)。

> [!NOTE]
> 使用して`send`サーバーがメッセージを受信するまで待機しません。 その結果、サーバーからデータまたはエラーを返すことはできません。

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

### <a name="automatically-reconnect"></a>自動的に再接続します。

使用して再接続が自動的に SignalR JavaScript クライアントを構成することができます、`withAutomaticReconnect`メソッド[HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)します。 既定では、自動的に再接続しません。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

任意のパラメーターを指定せず`withAutomaticReconnect()`後、4 つの失敗した試行を停止する各再接続の試行を試す前に、それぞれ 0、2、10、および 30 秒間待機するクライアントを構成します。

再接続しようとすると、開始する前に、`HubConnection`への移行には、`HubConnectionState.Reconnecting`起動状態にあり、その`onreconnecting`コールバックへの移行ではなく、`Disconnected`状態とトリガーの`onclose`コールバックなどの`HubConnection`せず、自動再接続を構成します。 これは、UI 要素を無効にして、接続が失われたことをユーザーに警告する機会を提供します。

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

内の最初の 4 つの試行で、クライアントが再接続に成功した場合、`HubConnection`から戻るへの移行、`Connected`起動状態にあり、その`onreconnected`コールバック。 これは、接続が再確立されたユーザーに通知する機会を提供します。

接続は、サーバーから完全に新規に見えるため、新しい`connectionId`に提供される、`onreconnected`コールバック。

> [!WARNING]
> `onreconnected`コールバックの`connectionId`パラメーターは不確定になる場合、`HubConnection`するように構成された[ネゴシエーションをスキップ](xref:signalr/configuration#configure-client-options)します。

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` 構成はありません、`HubConnection`起動障害を手動で処理する必要があるために、初回起動時の障害を再試行します。

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

内の最初の 4 つの試行で、クライアントが再接続が正常に場合、`HubConnection`への移行には、`Disconnected`起動状態にあり、その[onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose)コールバック。 これは、接続が完全に失われ、ページの更新をお勧めします。 ユーザーに通知する機会を提供します。

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

切断する前に再接続試行のカスタムの数を構成または再接続のタイミングを変更するには`withAutomaticReconnect`再接続が試みられるたびに開始する前に待機するミリ秒の遅延を表す数値の配列を受け入れます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

上記の例では、`HubConnection`接続が失われた後にすぐに再接続しようとすると開始します。 またこれは、既定の構成に当てはまります。

既定の構成でこれと同じように、2 秒待機しているのではなく、最初の再接続試行が失敗すると、2 回目の再接続試行がすぐに開始もは。

2 回目の再接続の試行が失敗した場合、これは、既定の構成と同様にもう一度、10 秒後に、3 番目の再接続の試行が開始されます。

カスタム動作し、もう一度ブロックから分化既定の動作を停止して 3 番目の再接続試行のいずれかの代わりに失敗した後は別の 30 秒後、既定の構成のように試行を再接続します。

タイミングと自動の数をさらに多くのコントロールの接続試行、なら`withAutomaticReconnect`実装するオブジェクトを受け入れる、`IReconnectPolicy`インターフェイスという単一のメソッドを`nextRetryDelayInMilliseconds`します。

`nextRetryDelayInMilliseconds` 2 つの引数を受け取り`previousRetryCount`と`elapsedMilliseconds`、両方の数値であります。 最初の再接続試行する前に両方`previousRetryCount`と`elapsedMilliseconds`は 0 になります。 各障害が発生した再試行後`previousRetryCount`1 つずつ増えますと`elapsedMilliseconds`はこれまでに再接続するミリ秒単位で費やされた時間数を反映するように更新されます。

`nextRetryDelayInMilliseconds` いずれかを表す数値ミリ秒数、[次へ] の再接続の試行までの待機を返す必要がありますまたは`null`場合、`HubConnection`再接続を停止する必要があります。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: (previousRetryCount, elapsedMilliseconds) => {
            if (elapsedMilliseconds < 60000) {
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

再接続で示すように手動で、クライアント コードを記述する代わりに、[手動で再接続](#manually-reconnect)します。

::: moniker-end

### <a name="manually-reconnect"></a>手動での再接続します。

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3\.0 では、前に SignalR JavaScript クライアントは自動的に再接続しません。 クライアントを手動で再接続するコードを記述する必要があります。

::: moniker-end

次のコードでは、手動による再接続の一般的なアプローチを示します。

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
* [Azure SignalR サービスのサーバーレス ドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
